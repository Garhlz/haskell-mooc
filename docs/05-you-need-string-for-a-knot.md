# 第 5 讲：用类型打结

- 类型系统
- 定义自定义类型


## 5.1 代数数据类型

Haskell 使用“代数数据类型”来定义新类型。这个名字听起来很高级，但概念本身相当简单。先来看一些熟悉类型在标准库中的定义：

```haskell
data Bool = True | False
data Ordering = LT | EQ | GT
```

你也可以用这种语法定义自己的类型：

```haskell
-- 定义一个有三个值的类型
data Color = Red | Green | Blue

-- 一个对新类型使用模式匹配的函数
rgb :: Color -> [Double]
rgb Red = [1,0,0]
rgb Green = [0,1,0]
rgb Blue = [0,0,1]
```

```haskell
Prelude> :t Red
Red :: Color
Prelude> :t [Red,Blue,Green]
[Red,Blue,Green] :: [Color]
Prelude> rgb Red
[1.0,0.0,0.0]
```

### 5.1.1 字段

像 `Bool`、`Ordering` 和 `Color` 这样只列出一组常量的类型，在 Haskell 和其他语言中都称为*枚举*。枚举很有用，但我们还需要能携带数据的类型。这里定义一个报告类型，它包含 ID、标题和正文：

```haskell
data Report = ConstructReport Int String String
```

可以这样创建报告：

```haskell
Prelude> :t ConstructReport 1 "Title" "This is the body."
ConstructReport 1 "Title" "This is the body." :: Report
```

你可以通过模式匹配访问字段：

```haskell
reportContents :: Report -> String
reportContents (ConstructReport id title contents) = contents
setReportContents :: String -> Report -> Report
setReportContents contents (ConstructReport id title _contents) = ConstructReport id title contents
```

### 5.1.2 构造函数

`data` 声明右侧的内容称为*构造函数*。`True`、`False`、`Red` 和 `ConstructReport` 都是构造函数的示例。一个类型可以有多个构造函数，一个构造函数可以有零个或多个字段。

下面是标准扑克牌的数据类型。它有 5 个构造函数，其中 `Joker` 有 0 个字段，其他构造函数各有 1 个字段。

```haskell
data Card = Joker | Heart Int | Club Int | Spade Int | Diamond Int
```

带有字段的构造函数具有函数类型，并且可以在任何函数可以使用的地方使用：

```haskell
Prelude> :t Heart
Heart :: Int -> Card
Prelude> :t Club
Club :: Int -> Card
Prelude> map Heart [1,2,3]
[Heart 1,Heart 2,Heart 3]
Prelude> (Heart . (\x -> x+1)) 3
Heart 4
```

### 5.1.3 附注：推导

顺便说一句，我们的 `Card` 类型缺少一些东西。看看它与 `Ordering` 和 `Bool` 相比表现如何：

```haskell
Prelude> EQ
EQ
Prelude> True
True
Prelude> Joker
<interactive>:1:0:
    No instance for (Show Card)
      arising from a use of `print' at <interactive>:1:0-4
    Possible fix: add an instance declaration for (Show Card)
    In a stmt of a 'do' expression: print it
```

问题是 Haskell 不知道如何打印我们自己定义的类型。正如错误所说，这个类型还不是 `Show` 类型类的实例。简单的解决方案是在类型定义后添加 `deriving Show`：

```haskell
data Card = Joker | Heart Int | Club Int | Spade Int | Diamond Int
  deriving Show
```

```haskell
Prelude> Joker
Joker
```

`deriving` 语法可以自动让你的类型成为某些基本类型类的实例，最值得注意的是 `Read`、`Show` 和 `Eq`。稍后我们将详细讨论这意味着什么。

### 5.1.4 代数？

那么为什么这些数据类型被称为代数呢？这是因为，从理论上讲，每个数据类型都可以是构造函数的“和”，而每个构造函数都是字段的“乘积”。将它们视为和与积是有意义的，原因有很多，其中之一是我们可以通过这种方式计算每种类型的可能值：

```haskell
data Bool = True | False            -- corresponds to 1+1. Has 2 possible values.
data TwoBools = TwoBools Bool Bool  -- corresponds to Bool*Bool, i.e. 2*2. Has 4 possible values.
data Complex = Two Bool Bool | One Bool | None
                                    -- 对应 Bool*Bool+Bool+1 = 2*2+2+1 = 7。有 7 个可能的值。
```

代数数据类型有丰富的理论。如果你有兴趣，可以在 [这里](https://codewords.recurse.com/issues/three/algebra-and-calculus-of-algebraic-data-types) 或 [这里](https://www.cis.upenn.edu/~sweirich/papers/yorgey-thesis.pdf) 找到更多信息。


## 5.2 类型参数

我们在第 2 讲介绍列表时介绍了类型参数和参数多态性。从那时起，我们看到了其他参数化类型，例如 `Maybe` 和 `Either`。现在我们将学习如何定义我们自己的参数化类型。

### 5.2.1 定义参数化类型

`Maybe` 的定义是：

```haskell
data Maybe a = Nothing | Just a
```

`a` 是什么？我们通过在 `=` 符号左侧提及*类型变量*（本例中为 `a`）来定义参数化类型。然后我们可以在构造函数的字段中使用相同类型的变量。这类似于多态函数。与其定义单独的函数

```haskell
headInt :: [Int] -> Int
headBool :: [Bool] -> Bool
```

一样，我们定义了一个适用于所有类型 `a` 的函数 `head :: [a] -> a`。同样，与其定义多个类型

```haskell
data MaybeInt = NothingInt | JustInt Int
data MaybeBool = NothingBool | JustBool Bool
```

我们定义了一种适用于所有类型 `a` 的类型 `Maybe a`。

这是我们的第一个参数化类型 `Described`。`Described a` 类型的值包含 `a` 类型的值和 `String` 描述。

```haskell
data Described a = Describe a String

getValue :: Described a -> a
getValue (Describe x _) = x

getDescription :: Described a -> String
getDescription (Describe _ desc) = desc
```

```haskell
Prelude> :t Describe
Describe :: a -> String -> Described a
Prelude> :t Describe True "This is true"
Describe True "This is true" :: Described Bool
Prelude> getValue (Describe 3 "a number")
3
Prelude> getDescription (Describe 3 "a number")
"a number"
```

### 5.2.2 语法注释

在上面的定义中，我们使用 `a` 作为类型变量。但任何以小写字母开头的单词都可以。我们可以这样定义 `Maybe`：

```haskell
data Maybe theType = Nothing | Just theType
```

Haskell 标识符的规则是：

- 类型变量以及函数和值的名称以小写开头（例如 `a`、 `map`、 `xs`）
- 类型名称和构造函数名称以大写开头（例如 `Maybe`、 `Just`、 `Card`、 `Heart`）

请注意，类型及其构造函数可以具有相同的名称。对于只有一个构造函数的类型，这在 Haskell 代码中很常见。在本材料中，我们尽量避免使用它以避免混淆。下面是一些示例：

```haskell
data Pair a = Pair a a
data Report = Report Int String String
```

```haskell
Prelude> :t Pair
Pair :: a -> a -> Pair a
```

注意不要混淆类型和构造函数。幸运的是，类型和构造函数永远不会出现在同一上下文中，因此你会得到很好的错误提示：

```haskell
Prelude> Maybe                              -- 试图把类型名当作值使用
<interactive>:1:1: error:
    • Data constructor not in scope: Maybe

Prelude> undefined :: Nothing               -- 试图把构造函数当作类型使用
<interactive>:2:14: error:
    Not in scope: type constructor or class ‘Nothing’
```

### 5.2.3 附注：多种类型参数

类型可以有多个类型参数。语法类似于定义具有多个参数的函数。下面是标准 `Either` 类型的定义：

```haskell
data Either a b = Left a | Right b
```


## 5.3 递归类型

到目前为止，我们定义的所有类型大小都是固定的。我们可以表示一份报告或一种颜色，但如何表示一组事物呢？当然可以使用列表，不过我们能不能自己定义列表类型？

就像 Haskell 函数一样，Haskell 数据类型也可以是“递归”的。这并不比 Java 或 Python 中一个对象引用同一类的另一个对象更奇怪。下面是整数列表的定义方式：

```haskell
data IntList = Empty | Node Int IntList
  deriving Show

ihead :: IntList -> Int
ihead (Node i _) = i

itail :: IntList -> IntList
itail (Node _ t) = t

ilength :: IntList -> Int
ilength Empty = 0
ilength (Node _ t) = 1 + ilength t
```

我们可以使用上面定义的函数来处理整数列表：

```haskell
Prelude> ihead (Node 3 (Node 5 (Node 4 Empty)))
3
Prelude> itail (Node 3 (Node 5 (Node 4 Empty)))
Node 5 (Node 4 Empty)
Prelude> ilength (Node 3 (Node 5 (Node 4 Empty)))
3
```

请注意，我们不能将 `Int` 以外的值放入 `IntList` 中：

```haskell
Prelude> Node False Empty

<interactive>:3:6: error:
    • Couldn't match expected type ‘Int’ with actual type ‘Bool’
    • In the first argument of ‘Node’, namely ‘False’
      In the expression: Node False Empty
      In an equation for ‘it’: it = Node False Empty
```

为了能够将任何类型的元素放入列表中，让我们对类型参数执行相同的操作。这与内置类型 `[a]` 相同，但语法稍显笨拙：

```haskell
data List a = Empty | Node a (List a)
  deriving Show
```

请注意我们需要如何在递归中向前传递类型参数 `a`。我们需要编写 `Node a (List a)` 而不是 `Node a List`。 `Node` 构造函数有两个参数。第一个的类型为 `a`，第二个的类型为 `List a`。下面是 `List` 类型的一些标准列表函数的重新实现：

```haskell
lhead :: List a -> a
lhead (Node h _) = h

ltail :: List a -> List a
ltail (Node _ t) = t

lnull :: List a -> Bool
lnull Empty = True
lnull _     = False

llength :: List a -> Int
llength Empty = 0
llength (Node _ t) = 1 + llength t
```

```haskell
Prelude> lhead (Node True Empty)
True
Prelude> ltail (Node True (Node False Empty))
Node False Empty
Prelude> lnull Empty
True
```

请注意，就像普通的 Haskell 列表一样，我们不能在同一个列表中包含不同类型的元素：

```haskell
Prelude> Node True (Node "foo" Empty)

<interactive>:5:12: error:
    • Couldn't match type ‘[Char]’ with ‘Bool’
      Expected type: List Bool
        Actual type: List [Char]
    • In the second argument of ‘Node’, namely ‘(Node "foo" Empty)’
      In the expression: Node True (Node "foo" Empty)
      In an equation for ‘it’: it = Node True (Node "foo" Empty)
```

### 5.3.1 示例：种一棵树

就像列表一样，我们也可以表示二叉树：

```haskell
data Tree a = Node a (Tree a) (Tree a) | Empty
```

我们的树包含节点，其中包含 `a` 类型的值和两个子树，以及空树。

如果你不熟悉二叉树，它们是一种经常用作其他数据结构基础的数据结构（`Data.Map` 基于树！）。二叉树通常被绘制为（颠倒的）图片，如下所示：

![二叉树](img/binaryTree.svg)

树中的最高节点称为“根”（在本例中为 `0`），没有子节点的节点称为 `leaves` （在本例中为 `2`、 `3` 和 `4`）。我们可以使用 `Tree` 类型来定义这棵树，如下所示：

```haskell
example :: Tree Int
example = (Node 0 (Node 1 (Node 2 Empty Empty)
                          (Node 3 Empty Empty))
                  (Node 4 Empty Empty))
```

二叉树的高度是从根到叶子的最长路径的长度。用 Haskell 术语来说，就是构建树所需的 `Node` 构造函数的嵌套层数。我们示例树的高度为 3。下面是计算树高度的函数：

```haskell
treeHeight :: Tree a -> Int
treeHeight Empty = 0
treeHeight (Node _ l r) = 1 + max (treeHeight l) (treeHeight r)
```

```haskell
treeHeight Empty ==> 0
treeHeight (Node 2 Empty Empty)
  ==> 1 + max (treeHeight Empty) (treeHeight Empty)
  ==> 1 + max 0 0
  ==> 1
treeHeight (Node 1 Empty (Node 2 Empty Empty))
  ==> 1 + max (treeHeight Empty) (treeHeight (Node 2 Empty Empty))
  ==> 1 + max 0 1
  ==> 2
treeHeight (Node 0 (Node 1 Empty (Node 2 Empty Empty)) Empty)
  ==> 1 + max (treeHeight (Node 1 Empty (Node 2 Empty Empty))) (treeHeight Empty)
  ==> 1 + max 2 0
  ==> 3
```

如果你熟悉*二叉搜索树*，下面是二叉搜索树的查找和插入操作的定义。如果你不知道我在说什么，你就不需要理解这一点。

```haskell
lookup :: Int -> Tree Int -> Bool
lookup x Empty = False
lookup x (Node y l r)
  | x < y = lookup x l
  | x > y = lookup x r
  | otherwise = True

insert :: Int -> Tree Int -> Tree Int
insert x Empty = Node x Empty Empty
insert x (Node y l r)
  | x < y = Node y (insert x l) r
  | x > y = Node y l (insert x r)
  | otherwise = Node y l r
```


## 5.4 记录语法

如果需要经常访问某些字段，那么使用辅助函数来读取这些字段会很方便。例如，类型 `Person` 可能有多个字段：

```haskell
data Person = MkPerson String Int String String String deriving Show
```

人员列表可能如下所示：

```haskell
people :: [Person]
people = [ MkPerson "Jane Doe" 21 "Houston" "Texas" "Engineer"
         , MkPerson "Maija Meikäläinen" 35 "Rovaniemi" "Finland" "Engineer"
         , MkPerson "Mauno Mutikainen" 27 "Turku" "Finland" "Mathematician"
         ]
```

假设我们需要找到来自芬兰的所有工程师：

```haskell
query :: [Person] -> [Person]
query [] = []
query ((MkPerson name age town state profession):xs)
  | state == "Finland" && profession == "Engineer" =
      (MkPerson name age town state profession) : query xs
  | otherwise = query xs
```

因此，

```haskell
query people ==> [MkPerson "Maija Meikäläinen" 35 "Rovaniemi" "Finland" "Engineer"]
```

请注意，字段的类型几乎没有提供有关这些字段中的预期内容的信息。我们需要在代码中的所有位置记住 `town` 位于 `state` 之前，反之亦然。

Haskell 有一个称为记录语法（record syntax）的特性，在此类情况下非常有用。数据类型 `Person` 可以定义为一条记录：

```haskell
data Person = MkPerson { name :: String, age :: Int, town :: String, state :: String, profession :: String}
  deriving Show
```

我们仍然可以正常定义 `Person` 的值，但 `Show` 实例会为我们打印字段名：

```haskell
Prelude> MkPerson "Jane Doe" 21 "Houston" "Texas" "Engineer"
MkPerson {name = "Jane Doe", age = 21, town = "Houston", state = "Texas", profession = "Engineer"}
```

但是，我们也可以使用记录语法来定义值。请注意，既然字段已经有了名称，那么它们就不需要按任何特定的顺序排列。

```haskell
Prelude> MkPerson {name = "Jane Doe", town = "Houston", profession = "Engineer", state = "Texas", age = 21}
MkPerson {name = "Jane Doe", age = 21, town = "Houston", state = "Texas", profession = "Engineer"}
```

最重要的是，我们免费获得字段的“访问器函数”：

```haskell
Prelude> :t profession
profession :: Person -> String
Prelude> profession (MkPerson "Jane Doe" 21 "Houston" "Texas" "Engineer")
"Engineer"
```

我们现在可以使用这些访问器函数重写查询函数：

```haskell
query :: [Person] -> [Person]
query []     = []
query (x:xs)
  | state x == "Finland" && profession x == "Engineer" =
      x : query xs
  | otherwise = query xs
```

你可能会同意代码现在看起来更令人愉快。


## 5.5 代数数据类型：总结

- 类型定义如下

```haskell
data TypeName = ConstructorName FieldType FieldType2 | AnotherConstructor FieldType3 | OneMoreCons
```

- ...或者像这样如果我们使用类型变量

```haskell
data TypeName variable = Cons1 variable Type1 | Cons2 Type2 variable
```

- 你可以有一个或多个构造函数
- 每个构造函数可以有零个或多个字段
- 构造函数以大写字母开头，类型变量以小写字母开头
- 值通过模式匹配进行处理：

```haskell
foo (ConstructorName a b) = a+b
foo (AnotherConstructor _) = 0
foo OneMoreCons = 7
```

- 构造函数只是函数：

```haskell
ConstructorName :: FieldType -> FieldType2 -> TypeName
Cons1 :: a -> Type1 -> TypeName a
```

- 你还可以使用记录语法定义数据类型：

```haskell
data TypeName = Constructor { field1 :: Field1Type, field2 :: Field2Type }
```

这样会自动为你生成 `field1 :: TypeName -> Field1Type` 这样的访问器函数。


## 5.6 附注：定义类型的其他方式

除了 `data` 关键字之外，Haskell 中还有两种定义类型的方法。

`newtype` 关键字的工作方式类似于 `data`，但它只能有一个构造函数，并且这个构造函数只能有一个字段。有时出于性能原因使用 `newtype` 是合理的，不过我们会在第 2 部分再回到这些内容。

`type` 关键字引入了*类型别名*。类型别名不会影响类型检查，它们只是提供编写类型的简写。例如，熟悉的 `String` 类型是 `[Char]` 的别名：

```haskell
type String = [Char]
```

这意味着每当编译器读取 `String` 时，它都会立即将其替换为 `[Char]`。类型别名看起来很有用，但它们很容易使读取类型错误变得更加困难。


## 5.7 代数数据类型如何工作？

还记得列表在内存中是如何表示为链表的吗？让我们更详细地了解代数数据类型在内存中的样子。

Haskell 数据在内存中形成“有向图”。每个构造函数都是一个节点，每个字段都是一条边。（变量的）名称是指向该图的指针。不同的名称可以*共享*部分结构。下面是一个列表示例。请注意，`x` 的最后两个元素如何与 `y` 和 `z` 共享。

```haskell
let x = [1,2,3,4]
    y = drop 2 x
    z = 5:y
```

![有向无环图 1](img/DAG1.svg)

当你创建数据结构的新版本时发生的情况称为“路径复制”。由于 Haskell 数据是不可变的，数据结构的更改部分会被复制，而未更改的部分可以在新旧版本之间共享。

考虑 `++` 的定义：

```haskell
[]     ++ ys = ys
(x:xs) ++ ys = x:(xs ++ ys)
```

当我们遍历第一个参数时，我们正在复制它。对于第一个输入列表中的每个 `:` 构造函数，我们在输出列表中创建一个新的 `:` 构造函数。第二个参数可以被共享——在递归中根本不使用它。视觉上：

![有向无环图 2](img/DAG2.svg)

另一种思考方式是：我们想要更改列表元素 `(3:)` 的 `tail` 指针。这意味着我们需要制作一个新的 `(3:)`。然而，`(2:)` 指向 `(3:)`，因此我们还需要 `(2:)` 的新副本。对于 `(1:)` 也是如此。

我们在使用列表时得到的图表相当简单。作为一个更复杂的示例，以下是当我们运行本讲座前面的二叉树插入示例时内存中发生的情况。

```haskell
insert :: Int -> Tree Int -> Tree Int
insert x Empty = Node x Empty Empty
insert x (Node y l r)
  | x < y = Node y (insert x l) r
  | x > y = Node y l (insert x r)
  | otherwise = Node y l r
```

![有向无环图 3](img/DAG3.svg)

请注意，旧树和新树如何与 3 和 4 共享子树，因为它没有更改，但“更改”的节点 7 及其上面的所有节点都被复制。


## 5.8 测验

为什么我们不能 `map Nothing`？

1.  因为 `Nothing` 不带参数
2.  因为 `Nothing` 什么也没返回
3.  因为`Nothing`是一个构造函数。

如果我们定义 `data Boing = Frick String Boing (Int -> Bool)`，那么 `Frick` 的类型是什么？

1.  `Boing`
2.  `String -> Boing -> Int -> Bool -> Boing`
3.  `String -> Boing -> (Int -> Bool) -> Boing`

如果我们定义 `data ThreeLists a b c = ThreeLists [a] [b] [c]`，那么构造函数 `ThreeLists` 的类型是什么？

1.  `[a] -> [b] -> [c] -> ThreeLists`
2.  `a -> b -> c -> ThreeLists a b c`
3.  `[a] -> [b] -> [c] -> ThreeLists a b c`
4.  `[a] -> [b] -> [c] -> ThreeLists [a] [b] [c]`

如果我们定义 `data TwoLists a b = TwoList {aList :: [a], bList :: [b]}`，那么函数 `aList` 的类型是什么？

1.  `aList` 不是一个函数，它是一个字段
2.  `TwoLists a b -> [a]`
3.  `[a] -> TwoLists a b`
4.  `[a]`


## 5.9 练习

- [Set5a](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set5a.hs)：使用和定义代数数据类型
- [Set5b](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set5b.hs)：玩转二叉树
