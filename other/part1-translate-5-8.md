- [5 第 5 讲：打结需要 String](#lecture-5-you-need-string-for-a-knot)
  - [5.1 代数数据类型](#algebraic-datatypes)
  - [5.2 类型参数](#type-parameters)
  - [5.3 递归类型](#recursive-types)
  - [5.4 记录语法](#record-syntax)
  - [5.5 代数数据类型：总结](#algebraic-datatypes-summary)
  - [5.6 附注：定义类型的其他方式](#sidenote-other-ways-of-defining-types)
  - [5.7 代数数据类型如何工作？](#how-do-algebraic-datatypes-work)
  - [5.8 测验](#quiz-4)
  - [5.9 练习](#exercises-4)
- [6 第 6 讲：类型类英雄](#lecture-6-working-class-hero)
  - [6.1 类和实例的语法](#syntax-of-classes-and-instances)
  - [6.2 默认实现](#default-implementations)
  - [6.3 有用的内容](#useful-stuff)
  - [6.4 层级结构](#hierarchies)
  - [6.5 小测验](#quiz-5)
  - [6.6 练习](#exercises-5)
- [7 第 7 讲：新的星座](#lecture-7-new-constellations)
  - [7.1 用盒子建模](#modeling-with-boxes)
  - [7.2 用不同情况建模](#modeling-with-cases)
  - [7.3 幺半群](#monoids)
  - [7.4 开放和封闭抽象](#open-and-closed-abstractions)
  - [7.5 用语言建模](#modeling-with-languages)
  - [7.6 练习](#exercises-6)
- [8 第 8 讲：回味](#lecture-8-the-aftertaste)
  - [8.1 IO 初体验](#a-taste-of-io)
  - [8.2 总结](#summary)
  - [8.3 接下来做什么？](#what-next)
  - [8.4 最终项目：图形](#final-project-graphics)
  - [8.5 致谢](#acknowledgements)


# Haskell 慕课，第 1 部分

# 5 第 5 讲：打结需要 String

- 类型系统
- 定义自定义类型

## 5.1 代数数据类型

Haskell 有一个称为“代数数据类型”的系统，用于定义新类型。这听起来很花哨，但相当简单。让我们深入研究一些熟悉类型的标准库定义：

``` haskell
data Bool = True | False
data Ordering = LT | EQ | GT
```

使用此语法，你也可以定义类型：

``` haskell
-- definition of a type with three values
data Color = Red | Green | Blue

-- a function that uses pattern matching on our new type
rgb :: Color -> [Double]
rgb Red = [1,0,0]
rgb Green = [0,1,0]
rgb Blue = [0,0,1]
```

``` haskell
Prelude> :t Red
Red :: Color
Prelude> :t [Red,Blue,Green]
[Red,Blue,Green] :: [Color]
Prelude> rgb Red
[1.0,0.0,0.0]
```

### 5.1.1 字段

像 `Bool`、 `Ordering` 和 `Color` 这样只列出一堆常量的类型在 Haskell 和其他语言中被称为 *enumerations* 或 *enums*。枚举很有用，但你还需要其他类型。这里我们定义一个包含 ID 号、标题和正文的报告类型：

``` haskell
data Report = ConstructReport Int String String
```

这是创建报告的方法：

``` haskell
Prelude> :t ConstructReport 1 "Title" "This is the body."
ConstructReport 1 "Title" "This is the body." :: Report
```

你可以通过模式匹配访问字段：

``` haskell
reportContents :: Report -> String
reportContents (ConstructReport id title contents) = contents
setReportContents :: String -> Report -> Report
setReportContents contents (ConstructReport id title _contents) = ConstructReport id title contents
```

### 5.1.2 构造函数

`data` 声明右侧的内容称为*构造函数*。  `True`、 `False`、 `Red` 和 `ConstructReport` 都是构造函数的示例。一个类型可以有多个构造函数，一个构造函数可以有零个或多个字段。

这是标准扑克牌的数据类型。它有 5 个构造函数，其中 `Joker` 有 0 个字段，其他有 1 个字段。

``` haskell
data Card = Joker | Heart Int | Club Int | Spade Int | Diamond Int
```

带有字段的构造函数具有函数类型，并且可以在任何函数可以使用的地方使用：

``` haskell
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

顺便说一句，我们的 `Card` 类型缺少一些东西。看看它与 `Ordering` 和 `Bool` 相比的表现如何：

``` haskell
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

问题是 Haskell 不知道如何打印我们定义的类型。正如错误所述，它们不属于 `Show` 类。简单的解决方案是在类型定义后添加 `deriving Show`：

``` haskell
data Card = Joker | Heart Int | Club Int | Spade Int | Diamond Int
  deriving Show
```

``` haskell
Prelude> Joker
Joker
```

`deriving` 语法是一种自动让你的类型成为某些基本类型类的实例的方法，最值得注意的是 `Read`、 `Show` 和 `Eq`。稍后我们将详细讨论这意味着什么。

### 5.1.4 代数？

那么为什么这些数据类型被称为代数呢？这是因为，从理论上讲，每个数据类型都可以是构造函数的“和”，而每个构造函数都是字段的“乘积”。将它们视为和与积是有意义的，原因有很多，其中之一是我们可以通过这种方式计算每种类型的可能值：

``` haskell
data Bool = True | False            -- corresponds to 1+1. Has 2 possible values.
data TwoBools = TwoBools Bool Bool  -- corresponds to Bool*Bool, i.e. 2*2. Has 4 possible values.
data Complex = Two Bool Bool | One Bool | None
                                    -- corresponds to Bool*Bool+Bool+1 = 2*2+2+1 = 7. Has 7 possible values.
```

代数数据类型有丰富的理论。如果你有兴趣，你可能会找到更多信息 [here](https://codewords.recurse.com/issues/three/algebra-and-calculus-of-algebraic-data-types) 或 [here](https://www.cis.upenn.edu/~sweirich/papers/yorgey-thesis.pdf)。

## 5.2 类型参数

我们在第 2 讲介绍列表时介绍了类型参数和参数多态性。从那时起，我们看到了其他参数化类型，例如 `Maybe` 和 `Either`。现在我们将学习如何定义我们自己的参数化类型。

### 5.2.1 定义参数化类型

`Maybe` 的定义是：

``` haskell
data Maybe a = Nothing | Just a
```

`a` 是什么？我们通过在 `=` 符号左侧提及*类型变量*（本例中为 `a`）来定义参数化类型。然后我们可以在构造函数的字段中使用相同类型的变量。这类似于多态函数。而不是定义单独的函数

``` haskell
headInt :: [Int] -> Int
headBool :: [Bool] -> Bool
```

依此类推，我们定义了一个适用于所有类型 `a` 的函数 `head :: [a] -> a`。同样，不定义多个类型

``` haskell
data MaybeInt = NothingInt | JustInt Int
data MaybeBool = NothingBool | JustBool Bool
```

我们定义了一种适用于所有类型 `a` 的类型 `Maybe a`。

这是我们的第一个参数化类型 `Described`。 `Described a` 类型的值包含 `a` 类型的值和 `String` 描述。

``` haskell
data Described a = Describe a String

getValue :: Described a -> a
getValue (Describe x _) = x

getDescription :: Described a -> String
getDescription (Describe _ desc) = desc
```

``` haskell
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

在上面的定义中，我们使用 `a` 作为类型变量。但是任何以小写字母开头的单词都可以。我们可以这样定义 `Maybe`：

``` haskell
data Maybe theType = Nothing | Just theType
```

Haskell 标识符的规则是：

- 类型变量以及函数和值的名称以小写开头（例如 `a`、 `map`、 `xs`）
- 类型名称和构造函数名称以大写开头（例如 `Maybe`、 `Just`、 `Card`、 `Heart`）

请注意，类型及其构造函数可以具有相同的名称。对于只有一个构造函数的类型，这在 Haskell 代码中很常见。在本材料中，我们尽量避免使用它以避免混淆。以下是一些示例：

``` haskell
data Pair a = Pair a a
data Report = Report Int String String
```

``` haskell
Prelude> :t Pair
Pair :: a -> a -> Pair a
```

注意不要混淆类型和构造函数。幸运的是，类型和构造函数永远不会出现在同一上下文中，因此你会得到一个很好的错误：

``` haskell
Prelude> Maybe                              -- trying to use a type name as a value
<interactive>:1:1: error:
    • Data constructor not in scope: Maybe

Prelude> undefined :: Nothing               -- trying to use a constructor as a type
<interactive>:2:14: error:
    Not in scope: type constructor or class ‘Nothing’
```

### 5.2.3 附注：多种类型参数

类型可以有多个类型参数。语法类似于定义具有多个参数的函数。以下是标准 `Either` 类型的定义：

``` haskell
data Either a b = Left a | Right b
```

## 5.3 递归类型

到目前为止，我们定义的所有类型的大小都是恒定的。我们可以代表一份报告或一种颜色，但我们如何才能代表一组事物呢？我们当然可以使用列表，但是我们可以自己定义列表类型吗？

就像 Haskell 函数一样，Haskell 数据类型可以是“递归”的。这并不比 Java 或 Python 中的一个对象引用同一类的另一个对象更奇怪。这是定义整数列表的方法：

``` haskell
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

``` haskell
Prelude> ihead (Node 3 (Node 5 (Node 4 Empty)))
3
Prelude> itail (Node 3 (Node 5 (Node 4 Empty)))
Node 5 (Node 4 Empty)
Prelude> ilength (Node 3 (Node 5 (Node 4 Empty)))
3
```

请注意，我们不能将 `Int` 以外的值放入 `IntList` 中：

``` haskell
Prelude> Node False Empty

<interactive>:3:6: error:
    • Couldn't match expected type ‘Int’ with actual type ‘Bool’
    • In the first argument of ‘Node’, namely ‘False’
      In the expression: Node False Empty
      In an equation for ‘it’: it = Node False Empty
```

为了能够将任何类型的元素放入列表中，让我们对类型参数执行相同的操作。这与内置类型 `[a]` 相同，但语法稍显笨拙：

``` haskell
data List a = Empty | Node a (List a)
  deriving Show
```

请注意我们需要如何在递归中向前传递类型参数 `a`。我们需要编写 `Node a (List a)` 而不是 `Node a List`。 `Node` 构造函数有两个参数。第一个的类型为 `a`，第二个的类型为 `List a`。以下是 `List` 类型的一些标准列表函数的重新实现：

``` haskell
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

``` haskell
Prelude> lhead (Node True Empty)
True
Prelude> ltail (Node True (Node False Empty))
Node False Empty
Prelude> lnull Empty
True
```

请注意，就像普通的 Haskell 列表一样，我们不能在同一个列表中包含不同类型的元素：

``` haskell
Prelude> Node True (Node "foo" Empty)

<interactive>:5:12: error:
    • Couldn't match type ‘[Char]’ with ‘Bool’
      Expected type: List Bool
        Actual type: List [Char]
    • In the second argument of ‘Node’, namely ‘(Node "foo" Empty)’
      In the expression: Node True (Node "foo" Empty)
      In an equation for ‘it’: it = Node True (Node "foo" Empty)
```

### 5.3.1 示例：种植一棵树

就像列表一样，我们也可以表示二叉树：

``` haskell
data Tree a = Node a (Tree a) (Tree a) | Empty
```

我们的树包含节点，其中包含 `a` 类型的值和两个子树，以及空树。

如果你不熟悉二叉树，它们是一种经常用作其他数据结构基础的数据结构（`Data.Map` 基于树！）。二叉树通常被绘制为（颠倒的）图片，如下所示：

![](img/binaryTree.svg)

树中的最高节点称为“根”（在本例中为 `0`），没有子节点的节点称为 `leaves` （在本例中为 `2`、 `3` 和 `4`）。我们可以使用 `Tree` 类型来定义这棵树，如下所示：

``` haskell
example :: Tree Int
example = (Node 0 (Node 1 (Node 2 Empty Empty)
                          (Node 3 Empty Empty))
                  (Node 4 Empty Empty))
```

二叉树的高度是从根到叶子的最长路径的长度。用 Haskell 术语来说，就是构建树所需的 `Node` 构造函数的嵌套层数。我们示例树的高度为 3。下面是计算树高度的函数：

``` haskell
treeHeight :: Tree a -> Int
treeHeight Empty = 0
treeHeight (Node _ l r) = 1 + max (treeHeight l) (treeHeight r)
```

``` haskell
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

如果你熟悉*二叉搜索树*，这里是二叉搜索树的查找和插入操作的定义。如果你不知道我在说什么，你就不需要理解这一点。

``` haskell
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

``` haskell
data Person = MkPerson String Int String String String deriving Show
```

人员列表可能如下所示：

``` haskell
people :: [Person]
people = [ MkPerson "Jane Doe" 21 "Houston" "Texas" "Engineer"
         , MkPerson "Maija Meikäläinen" 35 "Rovaniemi" "Finland" "Engineer"
         , MkPerson "Mauno Mutikainen" 27 "Turku" "Finland" "Mathematician"
         ]
```

假设我们需要找到来自芬兰的所有工程师：

``` haskell
query :: [Person] -> [Person]
query [] = []
query ((MkPerson name age town state profession):xs)
  | state == "Finland" && profession == "Engineer" =
      (MkPerson name age town state profession) : query xs
  | otherwise = query xs
```

因此，

``` haskell
query people ==> [MkPerson "Maija Meikäläinen" 35 "Rovaniemi" "Finland" "Engineer"]
```

请注意，字段的类型几乎没有提供有关这些字段中的预期内容的信息。我们需要在代码中的所有位置记住 `town` 位于 `state` 之前，反之亦然。

Haskell 有一个称为“记录语法”的函数，在此类情况下非常有用。数据类型 `Person` 可以定义为一条记录：

``` haskell
data Person = MkPerson { name :: String, age :: Int, town :: String, state :: String, profession :: String}
  deriving Show
```

我们仍然可以正常定义 `Person` 的值，但 `Show` 实例会为我们打印字段名称：

``` haskell
Prelude> MkPerson "Jane Doe" 21 "Houston" "Texas" "Engineer"
MkPerson {name = "Jane Doe", age = 21, town = "Houston", state = "Texas", profession = "Engineer"}
```

但是，我们也可以使用记录语法来定义值。请注意，既然字段已经有了名称，那么它们就不需要按任何特定的顺序排列。

``` haskell
Prelude> MkPerson {name = "Jane Doe", town = "Houston", profession = "Engineer", state = "Texas", age = 21}
MkPerson {name = "Jane Doe", age = 21, town = "Houston", state = "Texas", profession = "Engineer"}
```

最重要的是，我们免费获得字段的“访问器函数”：

``` haskell
Prelude> :t profession
profession :: Person -> String
Prelude> profession (MkPerson "Jane Doe" 21 "Houston" "Texas" "Engineer")
"Engineer"
```

我们现在可以使用这些访问器函数重写查询函数：

``` haskell
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

``` haskell
data TypeName = ConstructorName FieldType FieldType2 | AnotherConstructor FieldType3 | OneMoreCons
```

- ...或者像这样如果我们使用类型变量

``` haskell
data TypeName variable = Cons1 variable Type1 | Cons2 Type2 variable
```

- 你可以有一个或多个构造函数
- 每个构造函数可以有零个或多个字段
- 构造函数以大写字母开头，类型变量以小写字母开头
- 值通过模式匹配进行处理：

``` haskell
foo (ConstructorName a b) = a+b
foo (AnotherConstructor _) = 0
foo OneMoreCons = 7
```

- 构造函数只是函数：

``` haskell
ConstructorName :: FieldType -> FieldType2 -> TypeName
Cons1 :: a -> Type1 -> TypeName a
```

- 你还可以使用记录语法定义数据类型：

``` haskell
data TypeName = Constructor { field1 :: Field1Type, field2 :: Field2Type }
```

这为你免费提供了 `field1 :: TypeName -> Field1Type` 等访问器函数。

## 5.6 附注：定义类型的其他方式

除了 `data` 关键字之外，Haskell 中还有两种定义类型的方法。

`newtype` 关键字的工作方式类似于 `data`，但只能有一个带有单个字段的构造函数。有时出于性能原因使用 `newtype` 是明智的，但我们将在第 2 部分中回顾这些内容。

`type` 关键字引入了*类型别名*。类型别名不会影响类型检查，它们只是提供编写类型的简写。例如，熟悉的 `String` 类型是 `[Char]` 的别名：

``` haskell
type String = [Char]
```

这意味着每当编译器读取 `String` 时，它都会立即将其替换为 `[Char]`。类型别名看起来很有用，但它们很容易使读取类型错误变得更加困难。

## 5.7 代数数据类型如何工作？

还记得列表在内存中是如何表示为链表的吗？让我们更详细地了解代数数据类型在内存中的样子。

Haskell 数据在内存中形成“有向图”。每个构造函数都是一个节点，每个字段都是一条边。 （变量的）名称是指向该图的指针。不同的名称可以*共享*部分结构。这是一个带有列表的示例。请注意 `x` 的最后两个元素如何与 `y` 和 `z` 共享。

``` haskell
let x = [1,2,3,4]
    y = drop 2 x
    z = 5:y
```

![](img/DAG1.svg)

当你创建数据结构的新版本时发生的情况称为“路径复制”。由于 Haskell 数据是不可变的，数据结构的更改部分会被复制，而未更改的部分可以在新旧版本之间共享。

考虑 `++` 的定义：

``` haskell
[]     ++ ys = ys
(x:xs) ++ ys = x:(xs ++ ys)
```

当我们遍历第一个参数时，我们正在复制它。对于第一个输入列表中的每个 `:` 构造函数，我们在输出列表中创建一个新的 `:` 构造函数。第二个论点可以分享。在递归中根本不使用它。视觉上：

![](img/DAG2.svg)

另一种思考方式是：我们想要更改列表元素 `(3:)` 的 `tail` 指针。这意味着我们需要制作一个新的 `(3:)`。然而，`(2:)` 指向 `(3:)`，因此我们还需要 `(2:)` 的新副本。对于 `(1:)` 也是如此。

我们在使用列表时得到的图表相当简单。作为一个更复杂的示例，以下是当我们运行本讲座前面的二叉树插入示例时内存中发生的情况。

``` haskell
insert :: Int -> Tree Int -> Tree Int
insert x Empty = Node x Empty Empty
insert x (Node y l r)
  | x < y = Node y (insert x l) r
  | x > y = Node y l (insert x r)
  | otherwise = Node y l r
```

![](img/DAG3.svg)

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

# 6 第 6 讲：类型类英雄

我们已经在类型中看到了像 `Eq a =>` 这样的类约束。我们知道如何将现有类与现有类型一起使用。但是我们如何将现有的类与我们自己的类型一起使用呢？我们如何定义自己的类？

以下是如何使你自己的类型成为 `Eq` 类的成员：

``` haskell
data Color = Black | White

instance Eq Color where
  Black == Black  = True
  White == White  = True
  _     == _      = False
```

类实例是一个 `instance` 块，其中包含该类中函数的定义。这里我们定义了 `==` 如何在 `Color` 上工作。

## 6.1 类和实例的语法

类型类使用 `class` 语法定义。类中的函数是给定类型的。这是一个包含一个函数 `size` 的类 `Size`：

``` haskell
class Size a where
  size :: a -> Int
```

类的实例是用我们刚刚看到的 `instance` 语法定义的。以下是我们如何使 `Int` 和 `[a]` 成为 `Size` 类的成员：

``` haskell
instance Size Int where
  size x = abs x

instance Size [a] where
  size xs = length xs
```

我们的类 `Size` 的行为就像现有的类型类一样。我们可以在任何可以使用函数的地方使用 `size`，并且 Haskell 可以为我们推断具有 `Size` 约束的类型：

``` haskell
Prelude> :t size
size :: Size a => a -> Int
Prelude> size [True,False]
2
Prelude> sizeBoth a b = [size a, size b]
Prelude> :t sizeBoth
sizeBoth :: (Size a1, Size a2) => a1 -> a2 -> [Int]
```

一个类可以包含多个函数，甚至常量。这里我们定义了新版本的`Size`类，内容更多。

``` haskell
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

你是否注意到在前面的示例中我们如何在两个实例中为 `sameSize` 提供相同的定义？这是很常见的情况，这就是 Haskell 类可以有*默认实现*的原因。作为第一个示例，这里有一个 `Example` 类型类，用于给出类型的示例值。

``` haskell
class Example a where
  example :: a           -- the main example for the type `a`
  examples :: [a]        -- a short list of examples
  examples = [example]   -- ...defaulting to just the main example

instance Example Int where
  example = 1
  examples = [0,1,2]

instance Example Bool where
  example = True
```

以下是 `Example` 的工作原理。请注意 `examples` 的默认实现如何在 `Bool` 情况下使用，但在 `Int` 情况下不使用。另请注意，需要显式类型签名来告诉 GHCi 我们对哪个实例感兴趣。如果没有它们，我们将收到“不明确的类型变量”错误。

``` haskell
Prelude> example :: Bool
True
Prelude> example :: Int
1
Prelude> examples :: [Bool]
[True]
Prelude> examples :: [Int]
[0,1,2]
```

标准类型类使用大量默认实现来简化类的实现。以下是 `Eq` 的标准定义（为了便于阅读而格式化）。

``` haskell
class Eq a where
  (==) ::  a -> a -> Bool
  x == y  = not (x /= y)

  (/=) ::  a -> a -> Bool
  x /= y  = not (x == y)
```

请注意这两个操作如何具有彼此的默认实现。这意味着我们可以定义一个完全没有内容的 `Eq` 实例，但生成的函数将永远递归。在实践中，我们希望至少定义 `==` 和 `/=` 之一。

当有很多默认实现时，可能很难知道你需要自己实现哪些函数。因此，类文档通常会提到“最小完整定义”。对于 `Eq`、 [the docs say](https://hackage.haskell.org/package/base-4.16.4.0/docs/Prelude.html#t:Eq) “最小完整定义：== 或 /=。”

接下来我们看`Ord`。  `Ord` 有 7 个操作，彼此之间都有默认实现。顺便说一下，请注意一次定义多个类型签名的奇怪方式。没关系，这是 Haskell 的一个特性，这就是 `Ord` 在 [the standard](https://www.haskell.org/onlinereport/haskell2010/haskellch6.html#x13-1270006.3) 中的定义。 （我们很快就会回到 `(Eq a) =>` 部分的含义。）

``` haskell
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

有了这个定义，真的很难知道最小完整定义是什么。幸运的是 [docs tell us](https://hackage.haskell.org/package/base-4.16.4.0/docs/Prelude.html#t:Ord) “最小完整定义：比较或 \<=。”

作为默认实现的最后一句话，如果不需要重写默认定义，则为了简单起见，可以将该函数移出类。考虑下面类似 `Combine` 的类：

``` haskell
class Combine a where
  combine :: a -> a -> a
  combine3 :: a -> a -> a -> a
  combine3 x y z = combine x (combine y z)
```

很难想象 `combine3` 会被赋予任何其他定义的情况，所以为什么不将它移出类：

``` haskell
class Combine a where
  combine :: a -> a -> a

combine3 :: Combine a => a -> a -> a -> a
combine3 x y z = combine x (combine y z)
```

作为示例，以下是简单对类型的 `Eq` 和 `Ord` 实例。请注意该定义如何通过仅定义 `==` 和 `<=` 来使用最小完整定义规则。

``` haskell
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

``` haskell
*Main> (IntPair 1 2) < (IntPair 2 3)
True
*Main> (IntPair 1 2) > (IntPair 2 3)
False
*Main> compare (IntPair 1 2) (IntPair 2 3)
LT
*Main Data.List> sort [IntPair 1 1,IntPair 1 4,IntPair 2 1,IntPair 2 2]
[IntPair 1 1,IntPair 1 4,IntPair 2 1,IntPair 2 2]
```

## 6.3 有用的东西

### 6.3.1 推导

正如我们已经多次看到的，`deriving` 是一种获取自动生成的类实例的方法。 `Read` 和 `Show` 类几乎总是应该派生以获得标准行为。 `Eq` 的派生实例通常就是你想要的。它需要构造函数和字段匹配。

派生的 `Ord` 实例可能不是你想要的。它从左到右对构造函数进行排序，然后从左到右比较构造函数内的字段。一个例子：

``` haskell
data Person = Dead | Alive String Int
  deriving (Show, Eq, Ord)
```

``` haskell
Prelude> Dead < Alive "Bob" 35                   -- constructors are ordered left-to-right
True
Prelude> Alive "Barbara" 35 < Alive "Clive" 17   -- names are compared before ages
True
Prelude> Alive "Clive" 17 < Alive "Clive" 30     -- finally, ages are compared if names match
True
```

### 6.3.2 向 GHCi 询问类的信息

你可以在 GHCi 中使用 `:info` 命令来获取类的内容和实例。如今，该信息甚至包括最小完整定义（请参阅 MINIMAL pragma）。例如：

``` haskell
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
    -- Defined in ‘GHC.Num’
instance Num Word -- Defined in ‘GHC.Num’
instance Num Integer -- Defined in ‘GHC.Num’
instance Num Int -- Defined in ‘GHC.Num’
instance Num Float -- Defined in ‘GHC.Float’
instance Num Double -- Defined in ‘GHC.Float’
```

## 6.4 层次结构

类和实例都可以形成*层次结构*。这意味着一个类或实例依赖于另一个类或实例。

### 6.4.1 实例层次结构

让我们尝试为简单的pair类型定义一个`Eq`实例：

``` haskell
data Pair a = MakePair a a
  deriving Show

instance Eq (Pair a) where
  (MakePair x y) == (MakePair a b)   =   x==a && y==b
```

``` haskell
error:
    • No instance for (Eq a) arising from a use of ‘==’
      Possible fix: add (Eq a) to the context of the instance declaration
    • In the first argument of ‘(&&)’, namely ‘x == a’
      In the expression: x == a && y == b
      In an equation for ‘==’:
          (MakePair x y) == (MakePair a b) = x == a && y == b
```

编译器试图告诉我们，我们的 `Eq (Pair a)` 实例需要 `Eq a` 实例才能工作。如果我们无法比较 `a` 类型的值，我们如何比较 `a` 类型的值对？为了解决这个问题，我们需要在实例声明中添加类型约束，就像我们在函数定义中添加类型约束一样。

``` haskell
instance Eq a => Eq (Pair a) where
  (MakePair x y) == (MakePair a b)   =   x==a && y==b
```

现在我们可以比较对，只要元素类型是可比较的。但是，我们无法比较函数对，因为函数没有 Eq 实例。

``` haskell
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

我们继续看另一个例子。这是一个简单的类型类和一个实例

``` haskell
class Check a where
  check :: a -> Bool

instance Check Int where
  check x = x > 0
```

现在我们可以编写一个检查列表的函数。我们使用标准库函数 `and :: [Bool] -> Bool` 来检查列表是否都是 `True`。

``` haskell
checkAll :: Check a => [a] -> Bool
checkAll xs = and (map check xs)
```

为了将其转换为 `Check [a]` 实例，我们需要向实例声明添加约束。我们的`Check [a]`是基于`Check a`实例的。

``` haskell
instance Check a => Check [a] where
  check xs = and (map check xs)
```

这意味着我们的`Check [a]`实例只有在有对应的`Check a`实例时才有效。例如，如果我们尝试调用 `Check [Bool]` 实例，我们会收到有关缺少 `Check Bool` 实例的错误：

``` haskell
Prelude> check [True,False]

<interactive>:1:1: error:
    • No instance for (Check Bool) arising from a use of ‘check’
    • In the expression: check [True, False]
      In an equation for ‘it’: it = check [True, False]
```

另外，如果我们尝试在没有约束的情况下定义 `Check [a]` 实例，我们会收到错误（有一个非常好的建议！）

``` haskell
    • No instance for (Check a) arising from a use of ‘check’
      Possible fix:
        add (Check a) to the context of the instance declaration
```

如果你仔细想想，这个实例层次结构允许我们规避不能创建 `Check [Int]` 实例的限制。

最后，有时需要多重约束。例如，考虑 `Either` 的 `Eq` 实例：

``` haskell
instance (Eq a, Eq b) => Eq (Either a b) where
  Left x  == Left y   =  x==y
  Right x == Right y  =  x==y
  _       == _        =  False
```

### 6.4.2 类层次结构

分别地，一个一个类可以依赖另一个类。例如，当你想在默认实现中使用另一个类的函数时，这非常有用：

``` haskell
class Size a where
  size :: a -> Int

class Size a => SizeBoth a where
  sizeBoth :: a -> a -> Int
  sizeBoth x y = size x + size y
```

在这种情况下，我们说 `SizeBoth` 是 `Size` 的*子类*。再次注意与面向对象编程的混淆。标准库中的子类示例包括：

``` haskell
class Eq a => Ord a where
  ...
class Num a => Fractional a where
  ...
```

查看子类的另一种方法是，如果你有 `class Main a => Sub a`，则必须提供 `instance Main MyType` 才能声明 `instance Sub MyType`。

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

``` haskell
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

# 7 第 7 讲：新的星座

本讲座介绍了“类型化函数式编程”的“设计模式”。这些模式在编写 Haskell 程序时都很有用，并且为练习之前讲座中的技能提供了一个很好的舞台。

## 7.1 用盒子建模

有时你不需要新类型，而只需重用标准类型即可。例如，用 `String` 代表汽车牌照号。但是，如果你的代码充满了 `String`，则很容易意外混淆，例如 汽车的型号和在 `registerCar :: String -> String -> CarRegistry -> CarRegistry` 等函数中的注册。

对于这种情况，通常创建一个仅包含 `String` （“盒装”字符串）的新类型：

``` haskell
data Plate = Plate String
  deriving (Show, Eq)
```

我们现在可以给 `registerCar` 一个稍微好一点的类型， `String -> Plate -> CarRegistry -> CarRegistry`。此外，我们可以将 `Plate` 上可能的操作限制为字符串上可能的操作的子集。例如，无需合并两辆汽车的车牌号码。因此我们不需要提供函数 `concatPlates :: Plate -> Plate -> Plate`。我们还可以为 `Plate` 定义一个“智能构造函数”，用于检查寄存器编号的格式是否正确：

``` haskell
parsePlate :: String -> Maybe Plate
parsePlate string
  | correctPlateNumber string  =  Just (Plate string)
  | otherwise                  =  Nothing
```

这是另一个例子：代表金钱。如果我们只是将钱存储为 `Int`，编译器将无法保护我们免受诸如将钱与钱相乘之类的错误。如果我们实现自己的 `Money` 类型来包装 `Int`，我们就可以获得类型安全。此外，我们可以概括这样一个事实：货币表示为整数美分。

``` haskell
data Money = Money Int
  deriving Show

renderMoney :: Money -> String
renderMoney (Money cents) = show (fromIntegral cents / 100)

(+!) :: Money -> Money -> Money
(Money a) +! (Money b) = Money (a+b)

scale :: Money -> Double -> Money
scale (Money a) x = Money (round (fromIntegral a * x))

addVat :: Money -> Money
addVat m = m +! scale m 0.24
```

``` haskell
renderMoney (Money 100 +! Money 150)
  ==> "2.5"

scale (Money 299) 0.24
  ==> Money 72

addVat (Money 299)
  ==> Money 371
```

**注意！** 如果你熟悉面向对象编程，这有点像封装。

## 7.2 用不同情况建模

Haskell 的代数数据类型在基于*不同情况*建模方面非常强大。将类型视为定义可能情况的集合以及“处理”这些情况的函数（通常通过模式匹配）通常很有用。让我们看两个例子。

由于在 Haskell 中定义自定义类型非常容易，因此使用更具描述性的类型而不是布尔值或字符串非常方便。考虑一个人员名单。在其他语言中，如果你想按姓名升序对人员进行排序，你可以使用类似 `sortPersons(persons, "name", true)` 的调用。在 Haskell 中你可以这样做：

``` haskell
data Person = Person {name :: String, age :: Int}
  deriving Show

data SortOrder = Ascending | Descending
data SortField = Name | Age

sortByField :: SortField -> [Person] -> [Person]
sortByField Name ps = sortBy (comparing name) ps
sortByField Age ps = sortBy (comparing age) ps

sortPersons :: SortField -> SortOrder -> [Person] -> [Person]
sortPersons field Ascending ps = sortByField field ps
sortPersons field Descending ps = reverse (sortByField field ps)

persons = [Person "Fridolf" 73, Person "Greta" 60, Person "Hans" 65]
```

``` haskell
sortPersons Name Ascending persons
  ==> [Person {name = "Fridolf", age = 73},Person {name = "Greta", age = 60},Person {name = "Hans", age = 65}]
sortPersons Age Descending persons
  ==> [Person {name = "Fridolf", age = 73},Person {name = "Hans", age = 65},Person {name = "Greta", age = 60}]
```

请注意，你如何不会意外地拼写字段名称（与字符串不同），以及你如何不需要记住 `true` 是指升序还是降序。

让我们继续看下一个例子。许多 Haskell 函数不适用于空列表（考虑 `head []`）。如果你正在编写需要跟踪列表是否可能为空或保证不为空的代码，则可以使用 [Data.List.NonEmpty](https://hackage.haskell.org/package/base-4.16.4.0/docs/Data-List-NonEmpty.html) 模块中的 `NonEmpty` 类型。

考虑 `NonEmpty` 的定义：

``` haskell
data NonEmpty a = a :| [a]
```

这个类型表示“没有空列表”这种情况。类型 `NonEmpty a` 将始终由类型为 `a` 的值以及其他一些 `a` 组成，这些值收集在列表中。以下是 `NonEmpty Int` 的一些示例值：

``` haskell
1 :| [2,3,4]
1 :| []
```

顺便说一句，这也是*中缀构造函数*的示例。我们之前已经遇到过另一个中缀构造函数，即列表构造函数 `(:)`。任何以冒号（`:` 字符）开头的运算符都可以用作中缀构造函数。我们可以在 `(:|)` 上进行模式匹配，就像在 `(:)` 上一样，正如你将在下面的示例中看到的那样。

以下是在普通列表和非空列表之间进行转换的函数。请注意，我们不能使用函数 `[a] -> NonEmpty a`，而必须使用 `Maybe` 来表示列表确实为空的可能性。另请注意 `toList` 只有一个方程，由于 `NonEmpty` 类型，我们不能出现 `toList []` 情况。

``` haskell
nonEmpty :: [a] -> Maybe (NonEmpty a)
nonEmpty [] = Nothing
nonEmpty (x:xs) = Just (x :| xs)

toList :: NonEmpty a -> [a]
toList (x :| xs) = x : xs
```

``` haskell
nonEmpty [1,2,3]     ==>  Just (1 :| [2,3])
nonEmpty [1]         ==>  Just (1 :| [])
nonEmpty []          ==>  Nothing
toList (1 :| [2,3])  ==>  [1,2,3]
```

以下是为 `NonEmpty` 实现的 `head` 和 `last`：

``` haskell
neHead (x :| _) = x
neLast (x :| []) = x
neLast (_ :| xs) = last xs
```

``` haskell
neHead (1:|[2,3])  ==>  1
neLast (1:|[2,3])  ==>  3
```

顺便说一句，这些函数可用作 `Data.List.NonEmpty.head` 和 `Data.List.NonEmpty.last` 以及许多其他有用的函数。

总之，如果你编写表示值的所有可能情况的类型，然后编写处理这些情况的函数，那么你的代码将简单且正确。

## 7.3 幺半群

在函数式编程中经常出现的一种模式是 *monoid*（不要与 *monad* 混淆！）。对幺半群的解释通常非常数学化，但其思想很简单：将事物组合起来。

### 7.3.1 关联运算

我们使用的许多函数和运算符都是*关联的*。这只是一种表达不需要括号的奇特方式。例如，所有这些表达式的值都是 16，因为加法是结合的：

``` haskell
(1 +  3) + (5 + 7)
 1 + (3  + (5 + 7))
 1  + 3  +  5 + 7
```

在 Haskell 中很容易找到关联运算的例子。例如，用于连接列表的 `++` 运算符是关联的：无论你是执行 `([1] ++ [2,3]) ++ [4]` 还是 `[1] ++ ([2,3] ++ [4])` - 结果都是 `[1,2,3,4]`。

另一个很好的例子是函数组合运算符。 `(head . tail) . tail` 和 `head . (tail . tail)` 都计算列表的第三个元素。

然而，并非所有运算符都是结合运算符。最熟悉的例子是减法和求幂。  `(1-2)-3` 是 `-4` 但 `1-(2-3)` 是 `2`。类似地，`(2^3)^2` 是 `64`，而 `2^(3^2)` 是 512。在使用非结合运算符时需要小心括号。

另一个不具有关联性的运算符是列表构造函数 `(:)`。这次的原因更加根本：虽然 `True:(False:[])` 没问题，但 `(True:False):[]` 甚至没有打字！为了使操作具有关联性，它需要采用两个相同类型的参数。

除了运算符之外，函数也可以是关联的。语法看起来有点不同，但如果它们相同，则函数 `f` 是关联的：

``` haskell
f x (f y z)
f (f x y) z
```

两个广泛使用的关联函数是 `min` 和 `max` 函数：

``` haskell
min 2 (min 1 3) ==> 1
min (min 2 1) 3 ==> 1

max 2 (max 1 3) ==> 3
max (max 2 1) 3 ==> 3
```

### 7.3.2 半群

从数学上来说，关联函数（或运算符）形成*半群*。 Haskell 有一个类型类 `Semigroup` （在模块 `Data.Semigroup` 中定义），当类型具有明确的关联操作时可以使用该类型类。

``` haskell
class Semigroup a where
  -- An associative operation.
  (<>) :: a -> a -> a
```

列表是 `Semigroup` 的实例，其中 `(++)` 为 `(<>)`：

``` haskell
[1] <> [2,3] <> [4]  ==>  [1,2,3,4]
```

具有多个不同关联运算符的类型通常不会成为 Semigroup 的实例。一个例子是 `Int`，它具有许多关联函数，例如 `+`、 `*` 和 `max`。相反，Haskell 标准库使用装箱（请参阅本讲座前面的部分）。以下是 `Sum` 和 `Product` 的定义：

``` haskell
data Sum a = Sum a
instance Num a => Semigroup (Sum a) where
  Sum a <> Sum b  =  Sum (a+b)

data Product a = Product a
instance Num a => Semigroup (Product a) where
  Product a <> Product b   =  Product (a*b)
```

顺便说一句，这是装箱的另一个好处：能够声明不同类型的类实例！

请注意 `Num a` 约束如何让我们在包含的类型 `a` 上使用 `Num` 操作，例如 `+` 和 `*`。我们可以有像 `Sum "abc" :: Sum String` 这样的值，但它们不会有 `Semigroup` 实例！

同样，我们有盒子类型 `Min` 和 `Max`。让我们在 GHCi 中玩一下：

``` haskell
Prelude> import Data.Semigroup
Prelude Data.Semigroup> Product (2::Int) <> Product 3 <> Product 1
Product {getProduct = 6}
Prelude Data.Semigroup> Sum 3 <> Sum 5 <> Sum 7
Sum {getSum = 15}
Prelude Data.Semigroup> Product 2 <> Product 3 <> Product 1
Product {getProduct = 6}
Prelude Data.Semigroup> Min 4 <> Min 3 <> Min 5
Min {getMin = 3}
Prelude Data.Semigroup> Max 4 <> Max 3 <> Max 5
Max {getMax = 5}
```

### 7.3.3 最后，幺半群

如果我们再听听数学家的说法，*幺半群* 是一个带有*中性元素*的半群。中性元素是零：与其他元素组合时不执行任何操作的元素。以下是一些示例：

``` haskell
-- 0 is the neutral element of (+)
3 + 0        ==>  3
0 + 3        ==>  3

-- 1 is the neutral element of (*)
1 * 5        ==>  5
5 * 1        ==>  5

-- [] is the neutral element of (++)
[] ++ [1,2]  ==>  [1,2]
[1,2] ++ []  ==>  [1,2]
```

Haskell 类型类 `Monoid` （来自模块 `Data.Monoid`）表示幺半群。

``` haskell
class Semigroup a => Monoid a where
  -- The neutral element
  mempty :: a
```

以下是与我们的三个中性元素示例相对应的 `Monoid` 实例：

``` haskell
instance Num a => Monoid (Sum a) where
  mempty = Sum 0

instance Num a => Monoid (Product a) where
  mempty = Product 1

instance Monoid [] where
  mempty = []
```

那么，对于程序员来说什么是幺半群呢？如果有一种方法可以将类型的两个元素组合在一起，使得括号无关紧要，并且还有一个“空元素”可以与事物组合而不改变它们，那么该类型就形成了幺半群。当这样想时，幺半群在编程中经常出现！

### 7.3.4 为什么？

这个`Monoid`类有什么用呢？我们不能只写 `1 + 2` 而不是 `Sum 1 <> Sum 2` 吗？是的，我们可以，但某些库函数适用于所有 `Monoid` 类型。

我们同时需要一个中性元素和一个关联二元运算符的原因是，为了将多个元素*减少*或*折叠*为一个值，这正是我们所需要的两件事。这是以下人员的工作：

``` haskell
mconcat :: Monoid a => [a] -> a
```

附注：定义 `mconcat` 的一种方法是 `foldr (<>) mempty`。你还记得`foldr`吗？

让我们看看为什么我们需要 `Monoid` 的属性来实现 `mconcat`。首先，我们需要 `mempty` 来处理空列表：

``` haskell
mconcat [] :: Sum Int          ==>  Sum 0
```

其次，我们需要关联性才能将列表 `[x,y,z]` 减少为唯一值。如果 `<>` 不具有关联性，则 `mconcat [x,y,z]` 可能有两个可能的值，即 `(x<>y)<>z` 和 `x<>(y<>z)`。

最有用的 `Monoid` 函数是 `foldMap`：

``` haskell
foldMap :: (Foldable t, Monoid m) => (a -> m) -> t a -> m
```

这种类型签名看起来很可怕，但具体情况更简单：

``` haskell
foldMap Max [1::Int,4,2]  ==>  Max 4
foldMap Product [1::Int,4,2]  ==>  Product 8
-- We need the ::Int to avoid an "Ambiguous type variable" error when printing the result
```

让我们来分解一下这种类型。我们知道 `Foldable t => t a` 类型的一个示例是 `[a]`，因此我们可以将该类型重写为

``` haskell
foldMap' :: Monoid m => (a -> m) -> [a] -> m
```

我们可以用我们已知的函数构建这个函数：

``` haskell
foldMap' f xs = mconcat (map f xs)
```

哦，顺便说一句，多亏了 `(Monoid a, Monoid b) => Monoid (a,b)` 实例，我们甚至可以一次计算最大值和乘积：

``` haskell
foldMap (\x -> (Max x, Product x)) [1::Int,4,2]  ==>  (Max 4, Product 8)
```

请注意，你不需要在自己的代码中使用幺半群，但在使用 Haskell 库时你最终会遇到它们，因此最好了解它们是什么。

### 7.3.5 如何？

由于各种历史和性能原因，`Monoid` 和 `Semigroup` 类的定义不仅仅是

``` haskell
class Semigroup a where
  (<>) :: a -> a -> a
class Semigroup a => Monoid a where
  mempty :: a
```

尽管你大多可以假装它们是。实际的定义是：

``` haskell
class Semigroup a where
  -- | An associative operation.
  (<>) :: a -> a -> a

  -- Combine elements of a nonempty list with <>
  sconcat :: NonEmpty a -> a
  sconcat as = ... -- default implementation omitted

  -- Combine a value with itself using <>, n times
  stimes :: Integral b => b -> a -> a
  stimes n x = ... -- default implementation omitted
```

``` haskell
class Semigroup a => Monoid a where
  mempty  :: a

  mappend :: a -> a -> a
  mappend = (<>)

  -- Combine elements of a list with <>
  mconcat :: [a] -> a
  mconcat = ... -- default implementation omitted
```

如你所见，除了 `<>` 和 `mempty` 之外的所有操作都有默认定义，因此正常的 `Monoid` 实例声明如下所示：

``` haskell
instance Semigroup MyType where
  x <> y = ...

instance Monoid MyType where
  mempty = ...
```

## 7.4 开放和封闭抽象

新手 Haskell 程序员经常问（或者至少应该问！）的一个问题是：我什么时候应该使用类型类？本节提供了一个答案。

让我们看一个具体的例子。交通工具可以是汽车或飞机。我们可以使用代数数据类型（正如我们在本章前面所看到的）来建模，也可以使用类型类来建模。这是数据类型版本：

``` haskell
data Vehicle = Car String | Airplane String

sound :: Vehicle -> String
sound (Car _) = "brum brum"
sound (Airplane _) = "zooooom"
```

这是课堂版本。请注意每种情况如何获取自己的数据类型，这些数据类型被收集在一个类型类中。

``` haskell
data Car = Car String
data Airplane = Airplane String

class VehicleClass a where
  sound :: a -> String

instance VehicleClass Car where
  sound (Car _) = "brum brum"

instance VehicleClass Airplane where
  sound (Airplane _) = "zooooom"
```

这些解决方案有什么区别？基于数据的解决方案是“封闭的”，这意味着一组情况是固定的，我们可以在一个地方处理所有情况。基于类的解决方案是*开放的*，这意味着我们可以添加新情况，甚至在其他模块中也是如此。

当我们需要可扩展性时，开放抽象是很好的选择。在基于类的解决方案中，另一个模块可以定义自行车：

``` haskell
data Bike = Bike String

instance VehicleClass Bike where
  sound (Bike _) = "whirrr"
```

当我们想知道我们已经处理了所有情况时，封闭抽象是很好的，例如考虑函数 `canCollide`，它检查两辆车是否可以碰撞：

``` haskell
canCollide :: Vehicle -> Vehicle -> Bool
canCollide (Car _)      (Car _)      = True
canCollide (Airplane _) (Airplane _) = True
canCollide _            _            = False
```

这在基于类的解决方案中很难可靠地实现。例如，考虑如何处理 `Bike` 和 `Car` 之间的冲突检查。

## 7.5 用语言建模

有时，实现一种迷你编程语言来描述软件的各个部分是很有用的。这些的奇特术语是“嵌入式领域特定语言（EDSL）”。 Haskell 非常适合建模和解释语言。该语言的表达式使用（通常是递归的）代数数据类型来表示。该语言可以由递归函数“解释”（即求值或运行）。

以下是用于描述网上商店中产品价格计算的语言示例。

``` haskell
data Discount = DiscountPercent Int         -- A percentage discount
              | DiscountConstant Int        -- A constant discount
              | MinimumPrice Int            -- Set a minimum price
              | ForCustomer String Discount -- Discounts can be conditional
              | Many [Discount]             -- Apply a number of discounts in row
```

该语言由函数 `applyDiscount` 解释，该函数接受客户名称、价格、折扣并返回价格。

``` haskell
applyDiscount :: String -> Int -> Discount -> Int
applyDiscount _        price (DiscountPercent percent) = price - (price * percent) `div` 100
applyDiscount _        price (DiscountConstant discount) = price - discount
applyDiscount _        price (MinimumPrice minPrice) = max price minPrice
applyDiscount customer price (ForCustomer target discount)
    | customer == target  = applyDiscount customer price discount
    | otherwise           = price
applyDiscount customer price (Many discounts) = go price discounts
  where go p [] = p
        go p (d:ds) = go (applyDiscount customer p d) ds
```

在这里，我们应用 -50%、-\$30 的折扣链，最低价格为 \$35：

``` haskell
applyDiscount "Bob" 120 (DiscountPercent 50)
  ==> 60
applyDiscount "Bob" 60 (DiscountConstant 30)
  ==> 30
applyDiscount "Bob" 30 (MinimumPrice 35)
  ==> 35
applyDiscount "Bob" 120 (Many [DiscountPercent 50, DiscountConstant 30, MinimumPrice 35])
  ==> 35
```

这里我们为 Ssarah 和 Yvonne 提供不同的折扣：

``` haskell
applyDiscount "Yvonne" 100 (Many [ForCustomer "Yvonne" (DiscountConstant 10), ForCustomer "Ssarah" (DiscountConstant 20)])
  ==> 90
applyDiscount "Ssarah" 100 (Many [ForCustomer "Yvonne" (DiscountConstant 10), ForCustomer "Ssarah" (DiscountConstant 20)])
  ==> 80
```

正如你所看到的，即使是简单的 `Discount` 类型也可以生成复杂的行为，因为它是自引用（递归）的。使用 `Discount`，我们能够将网上商店的折扣逻辑表示为*数据*，而不是编写代码。

将逻辑表示为数据而不是代码有多种原因。与代码不同，数据可以轻松存储在文件或数据库中，甚至可以通过网络传输。我们还可以将相同的数据用于多种目的，例如，我们可以在管理用户界面中可视化折扣规则。

## 7.6 练习

- [Set7](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set7.hs)

# 8 第 8 讲：回味

## 8.1 IO 初体验

本课程以纯函数式编程为中心。我们已经做了很多算术、反转列表、使用二叉树，但到目前为止我们还无法影响 GHCi 之外的世界。

读取输入、写入文件或通过网络通信之类的事情都是“副作用”。副作用不能用纯函数式代码来表示。像这样的函数

``` haskell
readInputFromTheUser :: String -> String
```

不可能是纯的，因为如果是纯的，`readInputFromUser "What is your name?"` 将始终返回相同的结果。然而，用纯语言表示副作用和非纯性是可能的。有很多方法可以做到这一点，Haskell 方法是使用 *Monad*。

据说 Monad 很难理解。这可能是因为它们太抽象了。我认为最好先关注实际而具体的例子。这里先让你体验一下 `IO` Monad，你可以用它来处理 Haskell 中的各种副作用。

让我们开始吧！

``` haskell
Prelude> :t getLine
getLine :: IO String
Prelude> line <- getLine
```

    another line

``` haskell
Prelude> :t line
line :: String
Prelude> line
"another line"
Prelude> reverse line
"enil rehtona"
```

我们在这里看到的是 *IO 操作* `getLine`。它的类型为 `IO String`。这意味着 GHCi 可以*执行*该操作来生成 `String` 类型的值。当我们在 GHCi 中输入 `line <- getLine` 时，我们的意思是：

> 执行 IO 操作 `getLine`，并将结果命名为 `line`。

当我们收到 `line` 后，它是一个纯的 `String` 值，我们可以正常使用它。

一些 IO 操作带有参数。例如，`putStrLn :: String -> IO ()` 采用 `String` 并返回打印该字符串的 `IO` 操作。 `()` 类型是一种特殊类型，只有一个值 `()`。在这种情况下， `IO ()` 意味着该 IO 始终产生相同的空值 `()`。你可以通过以下方式运行 IO 操作

``` haskell
Prelude> :t putStrLn
putStrLn :: String -> IO ()
Prelude> :t putStrLn "hello"
putStrLn "hello" :: IO ()
Prelude> val <- putStrLn "hello"
hello
Prelude> val
()
```

如果你不需要 IO 操作的返回值，你可以在 *GHCi* 中运行它，而不需要 `<-`：

``` haskell
Prelude> putStrLn "hello"
hello
```

你可以通过将其他操作与 *do-notation* 相结合来构建自己的 IO 操作。 `do` 块列出了按顺序执行的 IO 操作。

``` haskell
printTwoThings :: IO ()
printTwoThings = do
  putStrLn "Hello!"
  putStrLn "How are you?"

greet :: IO ()
greet = do
  putStrLn "What's your name?"
  name <- getLine
  putStrLn ("Hello, " ++ name)
```

``` haskell
Prelude> printTwoThings
Hello!
How are you?
Prelude> greet
What's your name?
Seraphim
Hello, Seraphim
```

### 8.1.1 纯性怎么办？

感觉好像我们可以通过这些 IO 操作在任何我们想要的地方产生副作用。然而，重要的是要记住“定义”IO 操作和“执行”它之间的区别。

让我们尝试在映射列表时进行打印

``` haskell
printAndIncrement :: Int -> Int
printAndIncrement x = x+1
  where action = putStrLn "got a number!"
```

``` haskell
Prelude> map printAndIncrement [1,2,3]
[2,3,4]
```

这没有打印任何内容，因为即使我们定义了 `action`，它也没有交给 GHCi 执行。由于 `printAndIncrement` 返回 `Int`，因此它无法返回操作。好吧，让我们尝试另一种方法：

``` haskell
Prelude> length (map putStrLn ["string1","string2"])
2
```

那也没有打印任何东西！让我们看看为什么：

``` haskell
Prelude> :t map putStrLn ["string1","string2"]
map putStrLn ["string1","string2"] :: [IO ()]
Prelude> :t length (map putStrLn ["string1","string2"])
length (map putStrLn ["string1","string2"]) :: Int
```

我们生成了 IO 操作列表并计算了列表的长度。定义 IO 操作是纯的，“运行”它们会导致副作用。由于我们的表达式的类型是 `Int`，因此任何 `IO` 操作都无法登陆 GHCi 并被执行。

如果我们返回一个 IO 操作，它就会运行：

``` haskell
Prelude> :t head (map putStrLn ["string1","string2"])
head (map putStrLn ["string1","string2"]) :: IO ()
Prelude> head (map putStrLn ["string1","string2"])
string1
```

这里，生成操作 `putStrLn "string1"` 的代码也是纯的，只有在 GHCi 执行 IO 操作之后我们才能看到打印的字符串。正如你所看到的，另一个 IO 操作 `putStrLn "string2"` 从未运行。

如果这感觉很复杂，请不要担心。我们将在课程的第二部分中再次讨论这一点。

### 8.1.2 Haskell 程序怎么样？

我们知道 GHCi 可以运行 IO 操作。实际的 Haskell 程序怎么样？ Haskell 程序的工作方式是在程序运行时执行名为 `main` 的 IO 操作。回想一下第 1 讲中的示例程序。

``` haskell
module Gold where

-- The golden ratio
phi :: Double
phi = (sqrt 5 + 1) / 2

polynomial :: Double -> Double
polynomial x = x^2 - x - 1

f x = polynomial (polynomial x)

main = do
  print (polynomial phi)
  print (f phi)
```

这里我们看到一些纯代码和一个 `main` IO 操作，它打印两件事（ `print` 只是 `putStrLn` 与 `show` 的组合）。

我们可以将此代码放在名为 `Gold.hs` 的文件中，将其编译为可执行文件，然后运行它：

``` sh
$ ghc -main-is Gold Gold.hs
[1 of 1] Compiling Gold             ( Gold.hs, Gold.o )
Linking Gold ...
$ ./Gold
0.0
-1.0
```

## 8.2 总结

到目前为止，我们已经了解了 Haskell 的语法和类型、相当多的函数式编程以及一些语言特性，例如类型类。

我们还看到了一些面向类型的编程，甚至体验了 Haskell 中的 I/O。

现在你已经知道如何用 Haskell 编写真正的计算机程序，但仍有很多东西需要学习。

## 8.3 接下来做什么？

[课程第 2 部分](https://haskell.mooc.fi/part2) 现已推出！第 2 部分将涵盖 Monad、IO 以及 Haskell 幕后工作原理等主题。我们还将使用网络和数据库进行一些现实世界的编程。哦，还涵盖了 Haskell 中的测试。

如果你现在不想跳入第 2 部分，那么你现在应该可以关注以下一些其他 Haskell 资源：

- [Real World Haskell](https://book.realworldhaskell.org/read/) - 有关高级主题的免费电子书
- [What I Wish I Knew When Learning Haskell](https://web.archive.org/web/20220513191346/http://dev.stephendiehl.com/hask/)
- [The Haskell Wikibook](https://en.wikibooks.org/wiki/Haskell)
- [The Haskell Website](https://www.haskell.org/) 有演讲和演示的链接
- [Haskell mini-patterns handbook](https://kowainik.github.io/posts/haskell-mini-patterns) - 更多类似讲座 7 的内容

我还建议在 Haskell 中解决一些编程问题，例如：

- [Advent of Code](https://adventofcode.com/2019/events) – 形式多样的谜题，从简单开始，逐渐变得困难
- [Sphere Online Judge](https://www.spoj.com/) – 算法问题，各种难度
- [Project Euler](https://projecteuler.net/) – 数学编程谜题

你还可以继续扩展你的最终项目，并可能在 Haskell 中生成一些很酷的艺术作品。

无论如何，非常感谢你的陪伴，我们希望你今年过得愉快！

## 8.4 最终项目：图形

打开练习文件 [`Set8.hs`](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set8.hs) 并按照其中的说明进行操作。玩得开心！

## 8.5 致谢

这门课程是由 [Nitor](https://nitor.com/en) 完成的，他为这个项目捐赠了 Joel 的大量工作时间。谢谢你！如果你有兴趣在重视持续学习的地方工作，请查看我们的 [open positions](https://www.nitor.com/en/jobs)。

感谢整个 Haskell Mooc 团队，特别是

- John Lång 帮助完善课程材料
- Antti Laaksonen 设置课程并帮助安排
