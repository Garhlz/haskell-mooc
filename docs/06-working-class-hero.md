# 第 6 讲：类型类英雄

我们已经在类型中见过像 `Eq a =>` 这样的类型类约束。我们知道如何把现有类型类用于现有类型。但如何把现有类型类用于我们自己的类型？又如何定义自己的类型类？

下面展示如何让你自己的类型成为 `Eq` 类型类的实例：

```haskell
data Color = Black | White

instance Eq Color where
  Black == Black  = True
  White == White  = True
  _     == _      = False
```

类型类实例是一个 `instance` 块，其中包含该类型类中函数的定义。这里我们定义了 `==` 在 `Color` 上如何工作。


## 6.1 类和实例的语法

类型类使用 `class` 语法定义。类型类中的函数会给出相应的类型签名。下面是一个包含函数 `size` 的类型类 `Size`：

```haskell
class Size a where
  size :: a -> Int
```

类型类的实例使用刚才看到的 `instance` 语法定义。下面展示如何让 `Int` 和 `[a]` 成为 `Size` 类型类的实例：

```haskell
instance Size Int where
  size x = abs x

instance Size [a] where
  size xs = length xs
```

我们的类型类 `Size` 的行为就像现有的类型类一样。我们可以在任何可以使用函数的地方使用 `size`，并且 Haskell 可以为我们推断具有 `Size` 约束的类型：

```haskell
Prelude> :t size
size :: Size a => a -> Int
Prelude> size [True,False]
2
Prelude> sizeBoth a b = [size a, size b]
Prelude> :t sizeBoth
sizeBoth :: (Size a1, Size a2) => a1 -> a2 -> [Int]
```

一个类型类可以包含多个函数，甚至可以包含常量。这里我们定义一个内容更多的新版 `Size` 类型类。

```haskell
class Size a where
  empty :: a
  size :: a -> Int
  sameSize :: a -> a -> Bool

instance Size (Maybe a) where
  empty = Nothing

  size Nothing = 0
  size (Just a) = 1

  sameSize x y = size x == size y

instance Size [a] where
  empty = []
  size xs = length xs
  sameSize x y = size x == size y
```


## 6.2 默认实现

你是否注意到，在前面的示例中，我们在两个实例里为 `sameSize` 提供了相同的定义？这很常见，因此 Haskell 类型类可以有*默认实现*。先看一个 `Example` 类型类，它用于给出某个类型的示例值。

```haskell
class Example a where
  example :: a           -- 类型 `a` 的主要示例
  examples :: [a]        -- 一个简短的示例列表
  examples = [example]   -- ... 默认只包含主要示例

instance Example Int where
  example = 1
  examples = [0,1,2]

instance Example Bool where
  example = True
```

下面是 `Example` 的工作原理。请注意 `examples` 的默认实现如何在 `Bool` 情况下使用，但在 `Int` 情况下不使用。另请注意，需要显式类型签名来告诉 GHCi 我们对哪个实例感兴趣。如果没有它们，我们将收到“不明确的类型变量”错误。

```haskell
Prelude> example :: Bool
True
Prelude> example :: Int
1
Prelude> examples :: [Bool]
[True]
Prelude> examples :: [Int]
[0,1,2]
```

标准类型类大量使用默认实现来简化实例定义。下面是 `Eq` 的标准定义（为了便于阅读而格式化）。

```haskell
class Eq a where
  (==) ::  a -> a -> Bool
  x == y  = not (x /= y)

  (/=) ::  a -> a -> Bool
  x /= y  = not (x == y)
```

请注意这两个操作如何互相作为默认实现。这意味着我们可以定义一个完全没有内容的 `Eq` 实例，但生成的函数将永远递归。在实践中，我们希望至少定义 `==` 和 `/=` 之一。

当有很多默认实现时，可能很难知道你需要自己实现哪些函数。因此，类型类文档通常会提到“最小完整定义”。对于 `Eq`，[文档说](https://hackage.haskell.org/package/base-4.16.4.0/docs/Prelude.html#t:Eq) “最小完整定义：== 或 /=。”

接下来看看 `Ord`。`Ord` 有 7 个操作，它们彼此之间都有默认实现。顺便说一下，请注意这种一次性定义多个类型签名的写法。没关系，这是 Haskell 的一个特性，`Ord` 在[标准](https://www.haskell.org/onlinereport/haskell2010/haskellch6.html#x13-1270006.3)中就是这样定义的。（我们很快就会回到 `(Eq a) =>` 这部分的含义。）

```haskell
class  (Eq a) => Ord a  where
  compare              :: a -> a -> Ordering
  (<), (<=), (>=), (>) :: a -> a -> Bool
  max, min             :: a -> a -> a

  compare x y | x == y    = EQ
              | x <= y    = LT
              | otherwise = GT

  x <= y  = compare x y /= GT
  x <  y  = compare x y == LT
  x >= y  = compare x y /= LT
  x >  y  = compare x y == GT

  max x y | x <= y    =  y
          | otherwise =  x
  min x y | x <= y    =  x
          | otherwise =  y
```

有了这个定义，真的很难知道最小完整定义是什么。幸运的是 [文档告诉我们](https://hackage.haskell.org/package/base-4.16.4.0/docs/Prelude.html#t:Ord) “最小完整定义：比较或 \<=。”

关于默认实现，最后补充一点：如果不需要重写默认定义，则为了简单起见，可以将该函数移出类。考虑下面类似 `Combine` 的类：

```haskell
class Combine a where
  combine :: a -> a -> a
  combine3 :: a -> a -> a -> a
  combine3 x y z = combine x (combine y z)
```

很难想象 `combine3` 会被赋予任何其他定义的情况，所以为什么不将它移出类：

```haskell
class Combine a where
  combine :: a -> a -> a

combine3 :: Combine a => a -> a -> a -> a
combine3 x y z = combine x (combine y z)
```

作为示例，下面是简单 pair 类型的 `Eq` 和 `Ord` 实例。请注意该定义如何通过仅定义 `==` 和 `<=` 来使用最小完整定义规则。

```haskell
data IntPair = IntPair Int Int
  deriving Show

instance Eq IntPair where
  IntPair a1 a2 == IntPair b1 b2  =  a1==b1 && a2==b2

instance Ord IntPair where
  IntPair a1 a2 <= IntPair b1 b2
     | a1<b1     = True
     | a1>b1     = False
     | otherwise = a2<=b2
```

```haskell
*Main> (IntPair 1 2) < (IntPair 2 3)
True
*Main> (IntPair 1 2) > (IntPair 2 3)
False
*Main> compare (IntPair 1 2) (IntPair 2 3)
LT
*Main Data.List> sort [IntPair 1 1,IntPair 1 4,IntPair 2 1,IntPair 2 2]
[IntPair 1 1,IntPair 1 4,IntPair 2 1,IntPair 2 2]
```


## 6.3 实用技巧

### 6.3.1 推导

正如我们已经多次看到的，`deriving` 是一种获得自动生成类型类实例的方法。`Read` 和 `Show` 类型类几乎总是值得推导，以获得标准行为。`Eq` 的推导实例通常也正是你想要的：它要求构造函数和字段都匹配。

推导的 `Ord` 实例可能不是你想要的。它从左到右对构造函数进行排序，然后从左到右比较构造函数内的字段。一个示例：

```haskell
data Person = Dead | Alive String Int
  deriving (Show, Eq, Ord)
```

```haskell
Prelude> Dead < Alive "Bob" 35                   -- constructors are ordered left-to-right
True
Prelude> Alive "Barbara" 35 < Alive "Clive" 17   -- names are compared before ages
True
Prelude> Alive "Clive" 17 < Alive "Clive" 30     -- 最后，如果姓名相同，则比较年龄
True
```

### 6.3.2 向 GHCi 询问类型类的信息

你可以在 GHCi 中使用 `:info` 命令来获取类型类的内容和实例。如今，该信息甚至包括最小完整定义（请参阅 MINIMAL pragma）。例如：

```haskell
Prelude> :info Num
class Num a where
  (+) :: a -> a -> a
  (-) :: a -> a -> a
  (*) :: a -> a -> a
  negate :: a -> a
  abs :: a -> a
  signum :: a -> a
  fromInteger :: Integer -> a
  {-# MINIMAL (+), (*), abs, signum, fromInteger, (negate | (-)) #-}
    -- 定义于 ‘GHC.Num’
instance Num Word -- 定义于 ‘GHC.Num’
instance Num Integer -- 定义于 ‘GHC.Num’
instance Num Int -- 定义于 ‘GHC.Num’
instance Num Float -- 定义于 ‘GHC.Float’
instance Num Double -- 定义于 ‘GHC.Float’
```


## 6.4 类型类层级

类和实例都可以形成*层次结构*。这意味着一个类或实例依赖于另一个类或实例。

### 6.4.1 实例层次结构

让我们尝试为简单的 pair 类型定义一个 `Eq` 实例：

```haskell
data Pair a = MakePair a a
  deriving Show

instance Eq (Pair a) where
  (MakePair x y) == (MakePair a b)   =   x==a && y==b
```

```haskell
error:
    • No instance for (Eq a) arising from a use of ‘==’
      Possible fix: add (Eq a) to the context of the instance declaration
    • In the first argument of ‘(&&)’, namely ‘x == a’
      In the expression: x == a && y == b
      In an equation for ‘==’:
          (MakePair x y) == (MakePair a b) = x == a && y == b
```

编译器试图告诉我们，我们的 `Eq (Pair a)` 实例需要 `Eq a` 实例才能工作。如果我们无法比较 `a` 类型的值，我们如何比较 `a` 类型的值对？为了解决这个问题，我们需要在实例声明中添加类型约束，就像我们在函数定义中添加类型约束一样。

```haskell
instance Eq a => Eq (Pair a) where
  (MakePair x y) == (MakePair a b)   =   x==a && y==b
```

现在，只要元素类型可比较，我们就可以比较 pair。不过，我们无法比较函数 pair，因为函数没有 `Eq` 实例。

```haskell
Prelude> MakePair 1 1 == MakePair 1 1
True
Prelude> MakePair reverse reverse == MakePair reverse reverse

<interactive>:15:1: error:
    • No instance for (Eq ([a0] -> [a0])) arising from a use of ‘==’
        (maybe you haven't applied a function to enough arguments?)
    • In the expression:
        MakePair reverse reverse == MakePair reverse reverse
      In an equation for ‘it’:
          it = MakePair reverse reverse == MakePair reverse reverse
```

我们继续看另一个示例。这是一个简单的类型类和一个实例：

```haskell
class Check a where
  check :: a -> Bool

instance Check Int where
  check x = x > 0
```

现在我们可以编写一个检查列表的函数。我们使用标准库函数 `and :: [Bool] -> Bool` 来检查列表是否都是 `True`。

```haskell
checkAll :: Check a => [a] -> Bool
checkAll xs = and (map check xs)
```

为了将其转换为 `Check [a]` 实例，我们需要向实例声明添加约束。我们的 `Check [a]` 是基于 `Check a` 实例的。

```haskell
instance Check a => Check [a] where
  check xs = and (map check xs)
```

这意味着我们的 `Check [a]` 实例只有在有对应的 `Check a` 实例时才有效。例如，如果我们尝试调用 `Check [Bool]` 实例，我们会收到有关缺少 `Check Bool` 实例的错误：

```haskell
Prelude> check [True,False]

<interactive>:1:1: error:
    • No instance for (Check Bool) arising from a use of ‘check’
    • In the expression: check [True, False]
      In an equation for ‘it’: it = check [True, False]
```

另外，如果我们尝试在没有约束的情况下定义 `Check [a]` 实例，我们会收到错误（有一个非常好的建议！）

```haskell
    • No instance for (Check a) arising from a use of ‘check’
      Possible fix:
        add (Check a) to the context of the instance declaration
```

如果你仔细想想，这个实例层次结构允许我们规避不能创建 `Check [Int]` 实例的限制。

最后，有时需要多重约束。例如，考虑 `Either` 的 `Eq` 实例：

```haskell
instance (Eq a, Eq b) => Eq (Either a b) where
  Left x  == Left y   =  x==y
  Right x == Right y  =  x==y
  _       == _        =  False
```

### 6.4.2 类层次结构

此外，类与类之间也可以形成依赖关系。例如，当你想在默认实现中使用另一个类的函数时，这非常有用：

```haskell
class Size a where
  size :: a -> Int

class Size a => SizeBoth a where
  sizeBoth :: a -> a -> Int
  sizeBoth x y = size x + size y
```

在这种情况下，我们说 `SizeBoth` 是 `Size` 的*子类*。注意，这里同样容易与面向对象编程中的概念混淆。标准库中的子类示例包括：

```haskell
class Eq a => Ord a where
  ...
class Num a => Fractional a where
  ...
```

理解子类的另一种方式是，如果你有 `class Main a => Sub a`，则必须提供 `instance Main MyType` 才能声明 `instance Sub MyType`。


## 6.5 测验

`Eq`类中有哪些函数？

1.  `(==), (/=)`
2.  `(==)`
3.  `(==)`, `(<)`, `(>)`

对于以下哪些类，我们可以使用 `deriving` 获得自动实例？

1.  `Num`
2.  `Ord`
3.  `Size`

以下哪个实例声明是合法的？

1.  `instance Eq Maybe`
2.  `instance Eq (a,a)`
3.  `instance Eq (Maybe Int)`
4.  `instance Eq (a,b)`

给出类 `BitOperations` 的以下定义

```haskell
class BitOperations a where
  bitNot :: a -> a
  bitNot x = bitNand x x
  bitAnd :: a -> a -> a
  bitAnd x y = bitNot (bitOr (bitNot x) (bitNot y))
  bitOr :: a -> a -> a
  bitOr x y = bitNot (bitAnd (bitNot x) (bitNot y))
  bitNand :: a -> a -> a
  bitNand x y = bitNot (bitAnd x y)
```

哪一组操作“不是” `BitOperations` 的最小完整定义？

1.  `bitNand, bitAnd`
2.  `bitAnd, bitOr`
3.  `bitAnd, bitNot`
4.  `bitNot, bitOr`

声明 `instance Num a => Eq (Pair a)` 告诉我

1.  `Num` 的所有实例都是 `Eq` 的实例
2.  如果 `a` 是 `Num` 的实例，则 `Pair a` 是 `Eq` 的实例
3.  实例 `Eq (Pair a)` 继承实例 `Num a`

声明 `class Num a => Fractional a` 告诉我

1.  `Fractional` 的所有实例都必须是 `Num` 的实例
2.  `Num` 的所有实例都必须是 `Fractional` 的实例
3.  如果我为 `Fractional` 定义一个实例，我也会得到一个 `Num` 的实例
4.  如果我为 `Num` 定义一个实例，我也会得到一个 `Fractional` 的实例


## 6.6 练习

- [Set6](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set6.hs)：定义类和实例
