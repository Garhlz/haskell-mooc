# 第 12 讲：fmap fmap fmap


## 12.1 本讲内容

- Functor


## 12.2 Functor

### 12.2.1 保留结构

还记得列表的 `map` 函数吗？这是它的定义：

```haskell
map :: (a -> b) -> [a] -> [b]
map _ []     = []
map g (x:xs) = g x : map g xs
```

它将函数 `g :: a -> b` 应用到 `[a]` 类型列表的每个元素，返回 `[b]` 类型的列表。`map` 的类型也可以写成 `(a -> b) -> ([a] -> [b])`。这和之前的类型是一样的，因为 `->` 是右结合的。括号强调了这样一个事实：`map` 会把函数 `g :: a -> b` 转换成函数 `map g :: [a] -> [b]`。这意味着 `map` 是一个把函数转换成函数的“高阶函数”。

因为 `map` 是参数多态的，所以它的定义不依赖于列表中存储的值的类型。因此每个 `a -> b` 类型的函数都用完全相同的逻辑转换为 `[a] -> [b]` 类型的函数。使用上面的定义，我们可以看到：

```haskell
map (|| True) [True, True, False]
  ==> [True || True, True || True, False || True]
  ==> [True, True, True]

map (+1) [1,2,3]
  ==> [1 + 1, 2 + 1, 3 + 1]
  ==> [2, 3, 4]

map (++"1") ["1", "2", "3"]
  ==> ["1" ++ "1", "2" ++ "1", "3" ++ "1"]
  ==> ["11", "21", "31"]
```

这里值得注意的是 `map` *保留了列表的结构*。列表的长度和元素位置不变。总体思路如下图所示。

![将函数 g 映射到列表](img/map.svg)

将函数 `g` 映射到列表

让我们看看能否找到其他类似的函数。 `Maybe a` 类型的值有点像长度最多为 1 的列表。让我们对 `Maybe` 做映射！你能看出和 `map` 的定义相似吗？

```haskell
mapMaybe :: (a -> b) -> Maybe a -> Maybe b
mapMaybe f Nothing = Nothing
mapMaybe f (Just x) = Just (f x)
```

这里值的结构也被保留。`Nothing` 仍然是 `Nothing`，`Just` 仍然是 `Just`。我们也可以把这个类型看成 `(a -> b) -> (Maybe a -> Maybe b)`：它把普通函数转换（或“提升”）成可以在 `Maybe` 上运行的函数。

还有一个示例：考虑二叉树。

```haskell
data Tree a = Leaf | Node a (Tree a) (Tree a)

mapTree :: (a -> b) -> Tree a -> Tree b
mapTree f Leaf = Leaf
mapTree f (Node val left right) = Node (f val) (mapTree f left) (mapTree f right)
```

二叉树可能如下所示：

![二叉树](img/mapTreeBefore.svg)

一棵二叉树

在 `mapTree g` 之后，树将如下所示：

![将 g 映射到其节点后的二叉树](img/mapTreeAfter.svg)

将 `g` 映射到其节点后的二叉树

### 12.2.2 `Functor` 类

现在我们有了三个不同的结构保留映射函数。不同类型的三个类似操作。我们可以编写一个类型类来捕获这种相似性吗？

```haskell
map      :: (a -> b) ->      [a] ->      [b]
mapMaybe :: (a -> b) -> Maybe a  -> Maybe b
mapTree  :: (a -> b) -> Tree  a  -> Tree  b
```

编写类型类的天真尝试会遇到问题。如果我们尝试抽象 `Maybe c`，似乎我们无法为映射操作编写正确的类型。我们需要能够以某种方式更改类型参数 `c`。

```haskell
class Mappable m where
  mapThing :: (a -> b) -> m -> m

instance Mappable (Maybe c) where
  mapThing :: (a -> b) -> Maybe c -> Maybe c
  mapThing = ...
```

幸运的是，Haskell 类型类还有一个我们以前没有介绍过的能力。除了普通类型之外，你还可以为“类型构造函数”编写类型类。这是什么意思？来看标准类型类 `Functor`，它正好表达了我们试图用 `Mappable` 表达的东西。

```haskell
class Functor f where
  fmap :: (a -> b) -> f a -> f b
```

请注意类型参数 `f` 是如何成为类型构造函数的：它在 `fmap` 类型的不同部分中传递 `a` 和 `b` 参数。现在让我们看看 `Maybe` 的实例。

```haskell
instance Functor Maybe where
  -- 在这个实例中，fmap 的类型是：
  -- fmap :: (a -> b) -> Maybe a -> Maybe b
  fmap f Nothing = Nothing
  fmap f (Just x) = Just (f x)
```

现在 `fmap` 有了正确的类型，我们可以像 `mapMaybe` 一样实现它！请注意，我们声明的是 `instance Functor Maybe`，而不是 `instance Functor (Maybe a)`。类型 `Maybe a` 不是 Functor，类型构造函数 `Maybe` 才是。

列表的类型构造函数写作 `[]`。它是特殊语法，就像其他列表语法一样。不过，如果类型 `[a]` 写成 `List a`，那么类型构造函数 `[]` 就相当于 `List`。

```haskell
instance Functor [] where
  fmap = map
```

这是我们的最后一个示例，作为 `Functor` 实例。

```haskell
data Tree a = Leaf | Node a (Tree a) (Tree a)

instance Functor Tree where
  fmap _ Leaf = Leaf
  fmap f (Node val left right) = Node (f val) (fmap f left) (fmap f right)
```

附注：Functor 一词最初来自数学的一个分支，称为[范畴论](https://en.wikipedia.org/wiki/Category_theory)。不过，要使用 Haskell，你不需要了解任何范畴论。随着你继续学习 Haskell，你可能会对范畴论感兴趣，它也可能成为编程新思想的宝贵来源。范畴论可能让人望而生畏，所以知道没有它也能写好 Haskell 是件好事。现在，当你看到 Functor 时，可以先把它理解成“可以映射的东西”，或者“一个容器”。

换个角度看。当我们有一个实例 `Functor MyFun` 时，我们知道可以把类型 `X` 映射到新类型 `MyFun X`（因为 `MyFun` 是类型构造函数），也可以把接受 `X` 参数的函数 `f` 提升为接受 `MyFun X` 参数的函数 `fmap f`。所以可以说，我们既在类型层面做映射，也在值层面做映射。

哦，对了，还有一件事。一旦掌握了 `fmap` 的窍门，你可能会发现自己经常使用它。对于大量使用 `fmap` 的代码，最好使用其中缀别名 `<$>`。考虑这些示例中 `$` 和 `<$>` 之间的对称性：

```haskell
(+1) <$> [1,2,3]    ==>  [2,3,4]
not <$> Just False  ==>  Just True

reverse . tail  $       "hello"       ==>  "olle"
reverse . tail <$> Just "hello"       ==>  Just "olle"
-- 这等同于
fmap (reverse . tail) (Just "hello")  ==>  Just "olle"
```


## 12.3 符合法则的实例

上面提到的“保留结构”到底是什么？以下两个“Functor 法则”预计适用于任何 `Functor` 实例 `f`（尽管不幸的是 Haskell 编译器无法强制执行它们）：

1.`fmap id === id`
2.`fmap (f . g) === fmap f . fmap g`

如果这听起来很抽象，请不要担心！第一条 Functor 定律规定，Functor 将 `id :: a -> a` 映射到 `id :: f a -> f a`。 （`id` 是恒等函数，意思是 `id x = x`。）让我们具体来看一下它对于列表 `[1,2,3]` 是如何工作的：

```haskell
fmap id [1,2,3] ==> map id [1,2,3]
                ==> map id (1:[2,3])
                ==> id 1 : map id [2,3]
                ==> 1 : map id [2,3]
                ==> 1 : id 2 : map id [3]
                ==> 1 : 2 : id 3 : map id []
                ==> 1 : 2 : 3 : []
                === [1,2,3]
```

另一方面，

```haskell
id [1,2,3] ==> [1,2,3]
```

因此，`fmap id [1,2,3]` 的结果与 `id [1,2,3]` 的结果相同，因此第一条 Functor 定律在这种情况下成立。不难证明第一条 Functor 定律适用于任何列表。

如果你仔细想想，第一条 Functor 定律确实是一个非常简单的命题。它只是说，如果我们将 `fmap` 应用于不改变任何内容的函数 (`id`)，则结果函数 (`fmap id`) 再次不改变任何内容。因此，应用 `fmap` 的行为本身保留了Functor 的结构。

第二条 Functor 定律怎么样？对于列表，考虑如果我们 `fmap` 函数 `negate.(*2)` （记住，`negate` 将 `x` 映射到 `-x` 并且 `(*2)` 将其参数乘以 `2`）会发生什么：

```haskell
fmap (negate.(*2)) [1,2,3] ==> map (negate.(*2)) [1,2,3]
                           ==> (negate.(*2)) 1 : map (negate.(*2)) [2,3]
                           ==> negate (1 * 2)  : map (negate.(*2)) [2,3]
                           ==> -2 : map (negate.(*2)) [2,3]
                           ==> -2 : (negate.(*2)) 2 : map (negate.(*2)) [3]
                           ==> -2 : -4 : map (negate.(*2)) [3]
                           ==> -2 : -4 : (negate.(*2)) 3 : map (negate.(*2)) []
                           ==> -2 : -4 : -6 : []
                           ==> [-2,-4,-6]
```

在这种情况下，让我们考虑第二条 Functor 定律的右侧：

```haskell
(fmap negate . fmap (*2)) [1,2,3] ==> (map negate . map (*2)) [1,2,3]
                                  ==> map negate (map (*2) [1,2,3])
                                  ==> map negate [2,4,6]
                                  ==> [-2,-4,-6]
```

第二条 Functor 定律在这个特殊情况下成立。事实上，它适用于所有情况（练习！）。

一般来说，第二条 Functor 定律表示，首先组合两个函数然后应用 `fmap` 必须产生与对这些函数执行 `fmap` 然后组合结果函数相同的结果。换句话说，应用 `fmap` 和组合的顺序并不重要。 （这两个操作被称为“通勤”。）

还有一些高阶函数无法满足 Functor 定律。考虑函数 `badMap`：

```haskell
badMap :: (a -> b) -> [a] -> [b]
badMap f [] = []
badMap f (x:y:xs) = f x : badMap f xs
badMap f (x:xs) = f x : badMap f xs
```

该函数违反了第一条 Functor 定律。例如：

```haskell
badMap id [1,2,3] ==> badMap id (1:2:[3])
                  ==> id 1 : badMap id [3]
                  ==> 1 : badMap id [3]
                  ==> 1 : badMap id (3:[])
                  ==> 1 : id 3 : badMap id []
                  ==> 1 : 3 : []
                  ==> [1,3]
```

将 `badMap id` 应用于列表 `[1,2,3]` 会更改列表，因为元素 `2` 被删除。

如前所述，Haskell 编译器无法检测 Functor 是否遵守其法则。Haskell 编译器会接受使用 `badMap` 而不是 `map` 作为 `fmap` 实现的 `Functor []` 实例。这是 Haskell 类型系统的限制。有些技术（例如 LiquidHaskell）或依赖类型语言（例如 Agda、Idris、Coq 或 Lean）确实可以强制执行 Functor 法则，让非法 Functor 实例无法编译。然而，这些技术超出了本课程的范围。


## 12.4 附注：种类（Kind）

请记住，`Functor` 是类型构造函数的类。如果我们尝试为类型定义 `Functor` 的实例，我们会收到错误：

```haskell
Prelude> instance Functor Int where

<interactive>:1:18: error:
    • Expected kind ‘* -> *’, but ‘Int’ has kind ‘*’
    • In the first argument of ‘Functor’, namely ‘Int’
      In the instance declaration for ‘Functor Int’
```

错误消息谈到了*种类*。种类是*类型的类型*。像 `Int`、`Bool` 或 `Maybe Int` 这样可以包含值的类型，其种类是 `*`。类型构造函数的种类看起来像函数，例如 `Maybe` 的种类是 `* -> *`。这意味着必须把 `Maybe` 类型构造函数应用到一个种类为 `*` 的类型上，才能得到一个种类为 `*` 的类型。

我们可以向 GHCi 询问类型的种类：

```haskell
Prelude> :kind Int
Int :: *
Prelude> :kind Maybe
Maybe :: * -> *
Prelude> :kind Maybe Int
Maybe Int :: *
```

如果我们向 GHCi 询问有关 `Functor` 类的信息，它会告诉我们 `Functor` 的实例必须具有类型 `* -> *`：

```haskell
Prelude> :info Functor
class Functor (f :: * -> *) where
  fmap :: (a -> b) -> f a -> f b
...
```

以下是一些更复杂类型的示例。

```haskell
-- 多个类型参数
Prelude> :kind Either
Either :: * -> * -> *
Prelude> data Either3 a b c = Left a | Middle b | Right c
Prelude> :kind Either3
Either3 :: * -> * -> * -> *
-- 种类为 *->* 的类型参数
Prelude> data IntInside f = IntInside (f Int)
Prelude> :kind IntInside
IntInside :: (* -> *) -> *
```

在 Haskell 编程中你不会经常遇到 Kind，但有时你会在错误消息中看到与 Kind 相关的内容，了解它们是什么很有好处。


## 12.5 再谈 `Foldable`

我们在第 1 部分中简要介绍了 `Foldable` 类，它出现在基本函数的许多类型签名中。例如：

```haskell
length :: Foldable t => t a -> Int
sum :: (Foldable t, Num a) => t a -> a
minimum :: (Foldable t, Ord a) => t a -> a
foldMap :: (Foldable t, Monoid m) => (a -> m) -> t a -> m
```

从这些类型签名中可以看到，`Foldable` 和 `Functor` 一样，是类型构造函数的类型类（也就是类似 `* -> *` 的东西）。`Foldable` 的本质是“可以折叠的东西”的类型类。它的定义可以很简单：

```haskell
class Foldable (t :: *->*) where
  foldr :: (a -> b -> b) -> b -> t a -> b
```

不过，出于性能原因，真实的类型类包含许多方法（你可以在 GHCi 中用 `:info Foldable` 亲自查看！）。但当我们为 `Foldable` 定义实例时，只定义 `foldr` 就足够了。

理解 `Foldable` 的另一种方式是：它表示可以*从左到右*处理元素的结构。换句话说，如果 `Functor` 是容器的类型类，那么 `Foldable` 就是*有顺序的容器*的类型类。

作为示例，让我们为我们自己的配对类型实现 `Functor` 和 `Foldable` 。

```haskell
data Pair a = Pair a a
  deriving Show

instance Functor Pair where
  -- fmap f 将 f 应用于所有值
  fmap f (Pair x y) = Pair (f x) (f y)

instance Foldable Pair where
  -- 就像对长度为 2 的列表应用 foldr
  foldr f initialValue (Pair x y) = f x (f y initialValue)

-- 一个同时使用两个实例的示例函数
doubleAndCount :: (Functor f, Foldable f) => f Int -> Int
doubleAndCount = sum . fmap (*2)
```

现在，我们几乎可以在任何可以使用列表的地方使用 `Pair`：

```haskell
fmap (+1) (Pair 3 6)   ==> Pair 4 7
fmap (+1) [3,6]        ==> [4,7]

foldr (*) 1 (Pair 3 6) ==> 18
foldr (*) 1 [3,6]      ==> 18

length (Pair 3 6)      ==> 2
length [3,6]           ==> 2

minimum (Pair 3 6)     ==> 3
minimum [3,6]          ==> 3

doubleAndCount (Pair 3 6)  ==> 18
doubleAndCount [3,6]       ==> 18
```

我们遇到的其他类型 `Foldable` 包括 `Data.Map` 和 `Data.Array`。


## 12.6 回顾

因此，总而言之，Functor 是类型构造函数 `f` 和相应的 `Functor f` 实例，使得 `fmap` 满足两个 Functor 定律。这些定律断言 `fmap` 必须保留恒等函数并分布于函数组合上。更通俗地说，`fmap` 将基于值操作的函数 `g :: a -> b` 提升为基于容器操作的函数：`fmap g :: f a -> f b`。基本上 Haskell 中所有表现良好的数据结构都是 Functor。


## 12.7 测验

`fmap` 是什么类型的？

1.`a -> b -> f a -> f b`
2.`(a -> b) -> f a -> f b`
3.`Functor f => a -> b -> f a -> f b`
4.`Functor f => (a -> b) -> f a -> f b`

哪个代码片段完成下一个 `Functor` 实例？

```haskell
data Container x = Things x [x]

instance Functor Container where
  ????
```

1.`fmap f (Things x ys) = Things (f x) [f x]`
2.`fmap f (Things x ys) = Things (f x) (map f ys)`
3.`fmap f (Things x ys) = Things (f x) ys`
4.`fmap f (Things x ys) = f (Things x ys)`

`[a]` 的 Kind 是什么？

1.`*`
2.`* -> *`
3.`[a]`

`Foo` 的 Kind 是什么？

```haskell
data Foo x = FooConst
```

1.`*`
2.`* -> *`
3.`Foo`

`Bar` 的 Kind 是什么？

```haskell
data Bar = Baz | Qux Int
```

1.`*`
2.`* -> *`
3.`Bar`

`foldr (-) 1 (Just 2)` 的值是多少？

1.-1
2.1
3.`Just -1`
4.`Just 1`

哪个代码片段完成下一个 `Foldable` 实例？

```haskell
data Container x = Things x [x]

instance Foldable Container where
  ????
```

1.`foldr f z (Things x ys) = f x z`
2.`foldr f z (Things x ys) = foldr f x ys`
3.`foldr f z (Things x ys) = f x (foldr f z ys)`
4.`foldr f z (Things x ys) = foldr f z (x:ys)`


## 12.8 练习

- [Set12](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set12.hs)
