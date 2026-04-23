# 第 4 讲：类型类进阶

- 元组
- 类型类
- 数据结构：Map、Array


## 4.1 附注：元组

在深入了解类型类之前，让我们先介绍 Haskell 中最后一个内置数据类型：元组。*元组*或*对*（或三元组、四元组等）是将几个不同类型的值捆绑在一起的方式。你可以把元组看作固定长度的列表（就像 Python 的元组一样）。与列表不同，元组中的每个元素可以有不同的类型。元素的类型反映在元组的类型中。下面是元组类型和值的一些示例：

| 类型 | 示例值 |
|:------------------|:---------------------|
| `(String,String)` | `("Hello","World!")` |
| `(Int,Bool)` | `(1,True)` |
| `(Int,Int,Int)` | `(4,0,3)` |

要从元组中获取值，可以使用函数 `fst` 和 `snd`：

```haskell
fst :: (a, b) -> a
snd :: (a, b) -> b
```

你还可以对元组进行模式匹配。这通常是最方便的方法，也适用于较大的元组。`fst` 和 `snd` 函数只对二元组有效。

元组与列表结合起来很有用。下面是使用 `Data.List` 模块中的 `zip`、`unzip` 和 `partition` 函数的一些示例。

```haskell
zip :: [a] -> [b] -> [(a, b)]    -- 两个列表转为由配对组成的列表
unzip :: [(a, b)] -> ([a], [b])  -- 由配对组成的列表转为一对列表
partition :: (a -> Bool) -> [a] -> ([a], [a])    -- 满足和不满足谓词的元素
```

```haskell
zip [1,2,3] [True,False,True]
  ==> [(1,True),(2,False),(3,True)]
unzip [("Fred",1), ("Jack",10), ("Helen",13)]
  ==> (["Fred","Jack","Helen"],[1,10,13])
partition (>0) [-1,1,-4,3,2,0]
  ==> ([1,3,2],[-1,-4,0])
```

下面是元组模式匹配的例子：

```haskell
swap :: (a,b) -> (b,a)
swap (x,y) = (y,x)
```

下面是同时对元组和列表进行模式匹配的例子：

```haskell
-- 对所有与 True 配对的数字求和
sumIf :: [(Bool,Int)] -> Int
sumIf [] = 0
sumIf ((True,x):xs) = x + sumIf xs
sumIf ((False,_):xs) = sumIf xs
```

```haskell
sumIf [(True,1),(False,10),(True,100)]
  ==> 101
```


## 4.2 插曲：折叠

再次考虑函数 `sumNumbers :: [Int] -> Int`、`myMaximum :: [Int] -> Int` 和 `countNothings :: [Maybe a] -> Int`。

```haskell
sumNumbers :: [Int] -> Int
sumNumbers [] = 0
sumNumbers (x:xs) = x + sumNumbers xs

myMaximum :: [Int] -> Int
myMaximum [] = 0
myMaximum (x:xs) = go x xs
  where go biggest [] = biggest
        go biggest (x:xs) = go (max biggest x) xs

countNothings :: [Maybe a] -> Int
countNothings [] = 0
countNothings (Nothing : xs) = 1 + countNothings xs
countNothings (Just _  : xs) = countNothings xs
```

它们有一个共同点：接收一个列表，并生成一个依赖于列表元素的值。也就是说，它们把包含许多值的列表“压缩”或“折叠”成一个值。

Prelude 有一个名为 `foldr` 的函数，它会对 `Foldable` 数据类型执行“右结合折叠”。我们很快就会了解更多关于 `Foldable` 的内容。现在只需要把它想成列表上的函数即可，所以我们先看这个定义：

```haskell
foldr :: (a -> b -> b) -> b -> [a] -> b
foldr f y []     = y
foldr f y (x:xs) = f x (foldr f y xs)
```

这个定义表示：对于空列表 `[] :: [a]`，`foldr` 返回默认值 `y :: b`。对于任何非空列表 `x : xs`，`foldr` 会把 `f` 应用到 `x` 和 `foldr f y xs` 的结果上，也就是折叠列表其余部分后的结果。这是一个简单的递归定义。

换句话说，`foldr` 会反复调用它的参数函数 `f`，每次传入两个参数：

- 第一个参数是列表中的当前元素。
- 第二个参数是 `f` 为列表的其余部分返回的内容。

考虑列表 `[1,2,3]`：

![列表 [1,2,3]](img/list123.svg)

表达式 `foldr (+) 0 [1,2,3]` 的计算结果如下：

```haskell
foldr (+) 0 [1,2,3] ==> foldr (+) 0 (1:2:3:[])
                    ==> 1 + (foldr (+) 0 (2:3:[]))
                    ==> 1 + (2 + (foldr (+) 0 (3:[])))
                    ==> 1 + (2 + (3 + (foldr (+) 0 [])))
                    ==> 1 + (2 + (3 + 0))
```

结果可以被认为是一棵树：

![求和示例](img/sum123.svg)

理解 `foldr f y xs` 的一种方法是：它把 `(:)` 操作替换为 `f`，把 `[]` 替换为 `y`。在这个例子里，`f` 是 `(+)`，`y` 是 `0`。如果你写出 `sumNumbers [1,2,3]` 的求值过程，会发现它和 `foldr (+) 0 [1,2,3]` 执行的是同样的计算。更一般地：

```haskell
sumNumbers xs == foldr (+) 0 xs
```

那些具有数学背景的人可能会注意到，我们可以通过*归纳法*来证明这个说法：首先，`sumNumbers [] ==> 0` 和 `foldr (+) 0 [] ==> 0`，所以在基础情况下 `sumNumbers [] == foldr (+) 0 []`。接下来，我们可以假设 `sumNumbers xs == foldr (+) 0 xs` 对任何列表 `xs` 成立作为我们的归纳假设。然后，对于列表 `x:xs`，我们有 `sumNumbers (x:xs) ==> x + sumNumbers xs`。因此，通过归纳假设，得到 `foldr (+) 0 (x:xs) ==> x + foldr (+) 0 xs ==> x + sumNumbers xs`。因此，通过归纳法，方程成立。

在这门课程中，你不需要阅读、编写或理解归纳证明，但知道 Haskell 中函数的属性和等式可以（原则上）进行数学分析也许会很放心，因为 Haskell 是一门很好的语言。（任何编程语言都可以分析等式和属性，但对于 Haskell 来说，这种分析特别方便，因为 Haskell 是纯的。）

另一个折叠例子是 `map` 函数：

```haskell
map g xs = foldr helper [] xs
  where helper y ys = g y : ys
```

要了解其原理，请考虑 `foldr helper [] [x1,x2,..,xn]` 的作用：

![foldr 示例 1](img/foldr1.svg)

现在，由于 `helper x xs ==> g x : xs` 对于每个 `x` 和 `xs`，我们得到：

![foldr 示例 2](img/foldr2.svg)

结果列表 `[ g x1, g x2, g x3, ..., g xn ]` 正是我们使用 `map g xs` 得到的列表。（这也可以通过归纳法来证明，就像我们对 `sumNumbers` 所做的那样。）要吸取的教训是，折叠是一种特殊但相当普遍的方法，可以将某些变换递归地应用到某些结构（比如列表）中。


## 4.3 类型类

Haskell 的 `+` 为什么既能用于 `Int` 又能用于 `Double`？为什么我可以用 `==` 比较各种值？我们之前简要提到过约束类型。现在来看它们真正的含义。先看 `==` 和 `+` 的类型。

```haskell
(==) :: (Eq a) => a -> a -> Bool
```

类型 `(Eq a) => a -> a -> Bool` 的含义是：*对于所有属于 `Eq` 类的类型 `a`，这是一个 `a -> a -> Bool` 类型的函数*。也就是说，如果类型 `a` 是 `Eq` 类的成员，就可以把两个 `a` 类型的值传给 `==`，并得到一个 `Bool` 结果。

```haskell
(+) :: (Num a) => a -> a -> a
```

类似地，类型 `(Num a) => a -> a -> a` 意味着：*对于属于 `Num` 类的所有类型 `a`，这是类型 `a -> a -> a` 的函数*。也就是说，只要 `a` 是 `Num` 的成员，你就可以将两个相同类型 `a` 的值传给 `+` 并得到类型 `a` 的值。

`Num` 和 `Eq` 是类型类。*类型类*是一种将支持类似操作的类型分组在一起的方法。

**注意！** 类型类是类型的集合。它与面向对象编程中的类没有太大关系！在某些情况下，类型类可以类比为面向对象编程中的“接口”。不幸的是，类型类中的函数通常也被称为“方法”，这会增加一些混淆。

附言。还记得使用类型变量实现多态性如何称为“参数多态性”吗？描述类型类所实现的这种多态的术语是“特设多态”。不同之处在于，对于参数多态性，函数（例如 `head`）对所有类型都有相同的实现，而对于特设多态，则有多种实现（考虑数字和字符串上的 `==`）。


## 4.4 类型约束

当你使用具体类型（不是类型变量）时，你可以只使用类型类函数（在这个例子中为 `(==)`）：

```haskell
f :: (Int -> Int) -> Int -> Bool
f g x = x == g x
```

当然，如果相关类型不是正确类的成员，则会出现错误。例如：

```haskell
addTrue :: Bool -> Bool
addTrue b = b + True
```

    error:
        • No instance for (Num Bool) arising from a use of ‘+’
        • In the expression: b + True
          In an equation for ‘addTrue’: addTrue b = b + True

然而，在*多态*函数中，你需要添加*类型约束*。这是不行的：

```haskell
f :: (a -> a) -> a -> Bool
f g x = x == g x
```

幸运的是，这个错误很好：

    error:
        • No instance for (Eq a) arising from a use of ‘==’
          Possible fix:
            add (Eq a) to the context of
              the type signature for:
                f :: (a -> a) -> a -> Bool
        • In the expression: x == g x
          In an equation for ‘f’: f g x = x == g x

为了表明 `f` 仅适用于 `Eq` 类成员的类型，我们向类型签名添加类型约束 `(Eq a) =>`。

```haskell
f :: (Eq a) => (a -> a) -> a -> Bool
f g x = x == g x
```

如果你没有类型签名，*类型推断*可以推导出约束！

```haskell
Prelude> f g x = x == g x
Prelude> :type f
f :: (Eq a) => (a -> a) -> a -> Bool
```

你还可以有多个约束：

```haskell
bothPairsEqual :: (Eq a, Eq b) => a -> a -> b -> b -> Bool
bothPairsEqual left1 left2 right1 right2 = left1 == left2 && right1 == right2
```


## 4.5 标准类型类

下面是你应该了解的一些标准 Haskell 类型类。

### 4.5.1 `Eq`

我们已经看到了用于相等比较的 `Eq` 类。下面是 `Eq` 类的基本操作及其使用例子。正如你所看到的，到目前为止我们见过的几乎所有类型（除了函数）都是 `Eq` 的成员。

```haskell
(==) :: Eq a => a -> a -> Bool
(/=) :: Eq a => a -> a -> Bool
```

```haskell
Prelude> 1 == 2
False
Prelude> 1 /= 2
True
Prelude> "Foo" == "Bar"
False
Prelude> [[1,2],[3,4]] == [[1,2],[3,4]]
True
Prelude> (\x -> x+1) == (\x -> x+2)

<interactive>:5:1: error:
    • No instance for (Eq (Integer -> Integer))
        arising from a use of ‘==’
        (maybe you haven't applied a function to enough arguments?)
    • In the expression: (\ x -> x + 1) == (\ x -> x + 2)
      In an equation for ‘it’: it = (\ x -> x + 1) == (\ x -> x + 2)
```

还有一些其他有用的函数使用 `Eq` 类，比如模块 `Data.List` 中的 `nub`。

```haskell
Prelude> import Data.List
Prelude Data.List> :t nub
nub :: Eq a => [a] -> [a]
Prelude Data.List> nub [3,5,3,1,1]      -- eliminates duplicates
[3,5,1]
```

### 4.5.2 `Ord`

`Ord` 类用于排序（小于、大于）。同样，这里是基本操作及其使用的一些示例。请注意新的 `Ordering` 类型。它的值 `LT` 表示“小于”， `EQ` 表示“等于”， `GT` 表示“大于”。

```haskell
compare :: Ord a => a -> a -> Ordering
(<) :: Ord a => a -> a -> Bool
(>) :: Ord a => a -> a -> Bool
(>=) :: Ord a => a -> a -> Bool
(<=) :: Ord a => a -> a -> Bool
max :: Ord a => a -> a -> a
min :: Ord a => a -> a -> a
```

```haskell
Prelude> compare 1 1                -- 1 is EQual to 1
EQ
Prelude> compare 1 3                -- 1 is Less Than 3
LT
Prelude> compare 1 0                -- 1 is Greater Than 0
GT
Prelude> min 5 3
3
Prelude> max 5 3
5
Prelude> "aardvark" < "banana"      -- strings are compared alphabetically
True
Prelude> [1,2,3] > [2,5]            -- lists are compared like strings
False
Prelude> [1,2,3] > [1,1]
True
```

当我们可以比较值时，我们还可以对它们的列表进行排序。`Data.List` 中的函数 `sort` 适用于所有属于 `Ord` 类的类型。

```haskell
Prelude> import Data.List
Prelude Data.List> :t sort
sort :: Ord a => [a] -> [a]
Prelude Data.List> sort [6,1,4,8,2]
[1,2,4,6,8]
Prelude Data.List> sort "black sphinx of quartz, judge my vow!"     -- remember, strings are lists!
"      !,aabcdefghijklmnoopqrstuuvwxyz"
```

作为最后一个例子，让我们根据长度对列表列表进行排序。我们需要两个辅助函数：

```haskell
-- 来自 Data.Ord 模块
-- 通过函数 f 比较两个值
comparing :: (Ord a) => (b -> a) -> b -> b -> Ordering
comparing f x y = compare (f x) (f y)

-- 来自 Data.List 模块
-- 使用给定的比较函数对列表排序
sortBy :: (a -> a -> Ordering) -> [a] -> [a]
```

现在 `sortByLength` 的实现很简单：

```haskell
-- 按长度对列表排序
sortByLength :: [[a]] -> [[a]]
sortByLength = sortBy (comparing length)
```

```haskell
sortByLength [[1,2,3],[4,5],[4,5,6,7]]   ==>  [[4,5],[1,2,3],[4,5,6,7]]
```

### 4.5.3 `Num`, `Integral`, `Fractional`, `Floating`

`Num` 类包含数值算术：

```haskell
(+) :: Num a => a -> a -> a
(-) :: Num a => a -> a -> a
(*) :: Num a => a -> a -> a
negate :: Num a => a -> a    -- 0-x
abs :: Num a => a -> a       -- 绝对值
signum :: Num a => a -> a    -- -1 for negative values, 0 for 0, +1 for positive values
fromInteger :: Num a => Integer -> a
```

`Num` 也出现在数字字面量的类型中：

```haskell
Prelude> :t 12
12 :: Num p => p
```

这意味着像 `12` 这样的字面量可以解释为实现 `Num` 的任何类型的成员。当 GHC 读取 `12` 之类的数字字面量时，它会生成对应于 `fromIntegral 12` 的代码。

```haskell
Prelude> 1 :: Int
1
Prelude> 1 :: Double
1.0
Prelude> fromIntegral 1 :: Double
1.0
```

`Integral` 是表示整数的类型类，比如 `Int` 和 `Integer`。最有趣的函数是用于整数除法和取余的 `div` 和 `mod`。属于 `Integral` 的所有类型也属于 `Num`。

```haskell
div :: Integral a => a -> a -> a
mod :: Integral a => a -> a -> a
```

`Fractional` 是具有除法操作的类。属于 `Fractional` 的所有类型也属于 `Num`。

```haskell
(/) :: Fractional a => a -> a -> a
```

`Floating` 包含一些仅对浮点数有意义的附加操作。属于 `Floating` 的所有类型也属于 `Fractional`（以及 `Num`）。

```haskell
sqrt :: Floating a => a -> a
sin :: Floating a => a -> a
```

### 4.5.4 `Read` 和 `Show`

`Show` 和 `Read` 类用于函数 `show` 和 `read`，它们将值和字符串相互转换。

```haskell
show :: Show a => a -> String
read :: Read a => String -> a
```

```haskell
Prelude> show 3
"3"
Prelude> read "3" :: Int
3
Prelude> read "3" :: Double
3.0
```

正如你在上面所看到的，你经常需要将类型标注与 `read` 一起使用，以便编译器可以选择正确的实现。

### 4.5.5 附注：`Foldable`

还有一件事！你可能还记得前面提到过 `length` 的类型不是 `[a] -> Int` 而是更通用的类型。让我们看看：

```haskell
Prelude> :t length
length :: Foldable t => t a -> Int
```

这种类型看起来与我们之前见过的有点不同。类型变量 `t` 有一个参数 `a`。我们将在第 2 部分中更详细地讨论这样的类型类，但这里有一个快速入门。

`Foldable` 代表可以折叠的类型。`foldr` 的真实类型是：

```haskell
foldr :: Foldable t => (a -> b -> b) -> b -> t a -> b
```

我们已经成功地利用了列表是 `Foldable` 的事实，因为我们已经成功地在列表上使用了 `length` 和 `foldr`。然而，`Maybe` 也是 `Foldable`！`Maybe` 的 `Foldable` 实例只是将 `Maybe a` 的值看作长度为 0 或 1 的列表：

```haskell
foldr (+) 1 Nothing   ==> 1
foldr (+) 1 (Just 3)  ==> 4
length Nothing        ==> 0
length (Just 'a')     ==> 1
```

接下来我们将遇到更多可折叠的类型。


## 4.6 更多数据结构

现在我们已经熟悉了标准类型类，我们可以看看它们的应用之一：`Map` 和 `Array` 数据结构。

### 4.6.1 `Data.Map`

`Data.Map` 模块定义了 `Map` 类型。映射是键值对的搜索树。看待这个问题的一种方法是，`Map k v` 类型的值与 `[(k,v)]` 类型的值（对的列表）大致相同。然而，映射上的操作比列表上的操作更高效。

由于 `Data.Map` 包含一些与 `Prelude` 函数同名的函数，因此需要使用*限定*导入：

```haskell
import qualified Data.Map as Map
```

现在我们可以将映射类型称为 `Map.Map`，以及各种映射函数，比如 `Map.insert`。下面是映射最重要的函数：

```haskell
-- 从键值对列表创建 Map
Map.fromList :: Ord k => [(k, a)] -> Map.Map k a

-- 向 Map 插入一个值。会覆盖相同键上的旧值。
-- 返回一个新的 Map。不会修改给定的 Map。
Map.insert :: Ord k => k -> a -> Map.Map k a -> Map.Map k a

-- 使用键从 Map 中获取值。如果键不存在，则返回 Nothing。
Map.lookup :: Ord k => k -> Map.Map k a -> Maybe a

-- 空 Map
Map.empty :: Map.Map k a
```

需要映射键类型的 `Ord` 约束，因为映射被实现为*有序二叉树*。

请注意，与所有 Haskell 值一样，映射是“不可变的”，这意味着一旦定义映射就无法更改它。然而，像 `insert` 这样的映射操作会生成一个“新”映射。要执行多个映射操作，你需要重用返回值。下面是在映射上进行操作的 GHCi 会话。

```haskell
Prelude> import qualified Data.Map as Map
Prelude Map> values = Map.fromList [("z",3),("w",4)]
Prelude Map> Map.lookup "z" values
Just 3
Prelude Map> Map.lookup "banana" values
Nothing
Prelude Map> Map.insert "x" 7 values
fromList [("w",4),("x",7),("z",3)]
Prelude Map> values                                       -- note immutability!
fromList [("w",4),("z",3)]
Prelude Map> Map.insert "x" 1 (Map.insert "y" 2 values)   -- two insertions
fromList [("w",4),("x",1),("y",2),("z",3)]
Prelude Map>
```

下面的示例把银行表示为 `Map String Int`（从账户名称映射到账户余额），并从账户中取出一些钱：

```haskell
withdraw :: String -> Int -> Map.Map String Int -> Map.Map String Int
withdraw account amount bank =
  case Map.lookup account bank of
    Nothing  -> bank                                   -- 找不到账户，不做更改
    Just sum -> Map.insert account (sum-amount) bank   -- set new balance
```

下面是如何在 `GHCi` 中使用 `withdraw` 函数。请注意映射如何打印为 `fromList` 调用。还要注意，调用 `withdraw ... bank` 如何返回*新*账户，并且不会更改原有账户。

```haskell
GHCi> bank = Map.fromList [("Bob",100),("Mike",50)]
GHCi> withdraw "Bob" 80 bank
fromList [("Bob",20),("Mike",50)]
GHCi> bank                         -- note immutability
fromList [("Bob",100),("Mike",50)]
GHCi> withdraw "Bozo" 1000 bank
fromList [("Bob",100),("Mike",50)]
```

`Data.Map` 定义了各种有用的高阶函数来更新映射。我们可以使用 `Data.Map.adjust` 来重写 `withdraw` 函数：

```haskell
withdraw :: String -> Int -> Map.Map String Int -> Map.Map String Int
withdraw account amount bank = Map.adjust (\x -> x-amount) account bank
```

**注意！** 有单独的 `Data.Map.Strict` 和 `Data.Map.Lazy` 实现。当你导入 `Data.Map` 时，你会得到 `Data.Map.Lazy`。你可以在[`Data.Map.Lazy` 的文档](https://hackage.haskell.org/package/containers-0.6.5.1/docs/Data-Map-Lazy.html)中找到所有 `Data.Map` 函数的文档。我们不会在这里讨论它们的差异，但大多数情况下你应该在实际代码中使用 `Data.Map.Strict`。

### 4.6.2 `Data.Array`

另一种行为类似列表、但在某些操作上更高效的类型是数组。数组在许多其他编程语言中都很常见，但 Haskell 数组有点不同。

与 `Data.Map` 模块不同，`Data.Array` 可以正常导入：

```haskell
import Data.Array
```

现在来看用于构造数组的 `array` 函数的类型。

```haskell
array :: Ix i => (i, i) -> [(i, e)] -> Array i e
```

这里有几件事需要注意。首先，`Array` 类型由两种类型参数化：索引类型和元素类型。大多数其他编程语言中的数组通常只用元素类型参数化，索引类型则固定为 `int`。但在 Haskell 中，我们可以有一个 `Array Char Int`：一个由字符索引的数组；也可以有 `Array Bool String`，也就是由布尔值索引的数组；甚至可以有 `Array (Int,Int) Int`，也就是二维整数数组。

并非所有类型都可以是索引类型。只有类似于整数的类型才适合。这就是 `Ix i` 类约束的原因。`Ix` 类收集所有可用作数组索引的类型。

其次，`array` 函数需要一个额外的 `(i,i)` 参数，表示数组的最小索引和最大索引。在其他一些语言中，数组总是从索引 0 或 1 开始；而在 Haskell 中，你可以定义一个从 7 开始到 11 的数组。这个数组可以这样写：

```haskell
myArray :: Array Int String
myArray = array (7,11) [(7,"seven"), (8,"eight"), (9,"nine"), (10,"ten"), (11,"ELEVEN")]
```

按顺序列出所有索引和元素可能有点麻烦，因此还有 `listArray` 构造函数，它只需要按顺序给出元素列表：

```haskell
listArray :: Ix i => (i, i) -> [e] -> Array i e
```

```haskell
myArray :: Array Int String
myArray = listArray (7,11) ["seven", "eight", "nine", "ten", "ELEVEN"]
```

数组与两个新运算符一起使用：

```haskell
-- 数组查找
(!) :: Ix i => Array i e -> i -> e
-- 数组更新
(//) :: Ix i => Array i e -> [(i, e)] -> Array i e
```

下面是 `GHCi` 会话例子：

```haskell
Prelude> import Data.Array
Prelude Data.Array> myArray = listArray (7,11) ["seven", "eight", "nine", "ten", "ELEVEN"]
Prelude Data.Array> myArray
array (7,11) [(7,"seven"),(8,"eight"),(9,"nine"),(10,"ten"),(11,"ELEVEN")]
Prelude Data.Array> myArray ! 8
"eight"
Prelude Data.Array> myArray // [(8,"ocho"),(9,"nueve")]
array (7,11) [(7,"seven"),(8,"ocho"),(9,"nueve"),(10,"ten"),(11,"ELEVEN")]
```

你可能想知道为什么 `(//)` 运算符一次执行多个更新。原因是 Haskell 数组的主要弱点：不变性。由于数组无法就地修改，因此 `(//)` 必须复制整个数组。这就是为什么在 Haskell 中通常最好使用列表或映射来存储需要更新的数据。然而，当构造一次然后用于大量查找时，数组可能仍然有用。我们将在下一讲中回顾 Haskell 数据结构的工作原理。

**注意！** 在本课程中，我们将仅使用 `Array`，这是 Haskell 标准中指定的简单数组类型。还有许多其他数组类型，比如可变的 `IOArray` 和有些晦涩的 `DiffArray`。还有用于数组的类型类，如 `IArray` 和 `MArray`。除了数组之外，还有[大量的 `Vector` 类型](https://hackage.haskell.org/package/vector)对于实际程序来说比 `Array` 更实用。

### 4.6.3 附注：折叠映射和数组

`Map` 和 `Array` 类型是 `Foldable` 的实例，就像列表一样！这意味着你可以在它们上使用 `length` 和 `foldr` 等函数：

```haskell
length (array (7,11) [(7,"seven"),(8,"eight"),(9,"nine"),(10,"ten"),(11,"ELEVEN")])
  ==> 5
foldr (+) 0 (Map.fromList [("banana",3),("egg",7)])
  ==> 10
```


## 4.7 阅读文档

Haskell 库通常有很好的文档。我们之前已经通过 Hackage (<https://hackage.haskell.org>) 链接到了一些文档，但知道如何自己查找文档也很重要。用于生成 Haskell 文档的工具称为 *Haddock*，因此 Haskell 文档有时也被称为 *haddocks*。

Hackage 是 Haskell 包仓库（就像 Python 的 [PyPI](https://pypi.org/)、Java 的 Maven Central 或 JavaScript 的 [NPM](https://npmjs.com)）。除了实际的包之外，它还托管它们的文档。我们在本课程中使用的大多数模块都位于名为 `base` 的包中。你可以在 <https://hackage.haskell.org/package/base-4.16.4.0/> 浏览 `base` 包的文档。

当你不太确定要找的函数在哪里时，Hoogle (<https://hoogle.haskell.org/>) 可以提供帮助。Hoogle 是 Haskell 文档的搜索引擎。当你需要检查 `foldr` 的类型或哪些包包含名为 `reverse` 的函数时，这是一个很好的资源。

最后，由于本课程使用 `stack` 工具，你还可以使用以下命令浏览已为你安装的库的文档

    stack haddock --open
    stack haddock --open <package>

这样做的另一个好处是可以获得正确版本的文档。

总结一下，阅读 Haskell 库文档的主要方式如下：

- 如果你知道包的名称，你可以通过 <https://hackage.haskell.org/> 浏览文档。
- 如果你知道函数的名称，则可以使用 <https://hoogle.haskell.org/> 找到它。
- 如果你使用的是 `stack`，则可以使用 `stack haddock --open` 或 `stack haddock --open <package>` 在浏览器中打开文档。


## 4.8 测验

`swap . swap` 是什么类型？

1.  `(a, b) -> (a, b)`
2.  `(a, b) -> (b, a)`
3.  `a -> a`

`\f g x -> (f x, g x)` 是什么类型？

1.  `(a -> b) -> (c -> d) -> (a,c) -> (b, d)`
2.  `(a -> b) -> (a -> c) -> a -> (b, c)`
3.  `(a -> b) -> (b -> a) -> a -> (b, a)`

`\t -> (fst . fst $ t, (snd . fst $ t, snd t))` 是什么类型？

1.  `(a, (b, c)) -> (a, (b, c))`
2.  `(a, (b, c)) -> ((a, b), c)`
3.  `((a, b), c) -> (a, (b, c))`

函数 `foldr (\x xs -> xs ++ [x]) []` 有什么作用？

1.  它根本不改变它的输入列表
2.  它从左到右更改列表的关联性
3.  它反转其输入列表

函数 `foldr (\(x, y) zs -> x : y : zs) []` 有什么作用？

1.  它将一对列表变成一对列表
2.  它将一对列表变成一个对的列表
3.  它将对列表转换为元素列表

`foldr (\n b -> n == 3 && b)` 是什么类型？

1.  `(Foldable t, Eq a, Num a) => Bool -> t a -> Bool`
2.  `(Foldable t, Eq a, Num a, Bool b) => b -> t a -> b`
3.  `(Foldable t, Eq a, Num a) => Bool -> [ a ] -> Bool`

`\x -> case x of (True, "Foo") -> show True ++ "Foo"` 是什么类型？

1.  `Either Bool String -> String`
2.  `(Bool, String) -> String`
3.  `Show a => (Bool, String) -> a`


## 4.9 练习

- [Set4a](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set4a.hs)：类型类
- [Set4b](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set4b.hs)：折叠
