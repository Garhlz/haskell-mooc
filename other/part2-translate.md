- [9 第 9 讲：第 1 部分回顾](#lecture-9-recap-of-part-1)
  - [9.1 类型](#types)
  - [9.2 函数](#functions)
  - [9.3 函数式编程](#functional-programming)
  - [9.4 递归](#recursion)
  - [9.5 类型类](#type-classes)
  - [9.6 测验](#quiz)
  - [9.7 做练习](#working-on-the-exercises)
  - [9.8 练习](#exercises)
- [10 第 10 讲：还原论](#lecture-10-reductionism)
  - [10.1 惰性与纯性](#laziness-purity)
  - [10.2 等式推理](#equational-reasoning)
  - [10.3 无限列表](#infinite-lists)
  - [10.4 Haskell 是如何工作的？](#how-does-haskell-work)
  - [10.5 处理无限列表](#working-with-infinite-lists)
  - [10.6 插曲：加入严格性](#interlude-adding-strictness)
  - [10.7 newtype 声明](#newtype-declarations)
  - [10.8 趣味内容：打结](#something-fun-tying-the-knot)
  - [10.9 趣味内容：Debug.Trace](#something-fun-debug.trace)
  - [10.10 测验](#quiz-1)
  - [10.11 练习](#exercises-1)
- [11 第 11 讲：`RealWorld -> (a,RealWorld)`](#lecture-11-realworld---arealworld)
  - [11.1 内容](#contents)
  - [11.2 你被骗了！](#youve-been-fooled)
  - [11.3 微妙的 `return`](#the-subtle-return)
  - [11.4 `do` 和类型](#do-and-types)
  - [11.5 控制结构](#control-structures)
  - [11.6 关于 `do` 和缩进](#a-word-about-do-and-indentation)
  - [11.7 来写一个程序](#lets-write-a-program)
  - [11.8 这一切意味着什么？](#what-does-it-all-mean)
  - [11.9 还有一件事：IORef](#one-more-thing-ioref)
  - [11.10 IO 总结](#summary-of-io)
  - [11.11 测验](#quiz-2)
  - [11.12 练习](#exercises-2)
- [12 第 12 讲：fmap fmap fmap](#lecture-12-fmap-fmap-fmap)
  - [12.1 内容](#contents-1)
  - [12.2 Functor](#functors)
  - [12.3 合法实例](#lawful-instances)
  - [12.4 附注：Kind](#sidenote-kinds)
  - [12.5 再谈 `Foldable`](#foldable-again)
  - [12.6 回顾](#recap)
  - [12.7 测验](#quiz-3)
  - [12.8 练习](#exercises-3)
- [13 第 13 讲：问题范畴中的幺半群](#lecture-13-a-monoid-in-the-category-of-problems)
  - [13.1 示例 1：Maybe](#example-1-maybes)
  - [13.2 示例 2：日志](#example-2-logging)
  - [13.3 示例 3：维护状态](#example-3-keeping-state)
  - [13.4 终于：Monad 类型类](#finally-the-monad-type-class)
  - [13.5 Maybe 是 Monad！](#maybe-is-a-monad)
  - [13.6 `do` 的回归](#the-return-of-do)
  - [13.7 Logger 是 Monad！](#logger-is-a-monad)
  - [13.8 State Monad](#the-state-monad)
  - [13.9 `mapM` 的回归](#the-return-of-mapm)
  - [13.10 Monad 是 Functor](#monads-are-functors)
  - [13.11 又一个 Monad](#one-more-monad)
  - [13.12 哦对，IO](#oh-right-io)
  - [13.13 其他语言中的 Monad](#monads-in-other-languages)
  - [13.14 Monad：总结](#monads-wrap-up)
  - [13.15 附注：标准 Haskell](#sidenote-standard-haskell)
  - [13.16 测验](#quiz-4)
  - [13.17 练习](#exercises-4)
- [14 第 14 讲：来用一些库！](#lecture-14-lets-use-some-libraries)
  - [14.1 `Text` 和 `ByteString`](#text-and-bytestring)
  - [14.2 Monad：回顾](#monads-recap)
  - [14.3 编写 HTTP 服务器：WAI 和 Warp](#writing-a-http-server-wai-and-warp)
  - [14.4 使用数据库：sqlite-simple](#working-with-a-database-sqlite-simple)
  - [14.5 练习](#exercises-5)
- [15 第 15 讲：没有 Monad 也能验证](#lecture-15-youre-valid-even-without-monads)
  - [15.1 Applicative 简介](#introduction-to-applicatives)
  - [15.2 列表 Applicative](#the-list-applicative)
  - [15.3 新运算符](#new-operators)
  - [15.4 Validation Applicative](#the-validation-applicative)
  - [15.5 验证列表：`traverse`](#validating-lists-traverse)
  - [15.6 附注：`Traversable`](#sidenote-traversable)
  - [15.7 处理失败：`Alternative`](#dealing-with-failure-alternative)
  - [15.8 附注：语境中的 Applicative](#sidenote-applicatives-in-context)
  - [15.9 测验](#quiz-5)
  - [15.10 练习](#exercises-6)
- [16 第 16 讲：零碎内容](#lecture-16-odds-and-ends)
  - [16.1 使用 QuickCheck 测试](#testing-with-quickcheck)
  - [16.2 幻影类型](#phantom-types)
  - [16.3 同时性](#simultaneity)
  - [16.4 练习](#exercises-7)
  - [16.5 接下来去哪里？](#where-to-go-from-here)
  - [16.6 致谢](#acknowledgements)

# Haskell 慕课，第 2 部分

作者：Joel Kaasinen ([Nitor](https://nitor.com/en)) 和 John Lång（赫尔辛基大学）

# 9 第 9 讲：第 1 部分回顾

本讲座回顾了课程第 1 部分中介绍的 Haskell 的基本部分：类型、值、模式匹配、函数和递归。

## 9.1 类型

还记得 Haskell 的原始类型吗？他们在这里：

|值 |类型 |意义|
|:---|:---|:---|
| `True`、`False` | `Bool` |真值|
| `0`、`1`、`20`、`-37`、… | `Int` |整数 |
| `'A'`、`'a'`、`'!'`，… | `Char` |字符 |
| `""`、`"abcd"`、…… | `String` |字符串，实际上只是字符列表，`[Char]` |
| `0.0`、`-3.2`、`12.3`、… | `Double` |浮点数 |
| `()` | `()` |所谓只有一个值的单位类型 |

可以通过各种方式组合这些原始类型以形成更复杂的类型。函数类型、元组类型和列表类型是组合其他类型的类型的示例。

|值 |类型 |意义|
|:---|:---|:---|
| `(1,2)`、`(True,'c')`、…… | `(a, b)` |一对 `a` 类型的值和 `b` 类型的值 |
| `(1,2,3)`、`(1,2,'c')`、…… | `(a, b, c)` |三元组（类型为 `a`、`b` 和 `c`）|
| `[]`、`[1,2,3,4]`、…… | `[a]` | | `a` 类型的值列表 |
| `not`、`reverse`、`\x -> 1`、`\x -> x`、… | `a -> b` |从 `a` 类型到 `b` 类型的函数|

有一种更强大的机制可以创建更多类型：*代数数据类型* (ADT)。一些例子包括：

``` haskell
-- Enumeration types
data Bool = True | False
data Color = Red | Green | Blue

-- Record types that contain fields
data Vector2d = MakeVector Double Double
data Person = Person Int String

-- Parameterized types. Note the type parameter `a`
data PairOf a = TwoValues a a

-- Recursive types
data IntList = Empty | Node Int IntList

-- Complex types which combine many of these features
data Maybe a = Nothing | Just a
data Either a b = Left a | Right b
data List a = Nil | Cons a (List a)             -- This is equivalent to the built-in [a] type
data Tree a = Leaf a | Node a (Tree a) (Tree a)
data MultiTree a = MultiTree a [MultiTree a]     -- Note the list
```

这些类型的值包括：

|值 |类型 |
|:---|:---|
| `True`、`False` | `Bool` |
| `Red`、`Green`、`Blue` | `Color` |
| `MakeVector 1.5 3.2` | `Vector2d` |
| `Person 13 "Bob"` | `Person` |
| `TwoValues 1 3` | `PairOf Int` |
| `Empty`、`Node 3 (Node 4 Empty)` | `IntList` |
| `Nothing`、`Just 3`、`Just 4`、… | `Maybe Int` |
| `Nothing`、`Just 'c'`、`Just 'd'`、… | `Maybe Char` |
| `Left "foo"`、`Right 13`、…… | `Either String Int` |
| `Nil`、`Cons True Nil`、`Cons True (Cons False Nil)` … | `List Bool` |
| `Leaf 7`、`Node 1 (Leaf 0) (Leaf 2)`、…… | `Tree Int` |
| `MultiTree 'a' [MultiTree 'b' [], MultiTree 'c' []]]`，… | `MultiTree Char` |

你可以以复杂的方式组合参数化类型，例如使用 `Either [String->String] (Maybe String, Int)` 之类的东西。

具体类型的名称以大写字母开头。小写字母用于*类型变量*，表示*参数多态性*：可以具有多种类型的函数和值。以下是多态函数类型的一些示例：

``` haskell
[a] -> [a]    -- function from list of any type, to list of the same type
[a] -> a      -- function from list of any type, to the element type
(a,b) -> [a]  -- function from tuple to list
```

### 9.1.1 有关列表的更多信息

列表文字可以使用熟悉的 `[x,y,z]` 语法编写。然而，该表示法只是一种简写，因为列表实际上是由列表构造函数 `[]` 和 `(:)` 构建的。当模式匹配列表时也会使用这些构造函数。以下是一些列表示例：

|缩写 |完整列表 |类型 |
|:----------------|:--------------------------|:---------------------------------|
| `[1,2,3]` | `1:2:3:[]` | `[Int]` |
| `[[1],[2],[3]]` | `(1:[]):(2:[]):(3:[]):[]` | `[[Int]]` |
| `"foo"` | `'f':'o':'o':[]` | `[Char]`，又名`String` |

列表还有范围语法：

|范围 |结果 |
|:----------------|:------------------------------------------------------------|
| `['a' .. 'z']` | `"abcdefghijklmnopqrstuvwxyz"` |
| `[0 .. 9]` | `[0,1,2,3,4,5,6,7,8,9]` |
| `[0, 5 .. 25]` | `[0,5,10,15,20,25]` |
| `[x .. y]` |从 `x` 到 `y` 的一切 |
| `[9 .. 3]` | `[]` |
| `[y, y-1 .. x]` |从 `y` 到 `x` 的所有内容均按降序排列 |
| `[9,8 .. 3]` | `[9,8,7,6,5,4,3]` |

*列表推导式*是创建列表的另一种强大方法：

|理解力|结果 |
|:------------------------------------------------|:------------------|
| `[x^3 | x <- [1..3]]` | `[1,8,27]` |
| `[x^2 + y^2 | x <- [1..3], y <- [1..2]]` | `[2,5,5,8,10,13]` |
| `[y | x <- [1..10], let y = x^2, even x, y<50]` | `[4,16,36]` |
| `[c | c <- "Hello, World!", elem c ['a'..'z']]` | `"elloorld"` |

一般来说，`[f x | x <- xs, p x]` 与 `map f (filter p xs)` 相同。另外，`[y | x <- xs, let y = f x]` 与 `[f x | x <- xs]` 相同。 `<-`、`let` 和 `[f x | ...]` 的任意组合都是可能的。

关于语法还有一点注释。回想一下，`(:)` 与右侧关联，例如 `True:False:[]` 与 `True:(False:[])` 相同。 （事实上​​，`(True:False):[]`甚至不是一个列表，因为`True:False`试图在`False`前面添加`True`，而`False`不是一个列表。）

## 9.2 函数

函数定义的基本形式为：

``` haskell
functionName :: argumentType -> returnType
functionName argument = returnValue
```

例如：

``` haskell
repeatString :: String -> String
repeatString s = s ++ s
```

采用多个参数的函数以类似的方式定义。请注意多参数函数的类型。

``` haskell
surroundString :: String -> String -> String
surroundString around s = around ++ s ++ around
```

函数可以是多态的，可以接受多个参数，甚至可以将函数作为参数。以下是更多示例：

``` haskell
id :: a -> a
id x = x

const :: a -> b -> a
const x y = x

flip :: (a -> b -> c) -> b -> a -> c
flip f x y = f y x
```

可以使用*模式匹配*来定义更复杂的函数。我们可以对 `Maybe` 等代数数据类型的“构造函数”进行模式匹配，还可以使用构造函数 `[]` 和 `(:)` 进行列表。

``` haskell
swap :: (a,b) -> (b,a)
swap (x,y) = (y,x)

maybe :: b -> (a -> b) -> Maybe a -> b
maybe def _ Nothing  = def
maybe _   f (Just x) = f x

safeHead :: [a] -> Maybe a
safeHead []    = Nothing
safeHead (x:_) = Just x
```

然而，通过*守卫*可以实现更复杂的效果。守卫允许你根据 `Bool` 类型的测试逐案定义函数。守卫在无法使用模式匹配的情况下很有用。当然，守卫也可以与模式匹配结合起来：

``` haskell
myAbs :: Int -> Int
myAbs x
  | x < 0     = -x
  | otherwise = x

safeDiv :: Double -> Double -> Maybe Double
safeDiv x y
  | y == 0    = Nothing
  | otherwise = Just (x / y)

buy :: String -> Double -> String
buy "Banana" money
  | money < 3.2   = "You don't have enough money for a banana"
  | otherwise     = "You bought a banana"
buy product  _    = "No such product: " ++ product
```

*大小写表达式*让我们在函数内部进行模式匹配。当一个函数的结果依赖于另一个函数的结果并且我们想要匹配另一个函数的输出上的模式时，它们非常有用：

``` haskell
divDefault :: Double -> Double -> Double -> Double
divDefault x y def = case safeDiv x y of
  Nothing -> def
  Just w  -> w
```

Let 表达式启用*局部定义*。 Where 子句的工作方式与 `let` 类似。例如：

``` haskell
circleArea :: Double -> Double
circleArea r = let pi = 3.1415926
                   square x = x * x
               in pi * square r

circleArea' :: Double -> Double
circleArea' r = pi * square r
    where pi = 3.1415926
          square x = x * x
```

*Lambda 表达式* 是另一种偶尔有用的定义函数的语法。 Lambda 表达式表示匿名（未命名）函数。它们可用于定义通常仅使用一次的局部函数。

``` haskell
incrementAll :: [Int] -> [Int]
incrementAll xs = map (\x -> x + 1) xs
```

请注意，`f x = y` 与 `f = \x -> y` 相同。

最后，二元运算符有*部分*。节是部分应用的运算符。运算符的部分是通过将运算符及其参数之一写在括号中来获得的。例如，`(*2)` 将其参数从右侧乘以 `2`，例如 `(*2) 5 ==> 5 * 2`。小数（例如 `Double`）可以与部分 `(1/)` 反转，例如 `(1/) 2 ==> 0.5`。

``` haskell
incrementAll' :: [Int] -> [Int]
incrementAll' xs = map (+1) xs
```

## 9.3 函数式编程

Haskell 是一种函数式编程语言，这意味着函数可以作为参数传入并从函数返回。作为一种编程范式，函数式编程旨在通过将简单的函数组合在一起形成越来越大的函数来构建程序。

最常见的函数式编程示例是使用“高阶函数”（将函数作为参数的函数）进行函数列表操作，例如 `map` 和 `filter`。以下是第 1 部分中的一个示例：

``` haskell
-- a predicate that checks if a string is a palindrome
palindrome :: String -> Bool
palindrome str = str == reverse str

-- palindromes n takes all numbers from 1 to n, converts them to
-- strings using show, and keeps only palindromes
palindromes :: Int -> [String]
palindromes n = filter palindrome (map show [1..n])
```

``` haskell
palindromes 150
  ==> ["1","2","3","4","5","6","7","8","9",
       "11","22","33","44","55","66","77","88","99",
       "101","111","121","131","141"]
```

我们在第 1 部分中还遇到了其他函数式编程模式，例如*部分应用*：

``` haskell
map (take 3) [[1,2,3,4,5],[6,7,8,9,0]]
  ==> [[1,2,3],[6,7,8]]
```

另外，*函数组合*：

``` haskell
(map reverse . filter (/="Smith")) ["Jones","Smith","White"]
  ==> ["senoJ","etihW"]
map (negate . sum) [[1,2,3],[5]]
  ==> [-6,-5]
```

最后，*折叠*：

``` haskell
foldr (*) 1 [2,3,4]  ==> 24
foldr max 0 [1,3,7]  ==> 7
foldr (++) "" ["abc","de","f"] ==> "abcdef"
```

## 9.4 递归

要在 Haskell 中实现使用重复的函数，你需要递归。 Haskell 没有像其他编程语言那样的循环。以下是 Haskell 中的一些简单的递归函数：

``` haskell
repeatString :: Int -> String -> String
repeatString 0 s = ""
repeatString n s = s ++ repeatString (n-1) s

times :: Int -> Int -> Int
times 0 n = 0
times 1 n = n
times m n = n + times (m - 1) n

safeLast :: [a] -> Maybe a
safeLast []     = Nothing
safeLast [x]    = Just x
safeLast (x:xs) = safeLast xs
```

要使用或生成列表，你通常需要递归。以下是 `map` 和 `filter` 的实现作为递归列表处理的示例：

``` haskell
map :: (a -> b) -> [a] -> [b]
map _ []     = []
map f (x:xs) = f x : map f xs

filter :: (a -> Bool) -> [a] -> [a]
filter _    []     = []
filter pred (x:xs)
  | pred x         = x : filter pred xs
  | otherwise      = filter pred xs
```

有时，如果你需要跟踪多条数据，则需要递归辅助函数。

``` haskell
sumNumbers :: [Int] -> Int
sumNumbers xs = go 0 xs
  where go sum [] = sum
        go sum (x:xs) = go (sum+x) xs
```

这是使用守卫、模式匹配、辅助函数和递归的最后一个示例：

``` haskell
-- split a string into pieces at the given character
mySplit :: Char -> String -> [String]
mySplit c xs = helper [] xs
  where helper piece [] = [piece]
        helper piece (y:ys)
          | c == y    = piece : helper [] ys
          | otherwise = helper (piece++[y]) ys
```

``` haskell
mySplit '-' "a-bcd-ef"  ==>  ["a","bcd","ef"]
```

## 9.5 类型类

以下函数是*参数多态*：

``` haskell
id :: a -> a
id x = x

head :: [a] -> a
head (x:_) = x

fst :: (a,b) -> a
fst (x,y) = x
```

无论我们使用什么类型，参数多态函数始终以相同的方式工作。这意味着我们不能只为 `Int` 类型定义 `id` 的特殊实现，也不能为 `(Bool, String)` 类型定义 `fst` 的特殊实现。

相比之下，“临时多态性”允许不同类型对同一函数有不同的实现。 Haskell 中的临时多态性可以通过定义一个“类型类”，然后为各种类型声明该类型类的“实例”来实现。即席多态性是表达一组通用操作的便捷方法，即使操作的实现取决于它们所作用的类型。

使用临时多态性的函数在其类型中具有“类约束”。以下是一些示例：

``` haskell
negate :: Num a => a -> a
(==) :: Eq a => a -> a -> Bool
sort :: Ord a => [a] -> [a]
```

像 `Num a => a -> a` 这样的类型意味着：对于属于 `Num` 类成员的任何类型 `X`，该函数的类型为 `X -> X`。换句话说，我们可以在任何数字类型上调用 `negate`，但不能在其他类型上调用：

``` haskell
Prelude> negate 1
-1
Prelude> negate 1.0
-1.0
Prelude> negate True
<interactive>:3:1: error:
    • No instance for (Num Bool) arising from a use of ‘negate’
```

以下是标准库中一些有用的类型类的摘要。

- 比较
  - `Eq` 用于相等比较。它包含 `==` 运算符
  - `Ord` 用于订单比较。它包含 `<` 和 `=>` 等有序比较运算符，以及 `max` 和 `min` 等函数。
- 数字
  - `Num` 适用于所有数值类型。它包含`+`、`-`、`*`和`negate`。
  - `Integral` 适用于整数类型。最值得注意的是，它包含整数除法 `div`。
  - `Fractional` 适用于支持除法的数字类型，`/`
- 将值转换为字符串
  - `Show` 包含将值转换为字符串的函数 `show :: Show a => a -> String`
  - `Read` 包含函数 `read :: Read a => String -> a`，它是 `show` 的逆函数

有时，需要多个类约束。例如这里：

``` haskell
sumTwoSmallest :: (Num a, Ord a) => [a] -> a
sumTwoSmallest xs = let (a:b:_) = sort xs
                    in a+b
```

现在我们已经了解了一些类和类型，让​​我们看看声明类和实例的语法。下面是两个类的定义：

``` haskell
class Sized a where
  empty :: a        -- a thing with size 0
  size :: a -> Int

class Eq a where
  (==) :: a -> a -> Bool
```

考虑以下数据结构：

``` haskell
data Numbers = None | One Int | Two Int Int
data IntList = Nil | ListNode Int IntList
data Tree a = Leaf | Node a (Tree a) (Tree a)
```

所有这些都有我们可以计算的大小，但我们需要以不同的方式执行操作：

``` haskell
instance Sized Numbers where
  empty = None
  size None      = 0
  size (One _)   = 1
  size (Two _ _) = 2

instance Sized IntList where
  empty = Nil
  size Nil               = 0
  size (ListNode _ list) = 1 + size list

instance Sized (Tree a) where
  empty = Leaf
  size Leaf = 0
  size (Node _ left right) = 1 + size left + size right
```

我们还可以轻松地为 `Numbers` 和 `IntList` 声明 `Eq` 实例：

``` haskell
instance Eq Numbers where
  None      == None       = True
  (One x)   == (One y)    = x==y
  (Two x y) == (Two z w)  = x==z && y==w
  _         == _          = False         -- to handle cases like None == One 1

instance Eq IntList where
  Nil             == Nil               = True
  (ListNode x xs) == (ListNode y ys)   = x == y && xs == ys
  _               == _                 = False
```

但是，由于 `Tree` 数据类型是通过元素类型 `a` 进行参数化的，因此我们需要一个 `Eq a` 实例才能拥有 `Eq (Tree a)` 实例。这是通过向实例声明添加类约束来实现的。这称为“实例层次结构”。

``` haskell
instance Eq a => Eq (Tree a) where
  Leaf         == Leaf              = True
  (Node x l r) == (Node x' l' r')   = x == x' && l == l' && r == r'
  _            == _                 = False
```

### 9.5.1 推导

一些标准类型类，尤其是 `Show`、`Read`、`Eq` 和 `Ord` 可以“派生”，也就是说，你可以要求编译器为你生成自动实例。例如，我们可以为之前的 `Numbers` 示例派生所有这些类。

``` haskell
data Numbers = None | One Int | Two Int Int
  deriving (Show, Read, Eq, Ord)
```

``` haskell
None == One 1       ==> False
Two 1 2 == Two 1 2  ==> True
None < Two 1 2      ==> True
Two 1 3 < Two 1 2   ==> False
show (Two 1 3)      ==> "Two 1 3"
```

## 9.6 测验

`('c',not)`是什么型号

1.`[Char]`
2.`[Bool]`
3.`(Char,Bool -> Bool)`
4.`(Char,Bool)`
5.这是一个类型错误。

`['c',not]`是什么型号

1.`[Char]`
2.`[Bool]`
3.`(Char,Bool -> Bool)`
4.`(Char,Bool)`
5.这是一个类型错误。

其中哪一个是以下类型的值？

``` haskell
data T = X Int | Y String String | Z T
```

1.`X "foo"`
2.`Y "foo"`
3.`Z (X 1)`
4.`X (Z 1)`

这个函数的类型是什么？

``` haskell
f (_:Just x:_) = x
f _            = False
```

1.`Maybe a -> a`
2.`[Maybe a] -> a`
3.`[Maybe a] -> Bool`
4.`[Maybe Bool] -> Bool`

这个函数的类型是什么？

``` haskell
f x y = x-y == 0
```

1.`(Num a, Eq a) => a -> a -> Bool`
2.`Num a => a -> a -> Bool`
3.`Eq a => a -> a -> Bool`
4.`a -> a -> Bool`

为了使 `x (&&) y` 不成为类型错误，`x` 可以具有以下哪种类型？

1.`Bool`
2.`Bool -> Bool -> Bool`
3.`(Bool -> Bool -> Bool) -> Bool -> Bool`

## 9.7 做练习

以下是如何进行练习的简短回顾。该系统与课程第 1 部分相同。

1. 克隆 GitHub 存储库 <https://github.com/moocfi/haskell-mooc>
2、进入`exercises/`目录
3.运行`stack build`下载依赖
4.编辑文件`Set9a.hs`
5. 通过运行 `stack runhaskell Set9aTest.hs` 检查你的答案
6. 在【提交页面】返回你的答案(https://haskell.mooc.fi/submit.php)
7. 根据需要重复。你可以按任意顺序练习练习组，并且可以根据需要多次返回练习组！
8. 在【我的状态页】可以看到总分(https://haskell.mooc.fi/status.php)

## 9.8 练习

- [Set9a](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set9a.hs) - 回顾课程第 1 部分的小练习
- [Set9b](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set9b.hs) - 让我们解决 N 皇后难题！

# 10 第 10 讲：还原论

- 纯性
- 惰性
- Haskell评估

## 10.1 惰性与纯性

在第 1 部分的开头提到了 Haskell 的关键特性：纯性和惰性。让我们仔细看看它们。

Haskell 是一种“纯”函数式语言。这意味着对于给定的 `x` 和 `y`，值 `f x y` 始终相同。换句话说，`x`和`y`的值唯一确定`f x y`的值。此属性也称为“引用透明度”。

纯性还意味着没有副作用：你不能让 `f x y` 的评估从用户那里读取一行 - 该行在 `f` 的不同调用中会有所不同，并且会影响返回值，破坏引用透明度！显然，你需要副作用才能真正完成某件事。稍后我们将回到 Haskell 如何处理副作用。

Haskell 是一种“惰性”语言。这意味着如果不需要某个值，则不会对其进行评估。有一个例子最好地说明了这一点。考虑这两个函数：

``` haskell
f x = f x   -- infinite recursion
g x y = x
```

由于无限递归，评估 `f 1` 不会停止。然而，这有效：

``` haskell
g 2 (f 1)  ==>  2
```

惰性不是问题，因为 Haskell 是纯的。重要的只是函数的结果，而不是副作用。因此，如果不使用函数的结果，我们就无法在不改变程序含义（语义）的情况下对其进行评估。好吧，有时我们会得到一个正在终止的程序，而不是一个永远持续下去的程序，但是添加惰性永远不会使正常运行的 Haskell 程序中断。

如果你对此背后的理论感兴趣，请查看 [Church-Rosser 定理](https://en.wikipedia.org/wiki/Church%E2%80%93Rosser_theorem) 或 Haskell Wiki 文章 [惰性与非严格](https://wiki.haskell.org/Lazy_vs._non-strict)。

## 10.2 等式推理

引用透明度，即表达式对于相同输入始终返回相同值的函数，是一个非常强大的属性，我们可以利用它来“推理程序”。

在 C 风格语言中，我们可能编写一个过程，该过程可能并不总是为相同的参数返回相同的值：

    int c = 0;
    int funny(int x) {
      return x + c++;
    }

表达式 `c++` 递增 `c` 的值并返回 `c` 的旧值。下次评估时，`c` 的值增加了 1。这意味着根据 `c` 的当前值，`funny(0)` 可能返回 `0`、`1`、`2` 或任何其他整数值。 （如果 `c` 溢出，它甚至可能返回负值！）

在某些情况下，这种带有副作用的行为可能很有用，但有时更重要的是能够轻松地推理代码。纯函数的优点是可以使用基本的数学技术对其进行分析。有时，将数学应用于我们的函数甚至可以揭示我们原本不会想到的简化或优化。

考虑以下表达式：

``` haskell
map (+1) . reverse . map (-1)
```

该表达式可以简化为 `reverse`。我们首先建立一些有用的事实（或*引理*）。首先，假设我们知道

1.`map id === id`
2.`map f . map g === map (f.g)`
3.`reverse . map f === map f . reverse`

我们需要的第四个事实如下：

4.`(+1) . (-1) === id`

我们可以通过推理 `(+1) . (-1)` 对于任意输入 `x` 的行为来证明事实 4：

``` haskell
((+1) . (-1)) x === ((+1) ((-1) x))
                === ((+1) (x - 1))
                === (x - 1) + 1
                === x
                === id x
```

因为我们没有对 `x` 做出任何假设，所以我们可以得出结论，上述方程链适用于*每个*`x`。因此，

``` haskell
(+1) . (-1) === id
```

对于那些熟悉“归纳证明”技术的人来说，证明前三个事实也是一个有趣的练习。不过，本课程不讨论归纳证明，所以如果你不了解归纳也不必担心。

现在，从事实 1-4 可以看出

``` haskell
    map (+1) . reverse . map (-1)
=== map (+1) . (reverse . map (-1))    -- By associativity of (.)
=== map (+1) . (map (-1) . reverse)    -- By fact 3
=== (map (+1) . map (-1)) . reverse    -- By associativity of (.)
=== map ((+1) . (-1)) . reverse        -- By fact 2
=== map id . reverse                   -- By fact 4
=== id . reverse                       -- By fact 1
=== reverse                            -- By the definition of id
```

本课程不会详细介绍有关程序的证明，但很高兴知道纯函数式编程与这样的分析非常兼容。

## 10.3 无限列表

一些涉及“无限列表”的例子最好地证明了惰性的好处。让我们从 `repeat 1` 开始，它生成 `1` 的无限列表。如果我们尝试告诉 GHCi 打印值 `repeat 1`，它将永远继续打印 `1`，直到我们使用 Control-C 中断它：

``` haskell
Prelude> repeat 1
[1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1
^C
```

然而，由于惰性，我们可以使用无限列表并编写结束的计算。我们只需要使用无限列表中的有限数量的元素。以下是一些示例：

``` haskell
Prelude> take 10 $ repeat 1
[1,1,1,1,1,1,1,1,1,1]
Prelude> take 20 $ repeat 1
[1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1]
Prelude> repeat 1 !! 13337
1
```

有时可能需要一个仅重复一个元素的无限列表，但这毫无意义。接下来让我们看看一些更有用的无限列表。你可以使用 `[n..]` 语法生成从 `n` 开始的无限数字列表：

``` haskell
Prelude> take 20 [0..]
[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19]
Prelude> take 10 . map (2^) $ [0..]
[1,2,4,8,16,32,64,128,256,512]
```

函数 `cycle` 一遍又一遍地重复给定列表中的元素。它在处理旋转或循环时很有用。

``` haskell
Prelude> take 21 $ cycle "asdf"
"asdfasdfasdfasdfasdfa"
Prelude> take 4 . map (take 4) . tails $ cycle "asdf"
["asdf","sdfa","dfas","fasd"]
```

### 10.3.1 示例：交易编号

作为 `cycle` 如何有用的更具体示例，让我们看一下计算芬兰银行转账交易数字的校验位 ([viitenumero](https://fi.wikipedia.org/wiki/Tilisiirto#Viitenumero))。交易号由任意数量的数字组成，后跟一个校验位。通过将数字（从右到左）与数字 7、3、1、7、3、1 等相乘并对结果求和来检查校验位。如果和*加上校验位*的结果能被10整除，则该数字有效。

这是一个具体的例子。 `116127`是有效的交易号。计算过程如下：

    digits:       1  1  6  1  2
                  *  *  *  *  *
    multipliers:  3  7  1  3  7
                  3+ 7+ 6+ 3+14 = 33
    check digit is 7, 33+7=40 is divisible by 10, valid

这是交易数字检查器的 Haskell 代码。请注意我们如何使用无限列表 `cycle [7,3,1]` 作为乘法器。

``` haskell
viitenumeroCheck :: [Int] -> Bool
viitenumeroCheck allDigits = mod (checksum+checkDigit) 10 == 0
  where (checkDigit:digits) = reverse allDigits
        multipliers = cycle [7,3,1]
        checksum = sum $ zipWith (*) multipliers digits
```

``` haskell
viitenumeroCheck [1,1,6,1,2,7]  ==> True
viitenumeroCheck [1,1,6,1,2,8]  ==> False
```

### 10.3.2 示例：求幂

最后，这里介绍如何求出大于 100 的 3 的第一个幂。

``` haskell
Prelude> head . filter (>100) $ map (3^) [0..]
243
```

让我们逐步了解一下它是如何工作的。请注意映射和过滤器如何根据需要延迟处理列表，一次处理一个元素。这类似于 Python 或 Java 等语言中*生成器*或*迭代器*的工作方式。

``` haskell
    head (filter (>100) (map (3^) [0..]))
==> head (filter (>100) (map (3^) (0:[1..])))   -- evaluate first element of the lazy list
==> head (filter (>100) (1 : map (3^) [1..]))   -- map processes the element
==> head (filter (>100) (map (3^) [1..]))       -- filter drops the element
==> head (filter (>100) (map (3^) (1:[2..])))   -- evaluate second element of the lazy list
==> head (filter (>100) (3 : map (3^) [2..]))   -- map processes the element
==> head (filter (>100) (map (3^) [2..]))       -- filter drops the element
-- let's take bigger steps now
==> head (filter (>100) (9 : map (3^) [3..]))   -- map processes, filter will drop
==> head (filter (>100) (27 : map (3^) [4..]))  -- map processes, filter will drop
==> head (filter (>100) (81 : map (3^) [5..]))  -- map processes, filter will drop
==> head (filter (>100) (243 : map (3^) [6..])) -- map processes
==> head (243 : filter (>100) (map (3^) [6..])) -- filter lets the value through
==> 243                                         -- head returns the result
```

## 10.4 Haskell 是如何工作的？

现在你可能会觉得惰性有点神奇。你可能想知道它是如何实现的。 Haskell 评估非常简单，只是与你可能习惯的不同。让我们深入探讨一下。

在大多数其他编程语言（如 Java、C 或 Python）中，求值是由内而外进行的。函数的参数在函数之前计算。

Haskell 评估是从外到内而不是从内到外进行的。应用表达式中最外层函数的定义，而不计算任何参数。下面是玩具函数 `f` 和 `g` 的具体示例：

``` haskell
g :: Int -> Int -> Int
g x y = y+1
f :: Int -> Int -> Int -> Int
f a b c = g (a*1000) c
```

由内而外（正常）评估：

``` haskell
f 1 (1234*1234) 2
  -- evaluate arguments to f
  ==> f 1 1522756 2
  -- evaluate f
  ==> g (1*1000) 2
  -- evaluate arguments to g
  ==> g 1000 2
  -- evaluate g
  ==> 2+1
  ==> 3
```

Haskell 由外而内的评估：

``` haskell
f 1 (1234*1234) 2
  -- evaluate f without evaluating arguments
  ==> g (1*1000) 2
  -- evaluate g without evaluating arguments
  ==> 2+1
  ==> 3
```

请注意未使用的计算 `1234*1234` 和 `1*1000` 未得到评估。这就是为什么惰性常常是有帮助的。

### 10.4.1 模式匹配驱动评估

让我们看一个更复杂的示例，其中包含模式匹配和更复杂的数据（列表）。正如我们将看到的，模式匹配以非常具体的方式驱动 Haskell 评估。这是我们将使用的一些函数。它们在前奏曲中很熟悉，但我会给它们简单的定义。

``` haskell
not True = False
not False = True
map f [] = []
map f (x:xs) = f x : map f xs
length [] = 0
length (x:xs) = 1+length xs
```

这是表达式的由内而外的求值：

``` haskell
length (map not (True:False:[]))
  ==> length (not True : not False : [])  -- evaluate call to map
  ==> length (False:True:[])              -- evaluate calls to not
  ==> 2
```

以下是 Haskell 中的评估过程。请注意，它并不是严格由外向内的，因为我们有时需要评估内部参数才能知道匹配哪个模式。

``` haskell
length (map not (True:False:[]))
  -- We can't evaluate length since we don't know which equation of length applies,
  -- so we look at length's argument. We can apply the second equation of map, so we do.
  ==> length (not True : map not (False:[]))
  -- Now the argument of length has a (:) we can pattern match on, so we apply the
  -- second equation of length
  ==> 1 + length (map not (False:[]))
  -- The outermost function is now +, but it can't do anything unless both arguments
  -- are numbers. So we need to evaluate length. In order to pick an equation, we need
  -- to evaluate the argument of length again. We apply the second equation of map
  ==> 1 + length (not False : map not ([]))
  -- Now we can apply the second equation of length again.
  ==> 1 + (1 + length (map not []))
  -- The outermost + needs a number to be evaluated. The second + also needs a number.
  -- We need to evaluate length again, which means we need to pick an equation for length,
  -- which means we need to evaluate its argument. This time it is the first equation for
  -- map that applies.
  ==> 1 + (1 + length [])
  -- Now we can apply the first equation for length
  ==> 1 + (1 + 0)
  -- The outermost + still can't be evaluated, but the inner one can
  ==> 1 + 1
  -- Finally we evaluate the outer +
  ==> 2
```

请注意，我们不需要评估任何 `not` Applicative。

让我们介绍一些术语。我们说模式匹配*强制*评估。当 Haskell 求值时，它会将其求值为“弱头范式 (WHNF)”*。 WHNF 基本上意味着*可以进行模式匹配的值*。如果一个表达式无法在*其顶层*上进行计算，则该表达式处于 WHNF 状态。这意味着：

- 是常数，例如：`1`
- 在顶层有一个构造函数，例如：`False`、`Just (1+1)`、`0:filter f xs`
- 是一个函数，例如：`(\x -> 1+x)`

WHNF 中最值得注意的一类表达式是函数应用。如果表达式由应用于某些参数的函数（不是构造函数）组成，则它不在 WHNF 中。我们必须评估它以获得模式可匹配的东西。

在前面的示例中，我们无法为 `length (map not (False:[]))` 中的 `length` 选择方程。参数 `(map not ...)` 不在 WHNF 中，因此无法进行模式匹配。因此我们需要对其进行评估。当我们应用 `map` 的第二个方程时，我们得到 `length (not False : map not [])`，现在长度的参数在 WHNF 中，因为顶层有一个构造函数 `(:)`。如果我们从中缀转换为前缀表示法并将参数写入 `length` 作为 `(:) (not False) (map not [])`，这一点会更明显。

在实践中，模式匹配并不是强制评估的唯一因素。像 `(+)` 这样的原语也强制他们的论点。

有些消息来源谈论的是“严格性”，而不是强迫，例如我们可以说 `(+)` 在两个参数中都是“严格的”。

### 10.4.2 关于共享的一句话

关于 Haskell 评估还有一件事。任何时候你给一个值一个*名称*，它就会被*共享*。这意味着名称的每次出现都指向相同的（可能未计算的）表达式。当计算表达式时，所有出现的名称都会看到结果。

让我们看一个非常简单的例子。

``` haskell
square x = x*x
```

根据前面的部分，你可能会想象评估的工作方式如下。评估首先以文本形式表示，然后以表达式树的形式直观地表示。

``` haskell
square (2+2)
  ==> (2+2) * (2+2)   -- definition of square
  ==>   4   * (2+2)   -- (*) forces left argument
  ==>   4   *   4     -- (*) forces right argument
  ==>      16         -- definition of (*)
```

![](img/square1.svg)

然而，真正发生的是变量 `x` 命名的表达式 `2+2` 仅计算一次。然后，评估结果在 `square` 内的两次出现的 `x` 之间共享。所以这是正确的评价，首先是文字上的，然后是视觉上的。请注意，现在我们有一个*表达式图*，而不是表达式树。这就是为什么 Haskell 评估有时被称为 [*图缩减*](https://en.wikipedia.org/wiki/Graph_reduction)。

``` haskell
square (2+2)
  ==> (2+2) * (2+2)
  ==>   4   *   4
  ==>      16
```

![](img/square2.svg)

作为另一个示例，请考虑下面的函数 `f` 及其评估。

``` haskell
f :: Int -> Int
f i = if i>10 then 10 else i
```

``` haskell
                  _______shared________
                 |                     |
f (1+1) ==> if (1+1)>10 then 10 else (1+1)
        ==> if 2>10 then 10 else 2
        ==> if False then 10 else 2
        ==> 2
```

Haskell 不会计算 `1+1` 两次，因为它已被命名，并且该名称被使用了两次。我们可以将其与另一个带有两个参数的函数进行对比：

``` haskell
g :: Int -> Int
g i j = if i>10 then 10 else j
```

``` haskell
                        ______no sharing_____
                       |                     |
g (1+1) (1+1) ==> if (1+1)>10 then 10 else (1+1)
              ==> if 2>10 then 10 else (1+1)
              ==> if False then 10 else (1+1)
              ==> (1+1)
              ==> 2
```

这里我们有两个不同的等效表达式名称，Haskell 并没有神奇地共享它们。自动共享等效表达式是一种称为“公共子表达式消除 (CSE)”的优化。你可以在此处了解更多信息 [CSE 和 Haskell](https://wiki.haskell.org/GHC_optimisations#Common_subexpression_elimination)。

你可以通过命名事物

- 函数参数
- `let ... in ...`
- `where`

与惰性相结合，共享意味着*一个名称最多被评估一次*。

### 10.4.3 更多示例

你将在下面找到函数 `even` 的稍微人为的递归定义。它将说明强制和共享的概念。

``` haskell
not :: Bool -> Bool
not True = False
not False = True

(||) :: Bool -> Bool -> Bool
True || _ = True
_    || x = x

even :: Int -> Bool
even x  =  x == 0  ||  not (even (x-1))
```

首先，请注意 `||` 强制其左参数，但不强制其右参数。 （换句话说，`||` *在其左参数中是严格的*。）这是因为我们只需要评估 `||` 的左参数即可知道哪个方程适用。这意味着 `even` 强制其第一个参数：

- `||` 强制 `x==0`
- `x==0` 强制 `x`

现在我们将表达式 `even 2` 计算为 WHNF。

``` haskell
even 2
==> 2 == 0  ||  not (even (2-1))                    -- apply definition of even
==> False   ||  not (even (2-1))                    -- || forces its first argument
==> not (even (2-1))                                -- second equation of ||
==> not ((2-1) == 0 || not (even ((2-1)-1)))        -- not forces its argument: apply definition of even
==> not (  1   == 0 || not (even (  1  -1)))        -- note sharing!
==> not (  False    || not (even (1-1)))
==> not (not (even (1-1)))
==> not (not ((1-1) == 0 || not (even ((1-1)-1))))
==> not (not (  0   == 0 || not (even (  0  -1))))  -- (sharing)
==> not (not (   True    || not (even (0-1))))
==> not (not True)
==> not False
==> True
```

请注意，使用此替代定义 `even` 将不起作用。你能告诉我为什么吗？

``` haskell
even' x =  not (even' (x-1))  ||  x == 0
```

现在我们可以真正理解前面的无限列表示例中发生了什么。让我们使用这些定义：

``` haskell
head (x:_) = x
head [] = -1

filter p [] = []
filter p (x:xs) = if p x
                  then x : filter p xs
                  else filter p xs

map f [] = []
map f (x:xs) = f x : map f xs

-- [0..] is syntax sugar for enumFrom 0
enumFrom n = n : enumFrom (n+1)
```

我们开始吧：

``` haskell
    head (filter (>100) (map (3^) [0..]))
=== head (filter (>100) (map (3^) (enumFrom 0)))
-- head forces filter, which forces map, which forces enumFrom. We apply the definition of enumFrom.
==> head (filter (>100) (map (3^) (0:[1..])))
-- head forces filter, which forces map. We apply the second equation of map.
==> head (filter (>100) ((3^0) : map (3^) [1..]))
-- head forces filter. We apply the second equation of filter
==> head (if ((3^0)>100)
          then (3^0) : filter (>100) (map (3^) [1..])
          else filter (>100) (map (3^) [1..]))
-- head forces if, if forces >, > forces ^. Note sharing!
==> head (if (1>100)
          then 1 : filter (>100) (map (3^) [1..])
          else filter (>100) (map (3^) [1..]))
-- head forces if, if forces >
==> head (if False
          then 1 : filter (>100) (map (3^) [1..])
          else filter (>100) (map (3^) [1..]))
-- apply definition of if
==> head (filter (>100) (map (3^) [1..]))
-- let's take slightly bigger steps now
==> head (filter (>100) (map (3^) (1:[2..])))
==> head (filter (>100) ((3^1) : map (3^) [2..]))
==> head (filter (>100) (3 : map (3^) [2..]))
==> head (filter (>100) (map (3^) [2..]))
-- and even bigger steps now
==> head (filter (>100) (9 : map (3^) [3..]))
==> head (filter (>100) (27 : map (3^) [4..]))
==> head (filter (>100) (81 : map (3^) [5..]))
==> head (filter (>100) (243 : map (3^) [6..]))
==> head (243 : filter (>100) (map (3^) [6..]))
==> 243
```

呼。

## 10.5 处理无限列表

使用列表的函数在以利用惰性的方式编写时通常具有最佳性能。尝试实现此目的的一种方法是编写可以很好地处理无限列表的列表处理函数。

要编写一个转换无限列表的函数，你需要编写一个仅查看输入列表的有限前缀的函数，然后输出 `(:)` 构造函数，然后递归。这是第一个例子。

``` haskell
everySecond :: [a] -> [a]
everySecond [] = []
everySecond (x:y:xs) = x : everySecond xs
```

``` haskell
take 10 (everySecond [0..])  ==>  [0,2,4,6,8,10,12,14,16,18]
```

编写适用于无限列表的函数的一个很好的启发是：结果的 `head` 可以便宜地评估吗？以下是两个不适用于无限输入的函数示例。对于 `mapTailRecursive` 来说，问题在于它需要在进入 WHNF 之前处理整个输入。在 `myDrop` 的情况下，问题在于它使用函数 `length`，不适用于无限列表，因为它尝试迭代直到列表末尾。

``` haskell
map :: (a -> b) -> [a] -> [b]
map _ []     = []
map f (x:xs) = f x : map f xs

mapTailRecursive :: (a -> b) -> [a] -> [b]
mapTailRecursive f xs = go xs []
    where go (x:xs) res = go xs (res++[f x])
          go []     res = res
```

``` haskell
head (map inc [0..]) ==> head (inc 0 : map inc [1..]) ==> inc 0 ==> 1
head (mapTailRecursive inc [0..])
  ==> head (go [0..] [])
  ==> head (go [1..] ([]++[inc 0]))
  ==> head (go [2..] ([]++[inc 0]++[inc 1]))
  ==> head (go [3..] ([]++[inc 0]++[inc 1]++[inc 2]))
  --  never terminates
```

``` haskell
drop :: Int -> [a] -> [a]
drop 0 xs = xs
drop _ [] = []
drop n (x:xs) = drop (n-1) xs

myDrop :: Int -> [a] -> [a]
myDrop 0 xs = xs
myDrop n xs = if n > length xs then [] else myDrop (n-1) (tail xs)
```

``` haskell
head (drop 2 [0..]) ==> head (drop 1 [1..]) ==> head (drop 0 [2..]) ==> head [2..] ==> 2
head (myDrop 2 [0..])
  ==> head (if n > length [0..] then [] else myDrop (n-1) (tail [0..]))
  ==> head (if n > 1+length [1..] then [] else myDrop (n-1) (tail [0..]))
  ==> head (if n > 1+1+length [2..] then [] else myDrop (n-1) (tail [0..]))
  ==> head (if n > 1+1+1+length [3..] then [] else myDrop (n-1) (tail [0..]))
  --  never terminates
```

几乎标准库中的所有列表函数都是以这种形式编写的，例如：

``` haskell
head (takeWhile (>=0) [0..]) ==> 0
head (concat (repeat [1,2,3])) ==> 1
head (zip [0..] [2..]) ==> (0,2)
head (filter even [3..]) ==> 4
```

## 10.6 插曲：加入严格性

还记得第 1 部分中的 `foldr` 吗？我们来看看它的近亲 `foldl`。这是列表的 `foldl` 的定义（它实际上是 `Foldable` 类型类的一部分，因此也适用于各种其他类型）。 `foldr` 从右到左处理列表，而 `foldl` 从左到右处理列表。更准确地说，`foldr` *关联到右侧*，而 `foldl` *关联到左侧*。请注意下一个示例中的差异：

``` haskell
foldr (+) 0 [1,2,3]  ==>  1+(2+(3+0))
foldl (+) 0 [1,2,3]  ==>  ((0+1)+2)+3
```

以下是 `foldl` 和 `foldr` 的定义：

``` haskell
foldl :: (a -> b -> a) -> a -> [b] -> a
foldl f z [] = z
foldl f z (x:xs) = foldl f (f z x) xs
```

``` haskell
foldr :: (a -> b -> b) -> b -> [a] -> b
foldr f y []     = y
foldr f y (x:xs) = f x (foldr f y xs)
```

作为`foldr f y (x:xs) ==> f x (foldr f y xs)`，它使得惰性评估能够在第二步中集中于`f`。因此，`foldr` 可以很好地处理惰性或短路操作：

``` haskell
    foldr (&&) True [False,False,False]
==> False && (foldr (&&) True [False,False])
==> False
```

``` haskell
    head (foldr (++) [] ["Hello","World","lorem","ipsum"])
==> head ("Hello" ++ (foldr (++) [] ["World","lorem","ipsum"]))
==> head ('H':("ello" ++ (foldr (++) [] ["World","lorem","ipsum"])))
==> 'H'
```

然而，`foldl` 需要处理整个列表才能产生（WHNF）值。原因是，只要 `foldl` 的列表参数保持非空，它就保持在最左边最外面的位置。这使得 `foldl` 成为惰性求值的优先级。仅当列表变空后，评估才会继续简化折叠值。

``` haskell
    foldl (&&) True [False,False,False]
==> foldl (&&) (True&&False) [False,False]
==> foldl (&&) ((True&&False)&&False) [False]
==> foldl (&&) (((True&&False)&&False)&&False) []
==> ((True&&False)&&False)&&False
==> (    False    &&False)&&False
==>              False    &&False
==>                      False
```

``` haskell
    head (foldl (++) [] ["Hello","World","lorem","ipsum"])
==> head (foldl (++) ([]++"Hello") ["World","lorem","ipsum"])
==> head (foldl (++) (([]++"Hello")++"World") ["lorem","ipsum"])
==> head (foldl (++) ((([]++"Hello")++"World")++"lorem") ["ipsum"])
==> head (foldl (++) (((([]++"Hello")++"World")++"lorem")++"ipsum") [])
==> head (((([]++"Hello")++"World")++"lorem")++"ipsum")
-- head forces the last ++, which forces the next-to-last ++, and so on
==> head ((("Hello"++"World")++"lorem")++"ipsum")
-- same happens again
==> head ((('H':("ello"++"World"))++"lorem")++"ipsum")
-- for clarity, let's drop the "ello"++"World" expression which isn't needed
==> head ((('H':__)++"lorem")++"ipsum")
-- now the next-to-last ++ can operate
==> head (('H':(__++"lorem"))++"ipsum")
-- let's drop the __++"lorem" expression
==> head (('H':__)++"ipsum")
-- now the last ++ can operate
==> head ('H':(__++"ipsum"))
==> 'H'
```

那么为什么要使用 `foldl` 呢？让我们再次回到第一个折叠示例。现在，由于 `+` 是严格的操作，因此两种类型的折叠都需要构建具有大量 `+` 的表达式。 Haskell 实现需要在内存中跟踪这个表达式，这就是为什么这样的问题被称为“空间泄漏”。

``` haskell
    foldr (+) 0 [1,2,3]
==> 1 + foldr (+) 0 [2,3]
==> 1 + (2 + foldr (+) 0 [3])
==> 1 + (2 + (3 + foldr (+) 0 []))
==> 1 + (2 + (3 + 0))
==> 1 + (2 + 3)
==> 1 + 5
==> 6
```

``` haskell
    foldl (+) 0 [1,2,3]
==> foldl (+) (0+1) [2,3]
==> foldl (+) ((0+1)+2) [3]
==> foldl (+) (((0+1)+2)+3) []
==> ((0+1)+2)+3
==> (  1  +2)+3
==>       3  +3
==>          6
```

现在让我们看看当我们使用 `foldl'`（强制其第二个参数的 `foldl` 的一个版本）时会发生什么！

``` haskell
    foldl' (+) 0 [1,2,3]
==> foldl' (+) (0+1) [2,3]
-- force second argument
==> foldl' (+) 1 [2,3]
==> foldl' (+) (1+2) [3]
-- force second argument
==> foldl' (+) 3 [3]
==> foldl' (+) (3+3) []
-- force second argument
==> foldl' (+) 6 []
==> 6
```

现在，工作是在扫描列表的同时逐步执行的。没有空间泄漏！有时过于惰性会导致空间泄漏，稍微严格一点就可以解决这些问题。

你可以在`Data.List`模块中找到`foldl'`，它的工作原理就像这样。但如何实现 `foldl'` 呢？我们现在当然知道如何针对特定类型（例如 `Int`）执行此操作。我们只是在第二个参数上添加模式匹配，这不会改变函数的语义。

``` haskell
foldl'Int :: (Int -> Int -> Int) -> Int -> [Int] -> Int
foldl'Int f z [] = z
foldl'Int f 0 (x:xs) = foldl'Int f (f 0 x) xs
foldl'Int f z (x:xs) = foldl'Int f (f z x) xs
```

``` haskell
    foldl'Int (+) 0 [1,2,3]
==> foldl'Int (+) (0+1) [2,3]
-- to be able to pick between the second and third equations, (0+1) is forced
==> foldl'Int (+) 1 [2,3]
-- the third equation applies
==> foldl'Int (+) (1+2) [3]
-- again, we need to pick between the second and third equations
==> foldl'Int (+) 3 [3]
==> foldl'Int (+) (3+3) []
==> 3+3
==> 6
```

要编写 `foldl'` 的通用实现，我们需要引入一个新的内置函数 `seq`。调用 `seq a b` 的计算结果为 `b`，但强制 `a` 进入 WHNF。以下是在 GHCi 中使用 `seq` 的一些示例。为了演示评估的内容，我们使用特殊值 `undefined`，如果尝试将其评估为 WHNF，则会导致错误。

``` haskell
Prelude> seq (not True) 3
3
Prelude> seq undefined 3
*** Exception: Prelude.undefined
Prelude> (seq (not True) 3) + 7
10
Prelude> (seq undefined 3) + 7
*** Exception: Prelude.undefined
Prelude> let f x = f x in seq (f 3) 3
-- ...infinite recursion
```

作为在函数中使用 `seq` 的示例，以下是 `head` 的一个版本，它不适用于无限列表（因为它计算列表的最后一个元素）：

``` haskell
strictHead :: [a] -> a
strictHead xs = seq (last xs) (head xs)
```

让我们在 GHCi 中尝试一下：

``` haskell
Prelude> head [1,2,3]
1
Prelude> strictHead [1,2,3]
1
Prelude> head (1:2:undefined)
1
Prelude> strictHead (1:2:undefined)
*** Exception: Prelude.undefined
Prelude> head [1..]
1
Prelude> strictHead [1..]
-- ...infinite recursion
```

最后，这是 `foldl'` 的定义。请注意我们如何需要引入新变量 `z'` 的共享，以便能够使 `seq` 计算新值，然后在递归调用中使用它。新的定义也被用在下面对`foldl' (+) 0 [1,2,3]`的更详细的评估中。

``` haskell
foldl' :: (a -> b -> a) -> a -> [b] -> a
foldl' f z [] = z
foldl' f z (x:xs) = let z' = f z x
                    in seq z' (foldl' f z' xs)
```

``` haskell
    foldl' (+) 0 [1,2,3]
==> seq (0+1) (foldl' (+) (0+1) [2,3])  -- seq forces first argument
          |                 |
          +-----sharing-----'
          |                 |
==> seq   1   (foldl' (+)   1   [2,3])  -- first argument to seq in WHNF, seq disappears
==> foldl' (+) 1 [2,3]
==> seq (1+2) (foldl' (+) (1+2) [3])
==> seq   3   (foldl' (+)   3   [3])
==> foldl' (+)   3   [3]
==> seq (3+3) (foldl' (+) (3+3) [])
==> seq   6   (foldl' (+)   6   [])
==> foldl' (+)   6   []
==> 6
```

我们不会在本课程中深入探讨这个主题，但重要的是你要知道 `seq` 的存在。你可以在 [the Haskell Wiki](https://wiki.haskell.org/Seq) 上找到有关 `seq` 的更多信息，并在 [Real World Haskell](https://book.realworldhaskell.org/read/profiling-and-optimization.html) 中了解有关何时需要添加严格性的更多信息。正如 [FPComplete](https://www.fpcomplete.com/blog/2017/09/all-about-strictness/) 和 [Real World Haskell](https://book.realworldhaskell.org/read/profiling-and-optimization.html) 所讨论的，通常使用*bang 模式*而不是 `seq` 更好。

## 10.7 newtype 声明

回想第 7 课。有时我们需要盒装类型。当需要装箱类型时，可以使用特殊关键字 `newtype` 来代替 `data`。 `newtype` 期望只有一个构造函数，只有一个字段。例如，

``` haskell
newtype Money = Cents Int
```

然而，下面的方法不起作用，你需要`data`：

``` haskell
-- the compiler won't accept these!
newtype Currency = Dollars Int | Euros Int
newtype Money = Money Int Int
```

那么有什么区别呢？就写代码而言，没什么。你使用 `newtype` 的方式与使用 `data` 的方式完全相同。但是，内存布局不同。使用 `data` 引入了间接层（构造函数），但使用 `newtype` 则不会。 `data` 的间接寻址对于支持多个构造函数和多个字段是必需的。举例说明：

``` haskell
code:                                 memory:

data Money = Cents Int                x --> Cents --> 100
x = Cents 100


newtype Money = Cents Int             x --> 100
x = Cents 100
```

这种差异会产生很多影响。首先，`newtype` 效率更高：类型可以说是在编译时“消失”。不过，类型仍然会被检查，因此你可以获得类型安全，而不会对性能产生任何影响。其次，新类型是“严格的”。具体来说，这意味着仅当 `x` 处于 WHNF 时，`Money x` 才处于弱头范式。这可以在 GHCi 中得到见证：

``` haskell
-- if we use data, Cents undefined is in WHNF
Prelude> data Money = Cents Int
Prelude> seq (Cents undefined) True
True
-- if we use newtype, Cents undefined isn't in WHNF, and trying
-- to make it so trips up in undefined
Prelude> newtype Money = Cents Int
Prelude> seq (Cents undefined) True
*** Exception: Prelude.undefined
```

那么什么时候应该使用`newtype`呢？一般来说，只要你有单字段单构造函数数据类型，最好使用 `newtype`。然而，如果你始终使用 `data`，则不会出现灾难性错误。当你需要为类型定义不同的类型类实例时，也经常使用 `newtype` 模式。这是一个定义具有倒序的数字类型的示例

``` haskell
newtype Inverted = Inverted Int
  deriving (Show, Eq)

instance Ord Inverted where
  compare (Inverted i) (Inverted j) = compare j i
```

``` haskell
Prelude Data.List> sort [1,2,3]
[1,2,3]
Prelude Data.List> sort [Inverted 1,Inverted 2,Inverted 3]
[Inverted 3,Inverted 2,Inverted 1]
```

## 10.8 趣味内容：打结

现在我们了解了共享和路径复制，我们可以制作自己的*循环数据结构*。还记得列表讲座中的 `cycle` 示例吗？

``` haskell
Prelude> take 21 $ cycle "asdf"
"asdfasdfasdfasdfasdfa"
```

这就是它在内存中的样子：

![](img/DAG4.svg)

早些时候有人说 Haskell 数据在内存中形成有向图。这是带有循环的有向图的示例。

我们如何定义这样的结构？我们只是给一个值一个名称，并在值本身中引用该名称。也就是说，该值是*递归*或*自引用*。这个技巧被称为“打结”。一个简单的例子：

``` haskell
  code                     memory

let xs = 1:2:xs      xs -> (1:) -> (2:) -+
 in xs                      ^            |
                            +------------+
```

请注意我们如何在 `xs` 的定义中使用名称 `xs`。当我们进行这样的递归定义时，共享会导致它在内存中变成循环结构。

一个更有趣的例子：一个简单的冒险游戏，其中的世界是一个自我参照的结构。请注意循环结构是如何使用相互引用的局部定义构建的。

``` haskell
data Room = Room String [(String,Room)]

describe :: Room -> String
describe (Room s _) = s

move :: Room -> String -> Maybe Room
move (Room _ directions) direction = lookup direction directions

world :: Room
world = meadow
  where
    meadow = Room "It's a flowery meadow next to a cliff." [("Stay",meadow),("Enter cave",cave)]
    cave = Room "You are in a cave" [("Exit",meadow),("Go deeper",tunnel)]
    tunnel = Room "This is a very dark tunnel. It seems you can either go left or right."
                  [("Go back",cave),("Go left",pit),("Go right",treasure)]
    pit = Room "You fall into a pit. There is no way out." []
    treasure = Room "A green light from a terminal fills the room. The terminal says <<loop>>."
                    [("Go back",tunnel)]

play :: Room -> [String] -> [String]
play room [] = [describe room]
play room (d:ds) = case move room d of Nothing -> [describe room]
                                       Just r -> describe room : play r ds
```

``` haskell
Prelude> play world ["Stay","Enter cave","Go deeper","Go back","Go deeper","Go right"]
["It's a flowery meadow next to a cliff.",
 "It's a flowery meadow next to a cliff.",
 "You are in a cave",
 "This is a very dark tunnel. It seems you can either go left or right.",
 "You are in a cave",
 "This is a very dark tunnel. It seems you can either go left or right.",
 "A green light from a computer terminal floods the room. The terminal says <<loop>>."]
```

这是游戏的 `world` 在内存中的样子：

                   ,-----------------,
                   v                 |
             +-----------------------|-----------------+
    meadow-->|Room "It's..." ["Stay" o, "Enter cave" o]|
             +---------------------------------------|-+
                                        ^            v
             +--------------------------|----------------+
    cave---->|Room "You are..." ["Exit" o, "Go deeper" o]|
             +-----------------------------------------|-+
                                           ^           v
             +-----------------------------|----------------------------+
    tunnel-->|Room "This is..." ["Go back" o, "Go left" o, "Go right" o]|<--------,
             +------------------------------------------|-------------|-+         |
                                                        |             |           |
                         ,------------------------------'             |           |
                         v                                            v           |
             +---------------------+                +-----------------------------|-+
    pit----->|Room "You fall..." []|    treasure--->|Room "A green..." ["Go back" o]|
             +---------------------+                +-------------------------------+

我们现在已经看到了三种类型的递归。递归函数调用自身。递归类型允许我们表达任意大的结构。递归值是实现无限结构的一种方法。

## 10.9 趣味内容：Debug.Trace

尽管 Haskell 是一种纯的编程语言，但我们有时可以通过加入一些非纯性来获得见解。

我们可以使用模块 `Debug.Trace` 中的函数 `trace :: String -> a -> a` 来查看 Haskell 评估。表达式 `trace "message" x` 与 `x` 相同，但在求值（强制）时打印 `message`。我们可以用`trace`来见证`||`算子的惰性：

``` haskell
Prelude> import Debug.Trace
Prelude Debug.Trace> trace "a" True
a
True
Prelude Debug.Trace> trace "a" False || trace "b" True
a
b
True
Prelude Debug.Trace> trace "a" True || trace "b" True
a
True
```

我们还可以看看列表元素何时被求值。请注意，`length` 不需要评估列表的元素，而 `sum` 需要评估所有元素。 （准确地说，`head xs` 实际上并不计算 `xs` 的第一个元素，而是将其返回给 GHCi，GHCi 对其进行计算以显示它。）

``` haskell
Prelude Debug.Trace> head [trace "first" 1, trace "second" 2, trace "third" 3]
first
1
Prelude Debug.Trace> last [trace "first" 1, trace "second" 2, trace "third" 3]
third
3
Prelude Debug.Trace> length [trace "first" 1, trace "second" 2, trace "third" 3]
3
Prelude Debug.Trace> sum [trace "first" 1, trace "second" 2, trace "third" 3]
third
second
first
6
```

`Debug.Trace` 还提供 `trace` 的有用变体。值得注意的是 `traceShowId x`，它打印 `show x` 并计算为 `x`。让我们使用 `traceShowId` 验证之前的 head-filter-map 示例的评估。请注意，即使我们将 `traceShowId` 映射到无限列表 `[0..]`，实际上也只评估了 6 个值。最后的 243 是返回值，而不是跟踪打印。

``` haskell
Prelude Debug.Trace> head (filter (>100) (map (\x -> traceShowId (3^x)) [0..]))
1
3
9
27
81
243
243
```

当你遇到无限递归错误时，`Debug.Trace` 特别有用。这是一个例子：

``` haskell
-- computes sums like 7+5+3+1
sumEverySecond :: Int -> Int
sumEverySecond 0 = 0
sumEverySecond n = n + sumEverySecond (n-2)
```

``` haskell
sumEverySecond 6 ==> 12
sumEverySecond 7 ==> doesn't terminate
```

我们可以通过添加 `trace` 来包装整个递归情况来调试它。

``` haskell
sumEverySecond :: Int -> Int
sumEverySecond 0 = 0
sumEverySecond n = trace ("sumEverySecond "++show n) (n + sumEverySecond (n-2))
```

``` haskell
Prelude Debug.Trace> sumEverySecond 6
sumEverySecond 6
sumEverySecond 4
sumEverySecond 2
12
Prelude Debug.Trace> sumEverySecond 7
sumEverySecond 7
sumEverySecond 5
sumEverySecond 3
sumEverySecond 1
sumEverySecond -1
sumEverySecond -3
sumEverySecond -5
-- and so on
```

啊哈！问题是我们的递归基本情况 `sumEverySecond 0` 不足以停止递归。

最后，请注意一点。使用 `trace`，尤其是 `traceShowId`，可能会导致原本不会被评估的事情得到评估。例如：

``` haskell
Prelude Debug.Trace> traceHead xs = head (traceShowId xs)
Prelude Debug.Trace> traceHead [0..]
-- never terminates since it's trying to show an infinite list
```

因此，在进行练习时，请随意使用 `Debug.Trace`，但请尝试将 `trace` 调用排除在最终答案之外。某些练习集会检查你的导入并不允许 `Debug.Trace`。

我们将在下一讲中看到处理副作用的更有原则的方法！

## 10.10 测验

以下哪些说法是正确的？

1.`reverse . reverse . reverse === reverse`
2.`reverse . reverse === reverse`
3.`reverse . id === id`

以下哪一个是以 `[0,1,2,1,2,1,2...]` 开头的无限列表？

1.`cycle [0,1,2]`
2.`0:repeat [1,2]`
3.`0:cycle [1,2]`
4.`0:[1,2..]`

评估这个表达式时下一步是什么？

``` haskell
head (map not (True:False:[]))
```

1.`head (False : True : [])`
2.`head (not True)`
3.`head (False : map not (False:[]))`
4.`head (not True : map not (False:[]))`

以下哪个值*不*处于弱头正常形式？

1.`map`
2.`f 1 : map f (2 : [])`
3.`Just (not False)`
4.`(\x -> x) True`

关于以下函数的哪些陈述是正确的？

``` haskell
f 0 x = 1+x
f _ x = 2+x
```

1. `f` 的左参数严格
2. `f` 其正确论证严格
3. `f` 强制其两个参数
4.以上都不是

此函数是否可以使用无限列表作为输入？为什么？

``` haskell
f [] = []
f (x:xs) = x : map not xs
```

1. 不可以，因为它包含一个 `[]` 情况，这是从未达到的。
2. 不，因为它使用 `map`，它评估整个列表。
3. 是的，因为它在生成 WHNF 值之前仅查看列表的第一个元素。
4. 是的，因为它调用 `map`，它适用于无限列表。

这个呢？

``` haskell
f xs = map (+(sum xs)) xs
```

1. 不，因为它使用 `map`，它评估整个列表。
2. 不可以，因为计算结果的 `head` 需要整个输入列表。
3. 是的，因为它不包括 `[]` 外壳
4. 是的，因为它调用 `map`，它适用于无限列表。

## 10.11 练习

- [Set10a](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set10a.hs)
- [Set10b](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set10b.hs)

# 11 第 11 讲：`RealWorld -> (a,RealWorld)`

## 11.1 内容

- IO

## 11.2 你被骗了！

忘记我们谈论的函数式编程和纯性。事实上，Haskell 是*世界上最好的命令式编程语言*！让我们开始吧：

``` haskell
questionnaire = do
  putStrLn "Write something!"
  s <- getLine
  putStrLn ("You wrote: "++s)
```

``` haskell
Prelude> questionnaire
Write something!
Haskell!
You wrote: Haskell!
```

读取输入和写入输出非常容易。我们还可以通过网络阅读内容。这是一个完整的 Haskell 程序，它使用 HTTP 从 URL 中获取一些单词并打印它们。

``` haskell
import Network.HTTP
import Control.Monad

main = do
  rsp <- simpleHTTP (getRequest "http://httpbin.org/base64/aGFza2VsbCBmb3IgZXZlcgo=")
  body <- getResponseBody rsp
  forM_ (words body) $ \w -> do
     putStr "word: "
     putStrLn w
```

你可以在课程仓库中找到该程序 [`exercises/Examples/FetchWords.hs`](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Examples/FetchWords.hs)，并且可以像这样运行它：

    $ cd exercises/Examples
    $ stack runhaskell FetchWords.hs
    word: haskell
    word: for
    word: ever

这是怎么回事？我们来看看类型：

``` haskell
Prelude> :t putStrLn
putStrLn :: String -> IO ()
Prelude> :t getLine
getLine :: IO String
```

`IO a` 类型的值是一个“操作”，它“生成”`a` 类型的值。所以 `getLine`是一个产生字符串的IO 操作。 `()` 类型就是所谓的*单元类型*，它的唯一值是`()`。它主要用于 IO 操作不返回任何内容（而只是有副作用）的情况。

与 Java（方法）类型的比较可能会有所帮助：

| Haskell 类型 | Java 类型 |
|:----------------------------|:-----------------------------|
| `doIt :: IO ()` | `void doIt()` |
| `getSomething :: IO Int` | `int getSomething()` |
| `force :: a -> b -> IO ()` | `void force(a arg0, b arg1)` |
| `mogrify :: c -> IO d` | `d mogrify(c arg)` |

IO 操作可以使用 *do-notation* 组合成更大的操作。

``` haskell
do operation
   operation arg
   variable <- operationThatReturnsStuff
   let var2 = expression
   operationThatProducesTheResult var2
```

### 11.2.1 示例

你可以在标准库模块 [Prelude](https://hackage.haskell.org/package/base-4.16.4.0/docs/Prelude.html) 和 [System.IO](https://hackage.haskell.org/package/base-4.16.4.0/docs/System-IO.html) 中找到有用的 IO 操作

这是一个 IO 操作，要求用户输入一个字符串，并打印出该字符串的长度。

``` haskell
query :: IO ()
query = do
  putStrLn "Write something!"                    -- run an operation, ignore produced value
  s <- getLine                                   -- run an operation, capture produced value
  let n = length s                               -- run a pure function
  putStrLn ("You wrote "++show n++" characters") -- run an operation, passing on the produced value
```

``` haskell
Prelude> query
Write something!
lorem ipsum
You wrote 11 characters
```

`do` 块的最后一行产生的值是整个块产生的值。注意 `askForALine` 与 `getLine`、`IO String` 具有相同的类型：

``` haskell
askForALine :: IO String
askForALine = do
  putStrLn "Please give me a line"
  getLine
```

除了 `query` 等 IO 操作之外，你还可以在 GHCi 中运行产生值的 IO 操作，如 `askForALine`。如果需要，你可以使用 `<-` 将操作结果捕获到变量中。

``` haskell
Prelude> askForALine
Please give me a line
this is a line
"this is a line"
Prelude> line <- askForALine
Please give me a line
this is a line
Prelude> :t line
line :: String
Prelude> line
"this is a line"
```

如果你需要提供操作参数，你可以创建*一个返回操作的函数*。请注意 `ask` 如何具有带有 `->` 的函数类型，就像普通函数一样。我们还使用正常的函数定义语法将参数命名为 `question`。

``` haskell
ask :: String -> IO String
ask question = do
  putStrLn question
  getLine
```

``` haskell
Prelude> ask "What is love?"
What is love?
Baby don't hurt me!
"Baby don't hurt me!"
Prelude> response <- ask "Who are you?"
Who are you?
The programmer.
Prelude> response
"The programmer."
Prelude> :t response
response :: String
Prelude> :t ask
ask :: String -> IO String
Prelude> :t ask "Who are you?"
ask "Who are you?" :: IO String
```

## 11.3 微妙的 `return`

Haskell 函数 `return` 的命名有点误导。在其他语言中 `return` 是一个内置关键字，但在 Haskell 中它只是一个函数。 `return :: a -> IO a` 函数获取一个值并将其转换为一个*操作，从而产生该值*。

``` haskell
produceThree :: IO Int
produceThree = return 3

printThree :: IO ()
printThree = do
  three <- produceThree
  putStrLn (show three)
```

这听起来不太有用，不是吗？与 do 符号结合起来就是这样。这里我们根据用户回答的是`Y`还是`N`返回一个布尔值：

``` haskell
yesNoQuestion :: String -> IO Bool
yesNoQuestion question = do
  putStrLn question
  s <- getLine
  return (s == "Y")
```

``` haskell
Prelude> yesNoQuestion "Fire the missiles?"
Fire the missiles?
Y
True
Prelude> answer <- yesNoQuestion "Are you sure?"
Are you sure?
N
Prelude> :t answer
answer :: Bool
Prelude> answer
False
```

**注意！** 这意味着 return *不会停止操作的执行（与 Java 或 C 中的 return 不同）。请记住，在 do 块中，最后一行决定生成哪个值。这意味着该操作产生 `2`：

``` haskell
produceTwo :: IO Int
produceTwo = do return 1
                return 2
```

``` haskell
Prelude> produceTwo
2
```

让我们换个角度来看这个问题。 `do` 表示法允许我们引起一系列副作用，并最终产生一个值。

    produceThree = do putStrLn "1"   -- side effect, produces (), which is ignored
                      return    2    -- no side effect, produces 2, which is ignored
                      getLine        -- side effect, produces a String, which is ignored
                      return    3    -- no side effect, produces 3, which is passed on

    Prelude> final <- produceThree
    1
    this line is ignored
    Prelude> final
    3

另请注意，这些是相同的操作：

``` haskell
do ...
   x <- op
   return x
```

``` haskell
do ...
   op
```

由于 `return` 是一个函数，因此你应该记住将任何复杂表达式括起来：

``` haskell
return (f x : xs)
-- alternatively:
return $ f x : xs
```

## 11.4 `do` 和类型

让我们更详细地看看 do 表示法的输入。 do-block 构建一个 `IO <something>` 类型的值。例如在

``` haskell
foo = do
  ...
  lastOp
```

`lastOp` 的类型必须是 `IO X`（对于某些 `X`）。 `foo` 的类型也将为 `IO X`。接下来我们看一个带参数的例子：

``` haskell
bar x y = do
  ...
  lastOp arg
```

`lastOp` 的类型必须为 `Y -> IO X`（以便 `lastOp arg` 的类型为 `IO X`）。 `bar` 的类型将为 `A -> B -> IO X`（在 `bar` 内部我们将有 `x :: A` 和 `y :: B`）。

如果我们使用 `return`：

``` haskell
quux x = do
  ...
  return value
```

函数 `quux` 的类型为 `A -> IO B`，其中 `x :: A` 和 `value :: B`。

接下来我们看一下`<-`的打字。如果 `op :: IO X` 并且你有 `var <- op`，则 `var` 将具有 `X` 类型。我们在许多 GHCi 示例中都看到了这一点。

`do` 的最后一行不能是 `foo <- bar`。也不可能是`let foo = bar`。最后一行确定整个操作生成的内容，因此它必须是一个操作（例如，`return something`）。

这是一个有效的例子：

``` haskell
alwaysFine :: IO Bool
alwaysFine = do
  putStrLn "What?" -- :: IO ()
  return 2         -- :: IO Int, produced value is discarded
  s <- getLine     -- getLine :: IO String, thus s :: String
  putStrLn s       -- putStrLn :: String -> IO (), thus putStrLn s :: IO ()
  let b = True     -- b :: Bool
  return b         -- :: IO Bool
                   -- Thus, alwaysFine :: IO Bool
```

类型规则保证你无法“逃脱”`IO`。即使 `<-` 从 `IO X` 为你提供 `X`，你也只能在 `do` 中使用 `<-`。然而，`do` 始终表示 `IO Y` 类型的值。换句话说：你可以暂时打开`IO`盒子，但你必须返回其中。 *“IO 中发生的事情，留在 IO 中。”*

稍后我们将详细讨论这意味着什么。现在，只要知道如果你有一个非 IO 类型的函数（例如 `myFunction :: Int -> [String] -> String`），该函数内部就不能发生 IO。它是一个纯函数。

## 11.5 控制结构

对于以下示例，我们需要两个新操作。

``` haskell
print :: Show a => a -> IO ()   -- print a value using the show function
readLn :: Read a => IO a        -- get a line and convert it to a value using the read function
```

递归、守卫和 if-then-else 等常用工具也适用于 `IO` 世界。这是使用守卫定义的 IO 操作：

``` haskell
printDescription :: Int -> IO ()
printDescription n
  | even n    = putStrLn "even"
  | n==3      = putStrLn "three"
  | otherwise = print n
```

``` haskell
Prelude> printDescription 2
even
Prelude> printDescription 3
three
Prelude> printDescription 5
5
```

以下是使用递归和模式匹配打印列表中所有数字的操作：

``` haskell
printList :: [Int] -> IO ()
printList [] = return () -- do nothing
printList (x:xs) = do print x
                      printList xs -- recursion
```

``` haskell
Prelude> printList [1,2,3]
1
2
3
```

下面是两个稍微复杂一点的递归 IO 操作的例子。他们使用递归调用产生的值。操作 `readAndSum n` 从用户读取 `n` 数字并打印它们的总和。操作`ask questions`向用户显示`questions`中的每个字符串，读取响应，并返回所有响应的列表。

``` haskell
readAndSum :: Int -> IO Int
readAndSum 0 = return 0
readAndSum n = do
  i <- readLn            -- read one number
  s <- readAndSum (n-1)  -- recursion: read and sum rest of numbers
  return (i+s)           -- produce result
```

``` haskell
Prelude> s <- readAndSum 3
2
4
5
Prelude> s
11
```

``` haskell
ask :: [String] -> IO [String]
ask [] = return []
ask (question:questions) = do
  putStr question
  putStrLn "?"
  answer <- getLine         -- get one answer
  answers <- ask questions  -- recursion: get rest of answers
  return (answer:answers)   -- produce result
```

``` haskell
Prelude> replies <- ask ["What is your name","How old are you"]
What is your name?
Yog-Sothoth
How old are you?
The question is meaningless
Prelude> replies
["Yog-Sothoth","The question is meaningless"]
```

此外，我们还有一些 `IO` 特定的控制结构，或者更确切地说，函数。这些来自模块 `Control.Monad`。

``` haskell
-- when b op performs op if b is true
when :: Bool -> IO () -> IO ()
-- unless b op performs op if b is false
unless :: Bool -> IO () -> IO ()
-- do something many times, collect results
replicateM :: Int -> IO a -> IO [a]
-- do something many times, throw away the results
replicateM_ :: Int -> IO a -> IO ()
-- do something for every list element
mapM :: (a -> IO b) -> [a] -> IO [b]
-- do something for every list element, throw away the results
mapM_ :: (a -> IO b) -> [a] -> IO ()
-- the same, but arguments flipped
forM  :: [a] -> (a -> IO b) -> IO [b]
forM_ :: [a] -> (a -> IO b) -> IO ()
```

使用这些，我们可以重写之前的示例：

``` haskell
printList :: [Int] -> IO ()
printList xs = mapM_ print xs
```

``` haskell
readAndSum n = do
  numbers <- replicateM n readLn
  return (sum numbers)
```

``` haskell
ask :: [String] -> IO [String]
ask questions = do
  forM questions askOne

askOne :: String -> IO String
askOne question = do
  putStr question
  putStrLn "?"
  getLine
```

## 11.6 关于 `do` 和缩进

使用 do 表示法时很容易遇到奇怪的缩进问题。这里有一些经验法则可以帮助你正确行事。

do 和缩进最重要的规则是*do 块中的所有操作必须在同一列中开始*。

此规则的一些示例：

``` haskell
-- This is not OK, putStrLn is way too left
foo = do y <- getLine
   putStrLn y

-- This is not OK either
foo = do y <- getLine
           putStrLn y

-- This is OK
foo = do y <- getLine
         putStrLn y

-- This is also OK: putting a line break after do
foo = do
  y <- getLine
  putStrLn y
```

一个相关的规则是*当一个操作跨越多行时，缩进后续行*。如果不缩进，它看起来就像是一个新操作！

``` haskell
-- This is not OK, the string starts a new operation
quux = do putStrLn
          "this long string"
          print 1

-- This is OK
quux = do putStrLn
            "this long string"
          print 1
```

这是另一个示例，其中包含嵌套的 do 块和两个不同的有效缩进。

``` haskell
-- This is OK
foo x = do quux
           y <- blorg
           when y (do thing
                      otherThing)
           return 3

-- This is also OK: starting putting a line break after do, using $
foo x = do
  quux
  y <- blorg
  when y $ do
    thing
    otherThing
  return 3
```

## 11.7 来写一个程序

在所有这些简短的一次性示例之后，让我们转向更长一些的示例。让我们编写一个程序来从所有 `.hs` 文件中获取所有类型注释。我们使用 `readFile` 和 `listDirectory` 等 IO 操作来读取和查找文件，也使用 `map` 和 `filter` 等纯代码来进行实际处理。首先，回顾一下我们正在使用的库操作：

``` haskell
-- split string into lines
lines :: String -> [String]
-- `isSuffixOf suf list` is true if list ends in suf
Data.List.isSuffixOf :: Eq a => [a] -> [a] -> Bool
-- `isInfixOf inf list` is true if inf occurs inside list
Data.List.isInfixOf :: Eq a => [a] -> [a] -> Bool
-- FilePath is just an alias for String
type FilePath = String
-- get entire contents of file
readFile :: FilePath -> IO String
-- list files in directory
System.Directory.listDirectory :: FilePath -> IO [FilePath]
-- is the given file a directory?
System.Directory.doesDirectoryExist :: FilePath -> IO Bool
```

这是程序本身。你还可以在课程仓库中找到它，名称为 [`exercises/Examples/ReadTypes.hs`](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Examples/ReadTypes.hs)。

``` haskell
module Examples.ReadTypes where

import Control.Monad (forM)
import Data.List (isInfixOf, isSuffixOf)
import System.Directory (listDirectory, doesDirectoryExist)

-- a line is a type signature if it contains :: but does not contain =
isTypeSignature :: String -> Bool
isTypeSignature s = not (isInfixOf "=" s) && isInfixOf "::" s

-- return list of types for a .hs file
readTypesFile :: FilePath -> IO [String]
readTypesFile file
  | isSuffixOf ".hs" file = do content <- readFile file
                               let ls = lines content
                               return (filter isTypeSignature ls)
  | otherwise             = return []

-- list children of directory, prepend directory name
qualifiedChildren :: String -> IO [String]
qualifiedChildren path = do childs <- listDirectory path
                            return (map (\name -> path++"/"++name) childs)

-- get type signatures for all entries in given directory
-- note mutual recursion with readTypes
readTypesDir :: String -> IO [String]
readTypesDir path = do childs <- qualifiedChildren path
                       typess <- forM childs readTypes
                       return (concat typess)

-- recursively read types contained in a file or directory
-- note mutual recursion with readTypesDir
readTypes :: String -> IO [String]
readTypes path = do isDir <- doesDirectoryExist path
                    if isDir then readTypesDir path else readTypesFile path

-- main is the IO action that gets run when you run the program
main :: IO ()
main = do ts <- readTypes "."
          mapM_ putStrLn ts
```

我们可以通过进入目录 `exercises/Examples` 并运行以下命令来运行该程序：

``` haskell
$ stack runhaskell ReadTypes.hs
deposit :: String -> Int -> Bank -> Bank
withdraw :: String -> Int -> Bank -> (Int,Bank)
runBankOp :: BankOp a -> Bank -> (a,Bank)
... and so on
```

当然，确切的输出会根据目录的内容而有所不同。

## 11.8 这一切意味着什么？

让我们回到函数世界。我们如何协调 IO 操作与 Haskell 作为一种“纯”和“惰性”语言的关系？像 `putStrLn :: String -> IO ()` 这样的东西是一个返回操作的*纯*函数。怎样才算纯呢？当 `x` 相同时，`putStrLn x`也相同。换句话说：操作是一系列副作用的“纯描述”。只有*执行*该操作才会导致这些副作用。当 Haskell 程序运行时，只执行一个操作 - 它称为 `main :: IO ()`。其他操作只需链接到`main`即可运行。

在 GHCi 中，如果你输入的表达式计算结果为某个操作，GHCi 会为你运行该操作。这是 `print` 纯性的演示：

``` haskell
Prelude> x = print 1   -- creates operation, doesn't run it
Prelude> x             -- runs the operation
1
Prelude> x             -- runs it again!
1
```

*操作是值*，就像数字、列表和函数一样。我们可以编写对操作进行操作的代码。该函数需要两个操作，`a` 和 `b`，并返回一个操作，询问用户想要运行哪一个操作。

``` haskell
choice :: IO x -> IO x -> IO x
choice a b =
  do putStr "a or b? "
     x <- getLine
     case x of "a" -> a
               "b" -> b
               _ -> do putStrLn "Wrong!"
                       choice a b
```

``` haskell
Prelude> choice (putStrLn "A!!!!") (putStrLn "B!!!!")
a or b? z
Wrong!
a or b? a
A!!!!
```

使用指定为参数的操作可以让我们编写像我们之前遇到的 `mapM_` 这样的函数。实现是一个递归 IO 操作，将另一个IO 操作作为参数。概念上很复杂，但是当你阅读代码时就很简单：

``` haskell
mapM_ :: (a -> IO b) -> [a] -> IO ()
mapM_ op     [] = return ()       -- do nothing for an empty list
mapM_ op (x:xs) = do op x         -- run operation on first element
                     mapM_ op xs  -- run operation on rest of list, recursively
```

``` haskell
Prelude> mapM_ print [1,2,3]
1
2
3
```

## 11.9 还有一件事：IORef

到目前为止，我们能够在 IO 中产生的唯一副作用是终端（`getLine`、`print`）和文件（`readFile`、`listDirectory`）IO。用 Java、Python 或 C 编写的命令式程序也有其他类型的副作用，我们无法用纯 Haskell 来表达。其中之一是*可变（即可变）状态*。纯函数无法读取可变状态，因为否则同一函数的两次调用可能不会返回相同的值。

模块 `Data.IORef` 中的 Haskell 类型 `IORef a` 是对 `a` 类型值的可变引用

``` haskell
newIORef :: a -> IO (IORef a)                -- create a new IORef containing a value
readIORef :: IORef a -> IO a                 -- produce value contained in IORef
writeIORef :: IORef a -> a -> IO ()          -- set value in IORef
modifyIORef :: IORef a -> (a -> a) -> IO ()  -- modify value contained in IORef with a pure function
```

以下是在 GHCi 中使用 IORef 的一些示例：

``` haskell
Prelude> :m +Data.IORef
Prelude Data.IORef> myRef <- newIORef "banana"
Prelude Data.IORef> readIORef myRef
"banana"
Prelude Data.IORef> writeIORef myRef "apple"
Prelude Data.IORef> readIORef myRef
"apple"
Prelude Data.IORef> modifyIORef myRef reverse
Prelude Data.IORef> readIORef myRef
"elppa"
```

下面是使用 `IORef` 对列表中的值求和的示例。请注意与命令式循环的相似之处。

``` haskell
sumList :: [Int] -> IO Int
sumList xs = do r <- newIORef 0                       -- initialize r to 0
                forM_ xs (\x -> modifyIORef r (x+))   -- for every xs, add it to r
                readIORef r                           -- get last value of r
```

大多数时候不需要使用 `IORef`。 Haskell 风格更喜欢递归、参数和返回值。然而，现实世界的程序有时可能需要一两个 IORef。

## 11.10 IO 总结

`IO X` 类型的值是*IO 操作*，*在运行时**生成*X 类型的值。操作是纯值。只有“运行”该操作才会产生副作用。

IO 操作可以使用 `do` 表示法组合在一起：

``` haskell
op :: X -> IO Y
op arg = do operation                 -- run operation
            operation2 arg            -- run operation with argument
            result <- operation3 arg  -- run operation with argument, store result
            let something = f result  -- run a pure function f, store result
            finalOperation            -- last operation produces the the return value
```

`return x`运算是总是产生值`x`的运算。当`x :: a`、`return x :: IO a`时。

有用的 IO 操作：

``` haskell
-- printing & reading
putStr :: String -> IO ()
putStrLn :: String -> IO ()
print :: Show a => a -> IO ()
getLine :: IO String
readLn :: Read a => IO a

-- control structures from Control.Monad
when :: Bool -> IO () -> IO ()   -- when b op performs op if b is true
unless :: Bool -> IO () -> IO () -- unless b op performs op if b is false
replicateM :: Int -> IO a -> IO [a]   -- do something many times, collect results
replicateM_ :: Int -> IO a -> IO ()   -- do something many times, throw away the results
mapM ::  (a -> IO b) -> [a] -> IO [b] -- do something for every list element
mapM_ :: (a -> IO b) -> [a] -> IO ()  -- do something for every list element, throw away the results
forM ::  [a] -> (a -> IO b) -> IO [b] -- the same, but arguments flipped
forM_ :: [a] -> (a -> IO b) -> IO ()

-- files
readFile :: FilePath -> IO String
```

## 11.11 测验

这个IO 操作的类型是什么？

``` haskell
foo x = do putStrLn x
           y <- getLine
           return (length y)
```

1.`String -> IO String`
2.`IO Int`
3.`String -> IO Int`
4.`IO String -> IO Int`

以下哪一行可以用来代替 `????`

``` haskell
quux :: String -> IO [String]
quux q = do y <- getLine
            z <- getLine
            putStrLn (y++z)
            ????
```

1.`q <- getLine`
2.`return (y++z)`
3.`return [q]`
4.`ans <- return [y,z]`

`blorg [1,2,3]` 打印什么值？也就是说，它调用 `print x` 的值是 `x`。 `blorg` 产生的值不计算在内。

``` haskell
blorg [] = return 0
blorg (x:xs) = do m <- blorg xs
                  print x
                  return (m+x)
```

1.打印`1`、`2`、`3`
2.打印`1`、`2`、`3`、`6`
3.打印`3`、`2`、`1`
4.打印`3`、`2`、`1`、`6`

`Int -> IO Int` 类型的函数可以执行以下哪些操作？

1. 无法定义该类型的函数。
2. 返回一个常量值。
3. 运行给定的IO 操作并返回其值。
4. 向用户查询数字并返回。

`IO Int -> Int` 类型的函数可以执行以下哪些操作？

1. 无法定义该类型的函数。
2. 返回一个常量值。
3. 运行给定的IO 操作并返回其值。
4. 向用户查询数字并返回。

## 11.12 练习

- [Set11a](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set11a.hs) - 基本IO练习
- [Set11b](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set11b.hs) - 高级 IO 练习

# 12 第 12 讲：fmap fmap fmap

## 12.1 内容

- Functor

## 12.2 Functor

### 12.2.1 保留结构

还记得列表的 `map` 函数吗？再次给出定义：

``` haskell
map :: (a -> b) -> [a] -> [b]
map _ []     = []
map g (x:xs) = g x : map g xs
```

它将函数 `g :: a -> b` 应用于 `[a]` 类型列表的每个元素，返回 `[b]` 类型列表。表达 `map` 类型的另一种方法是 `(a -> b) -> ([a] -> [b])`。这是相同的类型，因为 `->` 关联到右侧。额外的括号强调了 `map` 将函数 `g :: a -> b` 转换为函数 `map g :: [a] -> [b]` 的事实。这意味着`map`是一个将函数转换为函数的“高阶函数”。

由于 `map` 是参数多态的，因此它的定义不依赖于列表中存储的值的类型。因此，每个 `a -> b` 类型的函数都使用完全相同的逻辑转换为 `[a] -> [b]` 类型的函数。使用上面的定义，我们可以看到：

``` haskell
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

这里值得注意的是 `map` *保留了列表的结构*。列表的长度和元素的相对位置是相同的。总体思路如下图所示。

![将函数 g 映射到列表](img/map.svg)

将函数 `g` 映射到列表

让我们看看是否还能找到其他类似的函数。 `Maybe a` 类型的值有点像长度最多为 1 的列表。让我们映射一个 `Maybe`！你能看出和`map`的定义有相似之处吗？

``` haskell
mapMaybe :: (a -> b) -> Maybe a -> Maybe b
mapMaybe f Nothing = Nothing
mapMaybe f (Just x) = Just (f x)
```

这里，值的结构也被保留。 `Nothing` 变为 `Nothing`，`Just` 变为 `Just`。在这里，我们也可以将类型视为 `(a -> b) -> (Maybe a -> Maybe b)`，将普通函数转换（或“提升”）为可在 Maybes 上运行的函数。

还有一个例子：考虑二叉树。

``` haskell
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

``` haskell
map      :: (a -> b) ->      [a] ->      [b]
mapMaybe :: (a -> b) -> Maybe a  -> Maybe b
mapTree  :: (a -> b) -> Tree  a  -> Tree  b
```

编写类型类的天真尝试会遇到问题。如果我们尝试抽象 `Maybe c`，似乎我们无法为映射操作编写正确的类型。我们需要能够以某种方式更改类型参数 `c`。

``` haskell
class Mappable m where
  mapThing :: (a -> b) -> m -> m

instance Mappable (Maybe c) where
  mapThing :: (a -> b) -> Maybe c -> Maybe c
  mapThing = ...
```

幸运的是，Haskell 类型类有一个我们以前没有介绍过的函数。除了类型之外，你还可以为“类型构造函数”编写类。这意味着什么？让我们看一下标准类型类 `Functor`，它执行我们尝试对 `Mappable` 执行的操作。

``` haskell
class Functor f where
  fmap :: (a -> b) -> f a -> f b
```

请注意类型参数 `f` 是如何成为类型构造函数的：它在 `fmap` 类型的不同部分中传递 `a` 和 `b` 参数。现在让我们看看 `Maybe` 的实例。

``` haskell
instance Functor Maybe where
  -- In this instance, the type of fmap is:
  -- fmap :: (a -> b) -> Maybe a -> Maybe b
  fmap f Nothing = Nothing
  fmap f (Just x) = Just (f x)
```

现在 `fmap` 有了正确的类型，我们可以像 `mapMaybe` 一样实现它！请注意我们如何声明 `instance Functor Maybe` 而不是 `instance Functor (Maybe a)`。类型 `Maybe a` 不是Functor，类型构造函数 `Maybe` 才是。

列表的类型构造函数写作`[]`。它是特殊的语法，就像其他列表语法一样。但是，如果类型 `[a]` 写为 `List a`，则类型构造函数 `[]` 将表示 `List`。

``` haskell
instance Functor [] where
  fmap = map
```

这是我们的最后一个示例，作为 `Functor` 实例。

``` haskell
data Tree a = Leaf | Node a (Tree a) (Tree a)

instance Functor Tree where
  fmap _ Leaf = Leaf
  fmap f (Node val left right) = Node (f val) (fmap f left) (fmap f right)
```

附注：Functor一词最初来自数学的一个分支，称为[范畴论](https://en.wikipedia.org/wiki/Category_theory)。然而，要使用 Haskell，你不需要了解任何范畴论。随着你学习 Haskell 的进展，你可能会对范畴论感兴趣，它可能是编程新思想的宝贵来源。范畴论可能会让人感到害怕，所以很高兴知道没有它你也能过得很好。现在，当你看到Functor时，你可以只想“我可以映射的东西”，或者也许是“一个容器”。

让我们缩小一点。当我们有一个实例 `Functor MyFun` 时，我们知道我们可以将类型 `X` 映射到新类型 `MyFun X` （因为 `MyFun` 是类型构造函数），而且我们可以将采用 `X` 参数的函数 `f` 提升到采用 `MyFun X` 参数的函数 `fmap f` ！所以你可以说我们在类型级别和值级别上进行映射。

哦，对了，还有一件事。一旦掌握了 `fmap` 的窍门，你可能会发现自己经常使用它。对于大量使用 `fmap` 的代码，最好使用其中缀别名 `<$>`。考虑这些示例中 `$` 和 `<$>` 之间的对称性：

``` haskell
(+1) <$> [1,2,3]    ==>  [2,3,4]
not <$> Just False  ==>  Just True

reverse . tail  $       "hello"       ==>  "olle"
reverse . tail <$> Just "hello"       ==>  Just "olle"
-- which is the same as
fmap (reverse . tail) (Just "hello")  ==>  Just "olle"
```

## 12.3 合法实例

上面提到的“保留结构”到底是什么？以下两个“Functor法则”预计适用于任何 `Functor` 实例 `f`（尽管不幸的是 Haskell 编译器无法强制执行它们）：

1.`fmap id === id`
2.`fmap (f . g) === fmap f . fmap g`

如果这听起来很抽象，请不要担心！第一定律规定Functor将 `id :: a -> a` 映射到 `id :: f a -> f a`。 （`id` 是恒等函数，意思是 `id x = x`。）让我们具体来看一下它对于列表 `[1,2,3]` 是如何工作的：

``` haskell
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

``` haskell
id [1,2,3] ==> [1,2,3]
```

因此，`fmap id [1,2,3]` 的结果与 `id [1,2,3]` 的结果相同，因此第一Functor定律在这种情况下成立。不难证明第一Functor定律适用于任何列表。

如果你仔细想想，第一Functor定律确实是一个非常简单的命题。它只是说，如果我们将 `fmap` 应用于不改变任何内容的函数 (`id`)，则结果函数 (`fmap id`) 再次不改变任何内容。因此，应用 `fmap` 的行为本身保留了Functor的结构。

第二Functor定律怎么样？对于列表，考虑如果我们 `fmap` 函数 `negate.(*2)` （记住，`negate` 将 `x` 映射到 `-x` 并且 `(*2)` 将其参数乘以 `2`）会发生什么：

``` haskell
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

在这种情况下，让我们考虑第二个Functor定律的右侧：

``` haskell
(fmap negate . fmap (*2)) [1,2,3] ==> (map negate . map (*2)) [1,2,3]
                                  ==> map negate (map (*2) [1,2,3])
                                  ==> map negate [2,4,6]
                                  ==> [-2,-4,-6]
```

第二个Functor定律在这个特殊情况下成立。事实上，它适用于所有情况（练习！）。

一般来说，第二Functor定律表示，首先组合两个函数然后应用 `fmap` 必须产生与对这些函数执行 `fmap` 然后组合结果函数相同的结果。换句话说，应用 `fmap` 和组合的顺序并不重要。 （这两个操作被称为“通勤”。）

还有一些高阶函数无法满足Functor定律。考虑函数 `badMap`：

``` haskell
badMap :: (a -> b) -> [a] -> [b]
badMap f [] = []
badMap f (x:y:xs) = f x : badMap f xs
badMap f (x:xs) = f x : badMap f xs
```

该函数违反了第一Functor定律。例如：

``` haskell
badMap id [1,2,3] ==> badMap id (1:2:[3])
                  ==> id 1 : badMap id [3]
                  ==> 1 : badMap id [3]
                  ==> 1 : badMap id (3:[])
                  ==> 1 : id 3 : badMap id []
                  ==> 1 : 3 : []
                  ==> [1,3]
```

将 `badMap id` 应用于列表 `[1,2,3]` 会更改列表，因为元素 `2` 被删除。

如前所述，Haskell 编译器无法检测Functor是否遵守其法则。 Haskell 编译器很乐意接受使用 `badMap` 而不是 `map` 作为 `fmap` 实现的实例 `Functor []`。这是 Haskell 类型系统的限制。有些技术（例如 LiquidHaskell）或依赖类型语言（例如 Agda、Idris、Coq 或 Lean）实际上可以强制执行Functor法则，以便非法Functor实例无法编译。然而，这些技术超出了本课程的范围。

## 12.4 附注：Kind

请记住，`Functor` 是类型构造函数的类。如果我们尝试为类型定义 `Functor` 的实例，我们会收到错误：

``` haskell
Prelude> instance Functor Int where

<interactive>:1:18: error:
    • Expected kind ‘* -> *’, but ‘Int’ has kind ‘*’
    • In the first argument of ‘Functor’, namely ‘Int’
      In the instance declaration for ‘Functor Int’
```

错误消息谈论*种类*。种类是*类型的类型*。像 `Int`、`Bool` 或 `Maybe Int` 这样可以包含值的类型具有 `*` 类型。类型构造函数具有看起来像函数的类型，例如，`Maybe` 具有 `* -> *` 类型。这意味着 `Maybe` 类型构造函数必须应用于 `*` 类型，才能获得 `*` 类型。

我们可以向 GHCi 询问类型的种类：

``` haskell
Prelude> :kind Int
Int :: *
Prelude> :kind Maybe
Maybe :: * -> *
Prelude> :kind Maybe Int
Maybe Int :: *
```

如果我们向 GHCi 询问有关 `Functor` 类的信息，它会告诉我们 `Functor` 的实例必须具有类型 `* -> *`：

``` haskell
Prelude> :info Functor
class Functor (f :: * -> *) where
  fmap :: (a -> b) -> f a -> f b
...
```

以下是一些更复杂类型的示例。

``` haskell
-- multiple type parameters
Prelude> :kind Either
Either :: * -> * -> *
Prelude> data Either3 a b c = Left a | Middle b | Right c
Prelude> :kind Either3
Either3 :: * -> * -> * -> *
-- a type parameter of kind *->*
Prelude> data IntInside f = IntInside (f Int)
Prelude> :kind IntInside
IntInside :: (* -> *) -> *
```

在 Haskell 编程中你不会经常遇到类型，但有时你会看到谈论类型的错误消息，所以了解它们是什么是很有好处的。

## 12.5 再谈 `Foldable`

我们在第 1 部分中简要介绍了 `Foldable` 类，它出现在基本函数的许多类型签名中。例如：

``` haskell
length :: Foldable t => t a -> Int
sum :: (Foldable t, Num a) => t a -> a
minimum :: (Foldable t, Ord a) => t a -> a
foldMap :: (Foldable t, Monoid m) => (a -> m) -> t a -> m
```

从这些类型签名中我们可以看到，`Foldable`，就像`Functor`一样，是一个类型构造函数的类（类似`* -> *`的东西）。 `Foldable` 的本质是成为一个“可以折叠的东西”的类。类的定义可以很简单

``` haskell
class Foldable (t :: *->*) where
  foldr :: (a -> b -> b) -> b -> t a -> b
```

然而，出于性能原因，该类包含许多方法（你可以通过在 GHCi 中检查 `:info Foldable` 来亲自查看它们！），但是当我们为 `Foldable` 定义实例时，仅定义 `foldr` 就足够了。

`Foldable` 类的另一种思考方式是*从左到右*处理元素，换句话说，如果 `Functor` 是容器的类，那么 `Foldable` 就是*有序容器*的类。

作为示例，让我们为我们自己的配对类型实现 `Functor` 和 `Foldable` 。

``` haskell
data Pair a = Pair a a
  deriving Show

instance Functor Pair where
  -- fmap f applies f to all values
  fmap f (Pair x y) = Pair (f x) (f y)

instance Foldable Pair where
  -- just like applying foldr over a list of length 2
  foldr f initialValue (Pair x y) = f x (f y initialValue)

-- an example function that uses both instances
doubleAndCount :: (Functor f, Foldable f) => f Int -> Int
doubleAndCount = sum . fmap (*2)
```

现在，我们几乎可以在任何可以使用列表的地方使用 `Pair`：

``` haskell
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

因此，总而言之，Functor是类型构造函数 `f` 和相应的 `Functor f` 实例，使得 `fmap` 满足两个Functor定律。这些定律断言 `fmap` 必须保留恒等函数并分布于函数组合上。更通俗地说，`fmap` 将基于值操作的函数 `g :: a -> b` 提升为基于容器操作的函数：`fmap g :: f a -> f b`。基本上 Haskell 中所有表现良好的数据结构都是Functor。

## 12.7 测验

`fmap`是什么类型的？

1.`a -> b -> f a -> f b`
2.`(a -> b) -> f a -> f b`
3.`Functor f => a -> b -> f a -> f b`
4.`Functor f => (a -> b) -> f a -> f b`

哪个代码片段完成下一个 `Functor` 实例？

``` haskell
data Container x = Things x [x]

instance Functor Container where
  ????
```

1.`fmap f (Things x ys) = Things (f x) [f x]`
2.`fmap f (Things x ys) = Things (f x) (map f ys)`
3.`fmap f (Things x ys) = Things (f x) ys`
4.`fmap f (Things x ys) = f (Things x ys)`

`[a]`是什么样的？

1.`*`
2.`* -> *`
3.`[a]`

`Foo`是什么样的？

``` haskell
data Foo x = FooConst
```

1.`*`
2.`* -> *`
3.`Foo`

`Bar`是什么样的？

``` haskell
data Bar = Baz | Qux Int
```

1.`*`
2.`* -> *`
3.`Bar`

`foldr (-) 1 (Just 2)` 的价值是多少？

1.-1
2.1
3.`Just -1`
4.`Just 1`

哪个代码片段完成下一个 `Foldable` 实例？

``` haskell
data Container x = Things x [x]

instance Foldable Container where
  ????
```

1.`foldr f z (Things x ys) = f x z`
2.`foldr f z (Things x ys) = foldr f x ys`
3.`foldr f z (Things x ys) = f x (foldr f z ys)`
4.`foldr f z (Things x ys) = foldr f z (x:ys)`

## 12.8 练习

- [组12](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set12.hs)

# 13 第 13 讲：问题范畴中的幺半群

- Monad

在本次讲座中，我们将使用许多示例来构建 *monad* 的概念。现在你应该熟悉理解 monad 所需的所有 Haskell 函数。

Monad 是编程中众所周知的难题，部分原因是奇怪的术语，部分原因是糟糕的教程，部分原因是在学习 Haskell 时过早地试图理解 monad。在课程后期引入 Monad 是为了让理解它们更容易。

如果你觉得本讲座很难，请不要绝望，许多其他人也觉得这个话题很难。有很多高效的 Haskell 程序员已经成功地理解了 monad，所以这项任务并非没有希望。

最后要注意的是：Monad和Functor一样，是一个最初来自称为范畴论的数学分支的概念。然而，我无论如何强调这一点都不为过，*你不需要了解任何东西，甚至不需要关心范畴论来理解 Haskell 编程中的 monad。* 就像人们可以在不了解对象或函数理论的情况下使用面向对象编程或函数式编程一样，人们可以在不了解与其相关的数学的情况下使用 monad。对于函数式程序员来说，范畴论可能是一个有益的主题，但它不是强制性的。

## 13.1 示例 1：Maybe

当使用许多 `Maybe` 值时，代码往往会变得有点混乱。让我们看一些例子。首先，我们组合一些返回 `Maybe String` 的函数。注意我们在 `stealSecret` 中需要的嵌套 `case`：写起来并不有趣。

``` haskell
-- Try to login with a password.
-- `Just username` on success, `Nothing` otherwise.
login :: String -> Maybe String
login "f4bulous!" = Just "unicorn73"
login "swordfish" = Just "megahacker"
login _           = Nothing

-- Get a secret associated with a user.
-- Not all users have secrets.
secret :: String -> Maybe String
secret "megahacker" = Just "I like roses"
secret _            = Nothing

-- Login and return the user's secret, if any
stealSecret :: String -> Maybe String
stealSecret password =
  case login password of
    Nothing -> Nothing
    Just user -> case secret user of
                   Nothing -> Nothing
                   Just s -> Just ("Stole secret: "++s)
```

``` haskell
stealSecret "swordfish"  ==>  Just "Stole secret: I like roses"
stealSecret "f4bulous!"  ==>  Nothing
stealSecret "wrong_password"  ==>  Nothing
```

接下来，我们修改对列表。我们使用 Prelude 中的 `Maybe` 返回函数 `lookup`。这里我们有一个 if 位于一个 case 中，而不是一个嵌套的 case。

``` haskell
-- Get the value corresponding to a key from a key-value list.
lookup :: (Eq a) => a -> [(a, b)] -> Maybe b
```

``` haskell
-- Set the value of key to val in the given key-value list,
-- but only if val is larger than the current value!
increase :: Eq a => a -> Int -> [(a,Int)] -> Maybe [(a,Int)]
increase key val assocs =
  case lookup key assocs
  of Nothing -> Nothing
     Just x -> if (val < x)
                then Nothing
                else Just ((key,val) : delete (key,x) assocs)
```

这种类型的代码非常常见，并且通常重复相同的模式：如果任何中间结果是 `Nothing`，则整个结果是 `Nothing`。让我们尝试通过定义一个*链接运算符* `?>` 来更轻松地编写这样的代码。链接运算符获取结果和下一步计算，并且仅当结果是 `Just` 值时才运行下一步。

``` haskell
(?>) :: Maybe a -> (a -> Maybe b) -> Maybe b
-- if we failed, don't even bother running the next step:
Nothing ?> _ = Nothing
-- otherwise run the next step:
Just x  ?> f = f x
```

链接运算符很好地简化了我们的示例。请注意我们如何定义简单的辅助函数来处理计算的一步，而不是编写一个大表达式。

``` haskell
stealSecret :: String -> Maybe String
stealSecret password =
    login password ?>
    secret ?>
    decorate
  where decorate s = Just ("Stole secret: "++s)
```

``` haskell
increase :: Eq a => a -> Int -> [(a,Int)] -> Maybe [(a,Int)]
increase key val assocs =
    lookup key assocs ?>
    check ?>
    buildResult
  where check x
           | val < x   = Nothing
           | otherwise = Just x
        buildResult x = Just ((key,val) : delete (key,x) assocs)
```

这是另一个示例：从 `safeHead` 和 `safeTail` 构建的安全列表索引：

``` haskell
safeHead :: [a] -> Maybe a
safeHead [] = Nothing
safeHead (x:xs) = Just x

safeTail :: [a] -> Maybe [a]
safeTail [] = Nothing
safeTail (x:xs) = Just xs

safeThird :: [a] -> Maybe a
safeThird xs = safeTail xs ?> safeTail ?> safeHead

safeNth :: Int -> [a] -> Maybe a
safeNth 0 xs = safeHead xs
safeNth n xs = safeTail xs ?> safeNth (n-1)
```

``` haskell
safeThird [1,2,3,4]
  ==> Just 3
safeThird [1,2]
  ==> Nothing
safeNth 5 [1..10]
  ==> Just 6
safeNth 11 [1..10]
  ==> Nothing
```

P.S. 请注意，`?>` *关联到左侧*，这是 Haskell 中的默认值。这意味着 `op ?> f ?> g` 意味着 `(op ?> f) ?> g`。另一种选择是，`op ?> (f ?> g)` 甚至不会进行类型检查！

附注：这个 `?>` 运算符表达了在其他语言中非常常见的 if-result 模式。以下是用 Python 和 Java 编写 `op val ?> f` 的方法。

``` python
# Python
x = op(val)
if x:
  f(x)
```

``` java
// Java
Object x = op(val);
if (x != null) {
  f(x);
}
```

if-result 模式和 `?>` 之间的区别在于，我们使用 `Nothing` 值来显式表示失败，而不是依赖于 Python 中任何变量都可以是 `None`（或 `False`）这一事实，或者 Java 中任何 `Object` 引用都可以是 `null` 的事实。

## 13.2 示例 2：日志

让我们用另一个例子来探讨链接的概念：日志记录。类型 `Logger` 表示一个值加上一个日志消息列表（由生成该值的计算生成）。

``` haskell
-- Logger definition
data Logger a = Logger [String] a  deriving Show

getVal :: Logger a -> a
getVal (Logger _ a) = a
getLog :: Logger a -> [String]
getLog (Logger s _) = s

-- Primitive operations:
nomsg :: a -> Logger a
nomsg x = Logger [] x        -- a value, no message

annotate :: String -> a -> Logger a
annotate s x = Logger [s] x  -- a value and a message

msg :: String -> Logger ()
msg s = Logger [s] ()        -- just a message
```

这是一个 `login` 函数，用于记录有关其处理的用户名和密码的一些详细信息。请注意，当我们需要处理多个 `Logger` 值时，我们如何在 `login` 中遇到复杂的代码。

``` haskell
validateUser :: String -> Logger Bool
validateUser "paul.atreides" = annotate "Valid user" True
validateUser "ninja" = nomsg True
validateUser u = annotate ("Invalid user: "++u) False

checkPassword :: String -> String -> Logger Bool
checkPassword "paul.atreides" "muad'dib" = annotate "Password ok" True
checkPassword "ninja"         ""         = annotate "Password ok" True
checkPassword _               pass       = annotate ("Password wrong: "++pass) False

login :: String -> String -> Logger Bool
login user password =
  let validation = validateUser user
  in if (getVal validation)
       then let check = checkPassword user password
            in Logger (getLog validation ++ getLog check) (getVal check)
       else validation
```

``` haskell
login "paul.atreides" "muad'dib"
  ==> Logger ["Valid user","Password ok"] True
login "paul.atreides" "arrakis"
  ==> Logger ["Valid user","Password wrong: arrakis"] False
login "ninja" ""
  ==> Logger ["Password ok"] True
login "leto.atreides" "paul"
  ==> Logger ["Invalid user: leto.atreides"] False
```

让我们尝试通过为 `Logger` 定义链接运算符来简化此代码。进行多个 `Logger` 操作时，重要的是保留所有日志。这是一个链接运算符 `#>`，以及如何使用它来记录一些算术计算的示例。

``` haskell
(#>) :: Logger a -> (a -> Logger b) -> Logger b
Logger la a #> f = let Logger lb b = f a  -- feed value to next step
                   in Logger (la++lb) b   -- bundle result with all messages
```

``` haskell
-- square a number and log a message about it
square :: Int -> Logger Int
square val = annotate (show val ++ "^2") (val^2)

-- add 1 to a number and log a message about it
add :: Int -> Logger Int
add val = annotate (show val ++ "+1") (val+1)

-- double a number and log a message about it
double :: Int -> Logger Int
double val = annotate (show val ++ "*2") (val*2)

-- compute the expression 2*(x^2+1) with logging
compute :: Int -> Logger Int
compute x =
    square x
    #> add
    #> double
```

``` haskell
compute 3
  ==> Logger ["3^2","9+1","10*2"] 20
```

通过使用 `#>`，我们可以大大简化 `login`。请注意，我们不需要担心将日志组合在一起。另请注意我们如何使用 lambda 表达式而不是定义辅助函数。

``` haskell
login :: String -> String -> Logger Bool
login user password =
  validateUser user
  #>
  \valid -> if valid then checkPassword user password
                     else nomsg False
```

为了让事情变得更糟，让我们在递归列表处理函数中使用 `Logger`。这是 `filter` 的日志版本。请注意代码如何在递归调用之前链接日志消息，以保持日志条目的良好顺序。

``` haskell
-- sometimes you don't need the previous value:
(##>) :: Logger a -> Logger b -> Logger b
Logger la _ ##> Logger lb b = Logger (la++lb) b

filterLog :: (Eq a, Show a) => (a -> Bool) -> [a] -> Logger [a]
filterLog f [] = nomsg []
filterLog f (x:xs)
   | f x       = msg ("keeping "++show x) ##> filterLog f xs #> (\xs' -> nomsg (x:xs'))
   | otherwise = msg ("dropping "++show x) ##> filterLog f xs
```

``` haskell
filterLog (>0) [1,-2,3,-4,0]
  ==> Logger ["keeping 1","dropping -2","keeping 3","dropping -4","dropping 0"] [1,3]
```

## 13.3 示例 3：维护状态

在前面的示例中，我们只是写入了一些状态（日志）。有时我们需要改变某种共享状态的计算。让我们看看一家小银行的账户。我们首先为银行状态定义一个数据类型：所有帐户的余额，作为从帐户名称到余额的映射。

``` haskell
import qualified Data.Map as Map

data Bank = Bank (Map.Map String Int)
  deriving Show
```

以下是我们如何将钱存入帐户的方法。我们使用 `Data.Map` 中的函数 `adjust` 来修改地图。

``` haskell
-- Apply a function to one value in a map
Map.adjust :: Ord k => (a -> a) -> k -> Map.Map k a -> Map.Map k a
```

``` haskell
deposit :: String -> Int -> Bank -> Bank
deposit accountName amount (Bank accounts) =
  Bank (Map.adjust (\x -> x+amount) accountName accounts)
```

提款有点复杂，因为我们要处理一些特殊情况，比如账户不存在，或者账户没有足够的钱。我们使用库函数 `findWithDefault` 来帮助我们。

``` haskell
-- Fetch the value corresponding to a key from a map,
-- or a default value in case the key does not exist
Map.findWithDefault :: Ord k => a -> k -> Map.Map k a -> a
```

``` haskell
withdraw :: String -> Int -> Bank -> (Int,Bank)
withdraw accountName amount (Bank accounts) =
  let -- balance is 0 for a nonexistant account
      balance = Map.findWithDefault 0 accountName accounts
      -- can't withdraw over balance
      withdrawal = min amount balance
      newAccounts = Map.adjust (\x -> x-withdrawal) accountName accounts
  in (withdrawal, Bank newAccounts)
```

最后，让我们编写一个函数，从一个账户中提取最多 100 块钱，将钱分成两半，然后存入两个账户。请注意我们需要如何仔细地线程化银行的不同版本，`bank`、`bank1`、`bank2` 和 `bank3`，以确保所有交易以正确的顺序发生。

``` haskell
share :: String -> String -> String -> Bank -> Bank
share from to1 to2 bank =
  let (amount,bank1) = withdraw from 100 bank
      half = div amount 2
      -- carefully preserve all money, even if amount was an odd number
      rest = amount-half
      bank2 = deposit to1 half bank1
      bank3 = deposit to2 rest bank2
  in bank3
```

``` haskell
share "wotan" "siegfried" "brunhilde"
      (Bank (Map.fromList [("brunhilde",0),("siegfried",0),("wotan",1000)]))
   ==> Bank (Map.fromList [("brunhilde",50),("siegfried",50),("wotan",900)])

share "wotan" "siegfried" "brunhilde"
      (Bank (Map.fromList [("brunhilde",0),("siegfried",0),("wotan",91)]))
   ==> Bank (Map.fromList [("brunhilde",46),("siegfried",45),("wotan",0)])
```

当你对一个值进行串行更新，同时还执行一些其他计算时，这样的代码经常出现在 Haskell 中。很容易犯错误，并且类型系统不会帮助你，例如， 重用 `bank1` 值。让我们重写 `share` ，这样我们就不需要引用银行本身了。我们可以再次使用相同的链接思想来完成此任务。

``` haskell
-- `BankOp a` is an operation that transforms a Bank value,
-- while returning a value of type `a`
data BankOp a = BankOp (Bank -> (a,Bank))

-- running a BankOp on a Bank
runBankOp :: BankOp a -> Bank -> (a,Bank)
runBankOp (BankOp f) bank = f bank

-- Running one BankOp after another
(+>>) :: BankOp a -> BankOp b -> BankOp b
op1 +>> op2 = BankOp combined
  where combined bank = let (_,bank1) = runBankOp op1 bank
                        in runBankOp op2 bank1

-- Running a parameterized BankOp, using the value returned
-- by a previous BankOp.  The implementation is a bit tricky
-- but it's enough to understand how +> is used for now.
(+>) :: BankOp a -> (a -> BankOp b) -> BankOp b
op +> parameterized = BankOp combined
  where combined bank = let (a,bank1) = runBankOp op bank
                        in runBankOp (parameterized a) bank1

-- Make a BankOp out of deposit.
-- There is no return value so we use ().
depositOp :: String -> Int -> BankOp ()
depositOp accountName amount = BankOp depositHelper
  where depositHelper bank = ((), deposit accountName amount bank)

-- Make a BankOp out of withdraw. Note how
--   withdraw accountName amount :: Bank -> (Int,Bank)
-- is almost a BankOp already!
withdrawOp :: String -> Int -> BankOp Int
withdrawOp accountName amount = BankOp (withdraw accountName amount)
```

让我们看看连锁如何与这些银行业务配合使用。

``` haskell
Prelude> bank = Bank (Map.fromList [("edsger",10),("grace",50)])

-- Running a number of operations using +>>

Prelude> runBankOp (depositOp "edsger" 1) bank
((),Bank (fromList [("edsger",11),("grace",50)]))

Prelude> runBankOp (depositOp "edsger" 1 +>> depositOp "grace" 1) bank
((),Bank (fromList [("edsger",11),("grace",51)]))

Prelude> runBankOp (depositOp "edsger" 1 +>> depositOp "grace" 1 +>> withdrawOp "edsger" 11) bank
(11,Bank (fromList [("edsger",0),("grace",51)]))

-- Using +> to implement a transfer from one account to the other:

Prelude> runBankOp (withdrawOp "edsger" 5 +> depositOp "grace") bank
((),Bank (fromList [("edsger",5),("grace",55)]))

Prelude> runBankOp (withdrawOp "edsger" 100 +> depositOp "grace") bank
((),Bank (fromList [("edsger",0),("grace",60)]))
```

请注意 `BankOp` 类型的值如何表示转换银行的过程。存储体的初始状态必须使用 `runBankOp` 提供。这是有道理的，因为与 `Bank` 状态不同，`BankOp` 变换可以组合。必须使用 `runBankOp` 使得“定义”操作和“执行它们”之间的区别更加清晰。

现在我们已经熟悉了 `BankOp` 值的操作，我们可以将 `share` 实现为 `BankOp`。我们实现了一个助手 `distributeOp` 以使代码更加整洁。

``` haskell
-- distribute amount to two accounts
distributeOp :: String -> String -> Int -> BankOp ()
distributeOp to1 to2 amount =
  depositOp to1 half
  +>>
  depositOp to2 rest
  where half = div amount 2
        rest = amount - half

shareOp :: String -> String -> String -> BankOp ()
shareOp from to1 to2 =
  withdrawOp from 100
  +>
  distributeOp to1 to2
```

``` haskell
runBankOp (shareOp "wotan" "siegfried" "brunhilde")
          (Bank (Map.fromList [("brunhilde",0),("siegfried",0),("wotan",1000)]))
  ==> ((),Bank (Map.fromList [("brunhilde",50),("siegfried",50),("wotan",900)]))

runBankOp (shareOp "wotan" "siegfried" "brunhilde")
          (Bank (Map.fromList [("brunhilde",0),("siegfried",0),("wotan",91)]))
  ==> ((),Bank (Map.fromList [("brunhilde",46),("siegfried",45),("wotan",0)]))
```

那很干净不是吗？我们根本不需要提及银行，我们几乎可以像使用命令式语言一样进行编程，同时保持完全纯粹性。

你可以在课程仓库的 [`exercises/Examples/Bank.hs`](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Examples/Bank.hs) 下找到所有这些代码。

## 13.4 终于：Monad 类型类

我们现在已经看到了三种具有链接操作的不同类型：

``` haskell
(?>) :: Maybe a -> (a -> Maybe b) -> Maybe b
(#>) :: Logger a -> (a -> Logger b) -> Logger b
(+>) :: BankOp a -> (a -> BankOp b) -> BankOp b
```

就像之前的 `map` 和 `Functor` 一样，有一个类型类可以捕获此模式。请注意，`Monad` 是*类型构造函数*的类，就像 `Functor` 一样。

``` haskell
class Monad m where
  (>>=) :: m a -> (a -> m b) -> m b
```

`Monad` 中还有一些额外的操作：

``` haskell
  -- lift a normal value into the monad
  return :: a -> m a
  -- simpler chaining (like our ##>)
  (>>) :: m a -> m b -> m b
  a >> b  =  a >>= \_ -> b     -- remember: _ means ignored argument
```

回想一下，`Functor` 类是关于通用 `map` 操作的。类似地，`Monad` 类只是一个通用的链接操作。

``` haskell
fmap :: Functor f => (a->b) -> f a -> f b
(>>=) :: Monad m => m a -> (a -> m b) -> m b
```

表达式 `operation >>= next` 采用一元运算 `operation :: m a`，并使用 `next :: a -> m b` 生成的值进行一些进一步的计算。如果这感觉太抽象，只需回想一下 `Maybe` 的链接是如何工作的：

``` haskell
(>>=) :: Maybe a -> (a -> Maybe b) -> Maybe b
-- if we failed, don't even bother running the next step
Nothing >>= _ = Nothing
-- otherwise run the next step
Just x  >>= f = f x
```

## 13.5 Maybe 是 Monad！

这是 `Maybe` 的完整 `Monad` 实例和一些示例。

``` haskell
instance  Monad Maybe  where
    (Just x) >>= k      = k x
    Nothing  >>= _      = Nothing

    (Just _) >>  k      = k
    Nothing  >>  _      = Nothing

    return x            = Just x
```

``` haskell
Just 1 >>= \x -> return (x+1)
  ==> Just 2
Just "HELLO" >>= (\x -> return (length x)) >>= (\x -> return (x+1))
  ==> Just 6
Just "HELLO" >>= \x -> Nothing
  ==> Nothing
Just "HELLO" >> Just 2
  ==> Just 2
Just 2 >> Nothing
  ==> Nothing
```

以下是用 monad 操作重写的 `stealSecret` 和 `increase` 示例。更改为 `?>` 至 `>>=` 和 `Just` 至 `return`。

``` haskell
stealSecret :: String -> Maybe String
stealSecret password =
    login password >>=
    secret >>=
    decorate
  where decorate s = return ("Stole secret: "++s)
```

``` haskell
-- Set the value of key to val in the given key-value list,
-- but only if val is larger than the current value!
increase :: Eq a => a -> Int -> [(a,Int)] -> Maybe [(a,Int)]
increase key val assocs =
    lookup key assocs >>=
    check >>=
    buildResult
  where check x
           | val < x   = Nothing
           | otherwise = return x
        buildResult x = return ((key,val) : delete (key,x) assocs)
```

## 13.6 `do` 的回归

下面是一个复杂 monad 操作的示例。

``` haskell
f = op1 >>= continue
  where continue  x   = op2 >> op3 >>= continue2 x
        continue2 x y = op4 >> op5 x y
```

让我们看看稍微改变一下这段代码会发生什么。首先，让我们内联定义。

``` haskell
f = op1 >>= (\x ->
               op2 >>
               op3 >>= (\y ->
                          op4 >>
                          op5 x y))
```

由于 lambda 表达式一直持续到表达式末尾，因此我们可以省略括号。我们也以不同的方式缩进。

``` haskell
f = op1 >>= \x ->
    op2 >>
    op3 >>= \y ->
    op4 >>
    op5 x y
```

现在我们可以注意到与 `do` 表示法的相似性。下面的 `do` 块实际上是相同的代码！

``` haskell
f = do x <- op1
       op2
       y <- op3
       op4
       op5 x y
```

澄清一下，`do` 表示法只是 monad 操作（`>>=` 和 `>>`）和 lambda 的更好语法。以下是 do 表示法如何转换为 monad 操作。笔记！该定义是递归的。

``` haskell
do x <- op a       ~~~>       op a >>= \x -> do ...
   ...
```

``` haskell
do op a            ~~~>       op a >> do ...
   ...
```

``` haskell
do let x = expr    ~~~>       let x = expr in do ...
   ...
```

``` haskell
do finalOp         ~~~>       finalOp
```

这是使用 do 表示法的 `safeNth`：

``` haskell
safeHead :: [a] -> Maybe a
safeHead [] = Nothing
safeHead (x:xs) = Just x

safeTail :: [a] -> Maybe [a]
safeTail [] = Nothing
safeTail (x:xs) = Just xs

safeNth :: Int -> [a] -> Maybe a
safeNth 0 xs = safeHead xs
safeNth n xs = do t <- safeTail xs
                  safeNth (n-1) t
```

这是 `increase` 最后一次，现在用 do 符号

``` haskell
-- Set the value of key to val in the given key-value list,
-- but only if val is larger than the current value!
increase :: Eq a => a -> Int -> [(a,Int)] -> Maybe [(a,Int)]
increase key val assocs =
  do oldVal <- lookup key assocs
     check oldVal
     return ((key,val) : delete (key,oldVal) assocs)
  where check x
           | val < x   = Nothing
           | otherwise = return x
```

## 13.7 Logger 是 Monad！

我们应该能够通过将 `>>=` 设置为 `#>` 自己为 `Logger` 编写 `Monad` 实例。然而，由于[Haskell 语言的最新变化](https://wiki.haskell.org/Functor-Applicative-Monad_Proposal)，我们必须实现 `Functor` 和 `Applicative` 实例才能实现 `Monad` 实例。 `Functor` 我们已经见过了，但是`Applicative`是什么？我们稍后会知道。让我们来实现实例：

``` haskell
import Control.Monad

data Logger a = Logger [String] a  deriving Show

msg :: String -> Logger ()
msg s = Logger [s] ()

-- The Functor instance just maps over the stored value
instance Functor Logger where
  fmap f (Logger log x) = Logger log (f x)

-- This is an Applicative instance that works for any
-- monad, you can just ignore it for now. We'll get back
-- to Applicative later.
instance Applicative Logger where
  pure = return
  (<*>) = ap

-- Finally, the Monad instance
instance Monad Logger where
  return x = Logger [] x
  Logger la a >>= f = Logger (la++lb) b
    where Logger lb b = f a
```

我们不再需要 `nomsg` 操作，因为它只是 `return`。我们还可以使用 monad 操作重新实现 `annotate` 操作。

``` haskell
nomsg :: a -> Logger a
nomsg x = return x

annotate :: String -> a -> Logger a
annotate s x = msg s >> return x
```

以下是使用 do 表示法重写的 `compute` 和 `filterLog` 示例。请注意 `filterLog` 与 do 表示法的配合有多好。

``` haskell
compute x = do
  a <- annotate "^2" (x*x)
  b <- annotate "+1" (a+1)
  annotate "*2" (b*2)

filterLog :: (Show a) => (a -> Bool) -> [a] -> Logger [a]
filterLog f [] = return []
filterLog f (x:xs)
   | f x       = do msg ("keeping "++show x)
                    xs' <- filterLog f xs
                    return (x:xs')
   | otherwise = do msg ("dropping "++show x)
                    filterLog f xs
```

``` haskell
compute 3
  ==> Logger ["^2","+1","*2"] 20
filterLog (>0) [1,-2,3,-4,0]
  ==> Logger ["keeping 1","dropping -2","keeping 3","dropping -4","dropping 0"] [1,3]
```

## 13.8 State Monad

Haskell 的 `State` monad 是我们的 `BankOp` 类型的通用版本。 `State` 类型由两种类型参数化，第一个是状态类型，第二个是生成值的类型。 `State Bank a` 相当于我们的 `BankOp a`。你可以在[`transformers`包的模块`Control.Monad.Trans.State`](https://downloads.haskell.org/~ghc/latest/docs/html/libraries/transformers-0.5.6.2/Control-Monad-Trans-State.html)中找到`State` monad。这是 `State` 的简化实现。

``` haskell
data State s a = State (s -> (a,s))

runState (State f) s = f s

-- operation that overwrites the state (and produces ())
put :: s -> State s ()
put state = State (\oldState -> ((),state))

-- operation that produces the current state
get :: State s s
get = State (\state -> (state,state))

-- operation that modifies the current state with a function (and produces ())
modify :: (s -> s) -> State s ()
modify f = State (\state -> ((), f state))

-- Functor and Applicative instances skipped

instance Monad (State s) where
  return x = State (\s -> (x,s))

  op >>= f = State h
    where h state0 = let (val,state1) = runState op state0
                         op2 = f val
                     in runState op2 state1
```

注意我们如何声明一个实例 `Monad (State s)`。我们使用“部分应用的类型构造函数”，因为 `Monad` 的实例只能为多采用一个类型参数的类型构造函数声明。如果你看看下面的 `>>=` 类型中 `m`、`Maybe` 和 `State` 是如何出现的，这可能会更清楚一些。

``` haskell
class Monad m where
  (>>=) :: m a -> (a -> m b) -> m b

instance Monad Maybe where
  (>>=) :: Maybe a -> (a -> Maybe b) -> Maybe b

instance Monad (State s) where
  (>>=) :: State s a -> (a -> State s b) -> State s b
```

让我们看一下使用 `State` 的一些示例。首先，让我们考虑 `State Int a` 类型的计算，它代表使用简单的计数器。

``` haskell
-- adds i to the value of the counter
add :: Int -> State Int ()
add i = do old <- get
           put (old+i)
```

``` haskell
runState (add 1 >> add 3 >> add 5 >> add 6) 0
  ==> ((),15)
```

``` haskell
example :: State Int Int
example = do add 3           -- increment state by 3
             value <- get    -- value is current state, i.e. initial+3
             add 1000        -- increment state by 1000
             put (value + 1) -- overwrite state with value+1, i.e. initial+4
             return value    -- produce value, i.e. intial+3
```

``` haskell
runState example 1
  ==> (4,5)           -- initial is 1, state is initial+4=5, produces initial+3=4
```

请注意 `State s a` 类型的值如何表示转换状态的过程（就像 `BankOp` 一样）。必须使用 `runState` 提供初始状态。同样，必须使用 `runState` 使得“定义”操作和“执行它们”之间的区别更加清晰。

处理列表时，状态可以替换累加器参数。下面是两个示例：查找列表中最大的元素，以及查找列表中紧接在 `0` 之后出现的值。

``` haskell
findLargest :: Ord a => [a] -> State a ()
findLargest [] = return ()
findLargest (x:xs) = do
  modify (\y -> max x y)  -- update state with max of current value and previous largest value
  findLargest xs          -- process rest of list
```

``` haskell
runState (findLargest [1,2,7,3]) 0  ==>  ((),7)
```

``` haskell
-- store the given value in the state list
remember :: a -> State [a] ()
remember x = modify (x:)

valuesAfterZero :: [Int] -> ((),[Int])
valuesAfterZero xs = runState (go xs) []
  where go :: [Int] -> State [Int] ()
        go (0:y:xs) = do remember y
                         go (y:xs)
        go (x:xs) = go xs
        go [] = return ()
```

``` haskell
valuesAfterZero [0,1,2,3,0,4,0,5,0,0,6]
  ==> ((),[6,0,5,4,1])
```

**注意！** 顺便说一句，`State` monad 的实际实现没有像我们的简化示例那样的 `State` 构造函数。如果你想将函数包装到 `State` 操作中，请改用此帮助程序：

    state :: (s -> (a, s)) -> State s a

## 13.9 `mapM` 的回归

IO 讲座中的控制结构适用于*所有 monad*。这是他们的实际类型。

``` haskell
when :: Monad m => Bool -> m () -> m ()        -- conditional operation
unless :: Monad m => Bool -> m () -> m ()      -- same, but condition is flipped
replicateM :: Monad m => Int -> m a -> m [a]   -- do something many times
replicateM_ :: Monad m => Int -> m a -> m ()   -- same, but ignore the results
mapM :: Monad m => (a -> m b) -> [a] -> m [b]  -- do something on a list's elements
mapM_ :: Monad m => (a -> m b) -> [a] -> m ()  -- same, but ignore the results
forM  :: Monad m => [a] -> (a -> m b) -> m [b] -- mapM but arguments reversed
forM_ :: Monad m => [a] -> (a -> m b) -> m ()  -- same, but ignore the results
```

正如我们在这里看到的，我们可以在迄今为止遇到的所有 monad 上使用 `mapM`：

``` haskell
mapM (\x -> if (x>0) then Just (x-1) else Nothing) [1,2,3]  ==>  Just [0,1,2]
mapM (\x -> if (x>0) then Just (x-1) else Nothing) [1,0,3]  ==>  Nothing

mapM (\x -> msg "increment" >> msg (show x) >> return (x+1)) [1,2,3]
  ==> Logger ["increment","1","increment","2","increment","3"] [2,3,4]

runState (mapM (\x -> modify (x+) >> return (x+1)) [1,2,3]) 0
  ==> ([2,3,4],6)
```

更多示例：

``` haskell
safeHead :: [a] -> Maybe a
safeHead [] = Nothing
safeHead (x:xs) = Just x
firsts :: [[a]] -> Maybe [a]
firsts xs = forM xs safeHead
```

``` haskell
firsts [[1,2,3],[4,5],[6]] ==> Just [1,4,6]
firsts [[1,2,3],[],[6]]    ==> Nothing
```

``` haskell
-- an abbreviated version of an example from the last section
findLargest :: Ord a => [a] -> State a ()
findLargest xs = mapM_ update xs
  where update x = modify (\y -> max x y)
```

``` haskell
runState (findLargest [1,2,7,3]) 0  ==>  ((),7)
```

``` haskell
let increment = modify (+1) >> get
    ops = replicateM 4 increment
in runState ops 0
  ==> ([1,2,3,4],4)
```

这是使用 `State` monad 重新实现的 `filter`：

``` haskell
rememberElements :: (a -> Bool) -> [a] -> State [a] ()
rememberElements f xs = mapM_ maybePut xs
  where maybePut x = when (f x) (modify (++[x]))

sfilter :: (a -> Bool) -> [a] -> [a]
sfilter f xs = finalState
  where (_, finalState) = runState (rememberElements f xs) []
```

``` haskell
sfilter even [1,2,3,4,5]
  ==> [2,4]
```

我们可以编写自己的适用于所有 monad 的操作。正如我们之前所见，这是通过类型类实现的。如果你仅使用 `return` 和 do-notation 等 monad 操作，类型系统将为你的函数推断出通用类型。

``` haskell
mywhen b op = if b then op else return ()

mymapM_ op [] = return ()
mymapM_ op (x:xs) = do op x
                       mymapM_ op xs
```

``` haskell
*Main> :t mywhen
mywhen :: (Monad m) => Bool -> m () -> m ()
*Main> :t mymapM_
mymapM_ :: (Monad m) => (t -> m a) -> [t] -> m ()
```

我们可以在每个示例 monad 中使用这些通用操作：

``` haskell
perhapsDecrease :: Int -> Maybe Int
perhapsDecrease x = do
  mywhen (x<=0) Nothing
  return (x-1)
```

``` haskell
perhapsDecrease 2  ==>  Just 1
perhapsDecrease 0  ==>  Nothing
```

``` haskell
search :: (Show a, Eq a) => a -> [a] -> Logger ()
search x ys = mymapM_ look ys
  where look y = mywhen (x==y) (msg ("Found "++show y))
```

``` haskell
search 3 [1,2,3,4,3,2]  ==>  Logger ["Found 3","Found 3"] ()
```

``` haskell
sumPositive :: [Int] -> State Int ()
sumPositive xs = mymapM_ f xs
  where f x = when (x>0) (modify (x+))
```

``` haskell
runState (sumPositive [1,-4,2,3]) 0  ==>  ((),6)
```

## 13.10 Monad 是 Functor

尚未引入一项有用的操作：`liftM`。

``` haskell
liftM :: Monad m => (a->b) -> m a -> m b
liftM f op = do x <- op
                return (f x)
```

`liftM` 操作使得使用纯单元部分编写代码变得容易。

``` haskell
liftM negate (Just 3)
  ==> Just (-3)

liftM sort $ firsts [[4,6],[2,1,0],[3,3,3]]
  ==> Just [2,3,4]

runState (liftM negate get) 3
  ==> (-3,3)
```

`liftM` 的类型是不是很眼熟？就像`fmap`的类型一样！事实上，为 monad 定义Functor实例很容易：只需设置 `fmap = liftM` 即可。由于如今每个 `Monad` 都需要是 `Functor`，因此现代 Haskell 风格更喜欢 `fmap` 而不是 `liftM`。

``` haskell
fmap :: Functor f => (a->b) -> f a -> f b
```

``` haskell
fmap negate (Just 3)
  ==> Just (-3)

fmap sort $ firsts [[4,6],[2,1,0],[3,3,3]]
  ==> Just [2,3,4]

runState (fmap negate get) 3
  ==> (-3,3)
```

## 13.11 又一个 Monad

*列表 monad*（即 `[]` 的 `Monad` 实例）表示具有*多个返回值*的计算。它对于搜索替代方案很有用。这是第一个例子。对于每个 `x`，我们都生产 `x` 和 `-x`：

``` haskell
[1,2,3] >>= \x -> [-x,x]
  ==> [-1,1,-2,2,-3,3]
```

我们可以通过生成一个空列表来过滤掉不合适的值：

``` haskell
[1,2,3] >>= \x -> if x>1 then [x] else []
  ==> [2,3]
```

如果我们使用 do 表示法，列表 monad 开始看起来更像是一个循环结构：

``` haskell
do word <- ["Blue", "Green"]
   number <- [1,2,3]
   return (word ++ show number)
  ==> ["Blue1","Blue2","Blue3","Green1","Green2","Green3"]
```

更有趣的例子：找到列表中总和为 `k` 的所有对。 （同一元素两次算作一对。）

``` haskell
findSum :: [Int] -> Int -> [(Int,Int)]
findSum xs k = do a <- xs
                  b <- xs
                  if (a+b==k) then [(a,b)] else []
```

``` haskell
findSum [1,2,3,4,5] 5
  ==> [(1,4),(2,3),(3,2),(4,1)]
```

最后一个更复杂的例子。我们使用列表 monad 从字符串中查找所有回文，然后找到最长的一个。

``` haskell
import Data.List (sortBy)

substrings :: String -> [String]
substrings xs = do start <- [0..length xs - 1]
                   end <- [start+1..length xs - 1]
                   return $ drop start $ take end $ xs

palindromesIn :: String -> [String]
palindromesIn xs = do s <- substrings xs
                      if (s==reverse s) then return s else []

longestPalindrome xs = head . sortBy f $ palindromesIn xs
  where f s s' = compare (length s') (length s)  -- longer is smaller
```

``` haskell
palindromesIn "aabbacddcaca"
  ==> ["a","aa","a","abba","b","bb","b","a","acddca","c","cddc","d","dd","d","c","cac","a","c"]
longestPalindrome "aabbacddcaca"
  ==> "acddca"
```

这是列表 monad 的令人惊讶的简单实现：

``` haskell
instance Monad [] where
  return x = [x]                  -- an operation that produces one value
  lis >>= f = concat (map f lis)  -- compute f for all values, combine the results
```

实际上，我们之前已经以列表推导的形式见过列表Monad。将 `findSum` 的重新实现与使用 `do` 表示法的早期实现进行比较。

``` haskell
findSum :: [Int] -> Int -> [(Int,Int)]
findSum xs k = [(a,b) | a <- xs, b <- xs, a+b==k ]
```

## 13.12 哦对，IO

你现在可能已经猜到了，`IO` 是一个 monad。然而，`IO` 类型和 `instance Monad IO` 的实现是编译器内置的。你无法仅使用标准 Haskell 来实现 IO monad，这与 `Maybe` monad、`State` monad 和我们见过的其他 monad 不同。

然而，真正的副作用符合 monad 模式，就像 `State` 和 `Maybe` 一样。就像其他 monad 一样，我们将“操作的纯定义”与“运行操作”的过程分开。作为奖励，你可以将所有通用 monad 操作（`mapM` 等）与 IO 一起使用。

下面是一些使用 monad 操作编写 IO 的示例。

``` haskell
printTwoThings :: IO ()
printTwoThings = putStrLn "One!" >> print 2

echo :: IO ()
echo = getLine >>= putStrLn

verboseEcho :: IO ()
verboseEcho = getLine >>= \s -> putStrLn ("You wrote: " ++ s)

query :: String -> IO String
query question = putStrLn question >> getLine

confirm :: String -> IO Bool
confirm question = putStrLn question >> fmap interpret getLine
    where interpret "Y" = True
          interpret _ = False
```

``` haskell
Prelude> printTwoThings
One!
2

Prelude> verboseEcho
The Iliad
You wrote: The Iliad

Prelude> answer <- query "Why am I here?"
Why am I here?
Good question!
Prelude> answer
"Good question!"

Prelude> b <- confirm "Fire warheads?"
Fire warheads?
no no no no
Prelude> b
False
Prelude> b <- confirm "Make love, not war?"
Make love, not war?
Y
Prelude> b
True
```

## 13.13 其他语言中的 Monad

一旦你熟悉了 monad 的概念，你也会开始在其他语言中看到类似 monad 的东西。最著名的例子是 *Option types*、*Java Streams* 和 *JavaScript Promise* 。如果你以前了解这些语言或概念，你可能会发现本节很有启发性。如果你不这样做，请随意跳过此步骤。

### 13.13.1 选项

许多语言都有[选项类型](https://en.wikipedia.org/wiki/Option_type)。该类型在Java中称为`Optional<T>`，在C++中称为`std::optional<T>`，在C#中称为`Nullable<T>`，等等。这些类型通常具有类似于 Haskell `Maybe` monad 的行为，例如：

- 在 Java 中，[`Optional.flatMap`](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/Optional.html#flatMap(java.util.function.Function)) 对应于 `>>=`：它允许你将 `Function<T,<Optional<U>>` 应用于 `Optional<T>` 并获得 `Optional<U>`。
- 在 C# 中，二进制运算自动[提升](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/nullable-value-types#lifted-operators) 为 `Nullable` 类型。例如，`a + null` 变为 `null`。

### 13.13.2 流

[Java Streams](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/stream/Stream.html) 也有一个类似 monad 的 API。流是关于增量地产生许多值。就像Optional一样，方法`Stream.flatMap`让我们获取`Stream<T>`，将其与`Function<T,Stream<U>>`组合并得到`Stream<U>`。

例如，如果 `lines` 是 `Stream<String>`，`words` 接受 `String` 并返回 `Stream<String>`，`readInt` 接受 `String` 并返回 `Integer`，我们可以编写：

``` java
Stream<Integer> parseNumbers(Stream<String> lines) {
    return lines.flatMap(words).map(read);
}
```

这对应于以下 Haskell 列表 monad 代码：

``` haskell
parseNumbers :: [String] -> [Int]
parseNumbers strings = fmap read (strings >>= words)
```

``` haskell
parseNumbers ["123 456","7 89"]  ==>  [123,456,7,89]
```

### 13.13.3 承诺

关于 JavaScript 中的 Promise 是否*真的*是 monad，存在[很多](https://hackernoon.com/functional-javascript-functors-monads-and-promises-679ce2ab8abe)[分歧](https://stackoverflow.com/questions/45712106/why-are-promises-monads)。然而，一些相似之处是显而易见的。

首先，考虑 `Promise.then` 和 `>>=` 之间的相似之处。两者都采用*操作*（承诺或Monad操作），并将其与返回新操作的函数结合起来。

``` javascript
function concatPromises(promise1, promise2) {
  return promise1.then(value1 => promise2.then(value2 => value1+value2));
}
```

``` javascript
>> concatPromises(Promise.resolve("abc"), Promise.resolve("def")).then(console.log)
abcdef
```

``` haskell
concatMonadic :: Monad m => m String -> m String -> m String
concatMonadic op1 op2 = op1 >>= (\value1 -> op2 >>= (\value2 -> return (value1++value2)))
```

``` haskell
Prelude> concatMonadic (Just "abc") (Just "def")
Just "abcdef"
```

接下来，让我们考虑一下 async/await 和 do-notation 之间的相似之处。两者都是使用原始 `Promise.then` 或 `>>=` 机制的更好语法。我们使用 async/await 重新实现 `concatPromises`，使用 do-notation 重新实现 `concatMonadic`。他们的行为保持不变。

``` javascript
async function concatPromises(promise1, promise2) {
  let value1 = await promise1;
  let value2 = await promise2;
  return value1+value2;
}
```

``` haskell
concatMonadic :: Monad m => m String -> m String -> m String
concatMonadic op1 op2 = do
  value1 <- op1
  value2 <- op2
  return (value1++value2)
```

## 13.14 Monad：总结

- `Monad` 类型类是一种表示*执行配方*的不同方式的方法
  - 失败（`Maybe`）
  - 日志记录
  - 状态
  - 不确定性（列表Monad）
  - IO
- 你可以用两种等效的方式编写 monad 代码：
  - 直接使用`Monad`类操作（`>>=`、`>>`）
  - 使用 `do` 表示法
- 当`M`是一个monad时，`M a`类型的值是*产生`a`类型结果的操作*
- Monad 是一个*设计模式*和一个*库*（`mapM` 等）
  - 使用通用抽象使代码更容易理解
  - 读取 `State` 操作比破译带有状态的复杂递归更容易
- 你可以用 monad 做的一切，你也可以不用它们做
  - 例外：IO
  - 使用 monad 通常可以简化代码
- *警告*：互联网上充斥着试图使用简单类比来解释 monad 的教程
  - 根据我的经验，这是行不通的
  - 有效的是使用不同的Monad并慢慢习惯这个概念

## 13.15 附注：标准 Haskell

本讲座和上一讲座涵盖了 Haskell 的 GHC 版本与标准 Haskell 2010 不同的许多部分。以下是 GHC 所做更改的简短列表，仅供你了解：

- `length`、`sum`、`foldr` 等普遍适用于 `Foldable` 而不仅仅是列表
- `Functor` 和 `Applicative` 是 `Monad` 的超类
- `fail` 方法已从 `Monad` 类型类移至其自己的 `MonadFail` 类

## 13.16 测验

与下面的 do 块等效的表达式是什么？

``` haskell
do y <- z
   s y
   return (f y)
```

1.`z >> \y -> s y >> return (f y)`
2.`z >>= \y -> s y >> return (f y)`
3.`z >> \y -> s y >>= return (f y)`

`\x xs -> return (x : xs)`是什么类型？

1.`Monad m => a -> [a] -> m [a]`
2.`Monad m => a -> [m a] -> [m a]`
3.`a -> [a] -> Monad [a]`
4.以上都不是

`\x xs -> return x : xs`是什么类型？

1.`Monad m => a -> [a] -> m [a]`
2.`Monad m => a -> [m a] -> [m a]`
3.`a -> [a] -> Monad [a]`
4.以上都不是

`(\x xs -> return x) : xs`是什么类型？

1.`Monad m => a -> [a] -> m [a]`
2.`Monad m => a -> [m a] -> [m a]`
3.`a -> [a] -> Monad [a]`
4.以上都不是

## 13.17 练习

- [Set13a](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set13a.hs)
- [Set13b](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set13b.hs)

# 14 第 14 讲：来用一些库！

现在你了解了 monad，你几乎了解了 Haskell 的所有内容，可以开始编写使用库来做有用事情的实际程序。本讲座将介绍此类实际程序中常用的一些库的示例。使用这些库也是练习使用 monad、阅读文档和理解类型错误的好方法。

**注意！** 在阅读库的文档时，请记住注意库的版本。你可以在[`tests.cabal` 文件](https://github.com/moocfi/haskell-mooc/blob/master/exercises/tests.cabal) 中查看课程中使用的版本。课程材料中的链接始终将你带到正确的版本，`stack haddock --open <package>` 命令也是如此。另请参阅[阅读第 1 部分中的文档](https://haskell.mooc.fi/part1#reading-docs)。

## 14.1 `Text` 和 `ByteString`

到目前为止，我们一直在使用 Haskell `String` 类型来处理字符串。然而，`String` 只是 `[Char]`，一个字符链表。无论是在记忆方面还是在时间方面，这都是极其低效的。一旦我们超越处理短字符串并开始处理整个文件或网络请求，就必须使用更省时的字符串类型。

有两种类型可用作 `String` 的替代品，语义略有不同：

- `Data.Text` 表示 *[Unicode 字符](https://en.wikipedia.org/wiki/Unicode)* 序列，就像 `String` 一样，只是效率更高。处理文本时使用。
- `Data.ByteString` 表示*字节序列*。在处理二进制数据时使用。

此外，这两种类型都有*惰性*和*严格*变体。 [`Data.Text` 的文档](https://hackage.haskell.org/package/text-1.2.5.0/docs/Data-Text.html) 很好地总结了差异：

> 严格 `Text` 类型要求整个字符串立即装入内存。惰性 `Text` 类型能够使用较小的内存占用来流式传输大于内存的字符串...每个模块都提供几乎相同的 API...

所有这些类型（`Text` 和 `ByteString`，严格和惰性）都提供 `pack` 和 `unpack` 函数，用于从普通 `String` 进行转换。这些类型还附带了熟悉的列表函数的专门版本，如 `reverse`、`take`、`map` 等。

### 14.1.1 `Text` 示例

让我们通过一个简短的 GHCi 会话来演示 `Data.Text` 的使用。正如[文档](https://hackage.haskell.org/package/text-1.2.5.0/docs/Data-Text.html)所述，`Data.Text` 模块设计为导入*合格*。我们可以使用函数 `T.pack` 将 `String` 转换为 `Text`。请注意 `Text` 类型的值如何像 `String` 一样打印。

``` haskell
Prelude> import qualified Data.Text as T
Prelude T> :t T.pack
T.pack :: String -> T.Text
Prelude T> phrase = T.pack "brevity is the soul of wit"
Prelude T> :t phrase
phrase :: T.Text
Prelude T> phrase
"brevity is the soul of wit"
```

我们可以使用 `Data.Text` 中的函数来对 `Text` 的值进行操作。其中许多的命名类似于 `String` 的对应项或 `Prelude` 的列表。

``` haskell
Prelude T> :t T.length
T.length :: T.Text -> Int
Prelude T> T.length phrase
26
Prelude T> T.head phrase
'b'
Prelude T> T.take 4 phrase
"brev"
Prelude T> :t T.words
T.words :: T.Text -> [T.Text]
Prelude T> T.words phrase
["brevity","is","the","soul","of","wit"]
Prelude T> :t T.map
T.map :: (Char -> Char) -> T.Text -> T.Text
Prelude T> T.map (\c -> if c=='o' then '0' else c) phrase
"brevity is the s0ul 0f wit"
```

一个有用的细节是 `Text` 有一个 `Monoid` 实例，它将 `Text` 值粘合在一起。你还可以使用函数 `T.append` 和 `T.concat`。

``` haskell
Prelude T> phrase <> phrase
"brevity is the soul of witbrevity is the soul of wit"
Prelude T> T.append phrase phrase
"brevity is the soul of witbrevity is the soul of wit"
Prelude T> T.concat [phrase,phrase,phrase]
"brevity is the soul of witbrevity is the soul of witbrevity is the soul of wit"
```

如果你想编写一个在 `Text` 上进行模式匹配的递归函数，就像在 `String` 上一样，你可以使用函数 `T.uncons :: T.Text -> Maybe (Char, T.Text)` 将 `Text` 拆分为头部和尾部。这是一个简单的例子：

``` haskell
countLetter :: Char -> T.Text -> Int
countLetter c t =
  case T.uncons t of
    Nothing -> 0
    Just (x,rest) -> (if x == c then 1 else 0) + countLetter c rest
```

``` haskell
Prelude T> countLetter 't' phrase
3
```

#### 14.1.1.1 严格与惰性

请注意，`Data.Text` 实现严格 `Text` 类型。你需要使用 `Data.Text.Lazy` 作为惰性版本。如前所述，这两种类型之间的一个区别是严格类型不适用于无限字符串：

``` haskell
Prelude T> T.head (T.pack (repeat 'x'))
-- never returns
Prelude T> import qualified Data.Text.Lazy as TL
Prelude T TL> TL.head (TL.pack (repeat 'x'))
'x'
```

另一个实际问题是，在使用库时，你可能会遇到严格 `Text` 和惰性 `Text` 之间的不匹配。你通常可以根据需要使用 `toStrict` 或 `fromStrict` 来修复此问题。

``` haskell
Prelude T TL> lazyPhrase = TL.pack "brevity is the soul of wit"
Prelude T TL> :t lazyPhrase
lazyPhrase :: TL.Text
Prelude T TL> :t phrase
phrase :: T.Text
Prelude T TL> lazyPhrase == phrase

<interactive>: error:
    • Couldn't match expected type ‘TL.Text’
                  with actual type ‘T.Text’
      NB: ‘T.Text’ is defined in ‘Data.Text.Internal’
          ‘TL.Text’ is defined in ‘Data.Text.Internal.Lazy’
    • In the second argument of ‘(==)’, namely ‘phrase’
      In the expression: lazyPhrase == phrase
      In an equation for ‘it’: it = lazyPhrase == phrase

Prelude T TL> :t TL.toStrict
TL.toStrict :: TL.Text -> T.Text
Prelude T TL> :t TL.fromStrict
TL.fromStrict :: T.Text -> TL.Text
Prelude T TL> TL.toStrict lazyPhrase == phrase
True
```

### 14.1.2 `ByteString` 示例

我们可以使用 `ByteString` 而不是 `Text` 来完成几乎相同的 GHCi 会话。但是，请注意 `ByteString` 是如何从 `Word8` 值而不是 `Char` 值构建的。 `Char` 可以表示任意 unicode 代码点，就像 `'Å'` 这样的字符一样，但 `Word8` 表示一个字节：从 0 到 255 的数字。不幸的是，有点令人困惑，`ByteString` 值的打印方式与 `String` 类似。

``` haskell
Prelude> import Data.Word
Prelude Data.Word> import qualified Data.ByteString as B
Prelude Data.Word B> binary = B.pack [99,111,102,102,101,101]
Prelude Data.Word B> :t binary
binary :: B.ByteString
Prelude Data.Word B> :t B.pack
B.pack :: [Word8] -> B.ByteString
Prelude Data.Word B> binary
"coffee"
Prelude Data.Word B> :t B.length
B.length :: B.ByteString -> Int
Prelude Data.Word B> B.length binary
6
Prelude Data.Word B> :t B.head
B.head :: B.ByteString -> Word8
Prelude Data.Word B> B.head binary
99
Prelude Data.Word B> B.take 4 binary
"coff"
Prelude Data.Word B> :t B.map
B.map :: (Word8 -> Word8) -> B.ByteString -> B.ByteString
Prelude Data.Word B> B.map (+1) binary
"dpggff"
```

与 `Text` 相同的警告适用于严格 `ByteString` 和惰性 `ByteString` 之间的差异：

``` haskell
Prelude B Data.Char> B.head (B.pack (repeat 99))
-- never returns
Prelude Data.Word B> import qualified Data.ByteString.Lazy as BL
Prelude Data.Word B BL> BL.head (BL.pack (repeat 99))
99
Prelude Data.Word B BL> binary == BL.pack [99]

<interactive>: error:
    • Couldn't match expected type ‘B.ByteString’
                  with actual type ‘BL.ByteString’
      NB: ‘BL.ByteString’ is defined in ‘Data.ByteString.Lazy.Internal’
          ‘B.ByteString’ is defined in ‘Data.ByteString.Internal’
    • In the second argument of ‘(==)’, namely ‘BL.pack [99]’
      In the expression: binary == BL.pack [99]
      In an equation for ‘it’: it = binary == BL.pack [99]

Prelude Data.Word B BL> :t BL.toStrict
BL.toStrict :: BL.ByteString -> B.ByteString
Prelude Data.Word B BL> :t BL.fromStrict
BL.fromStrict :: B.ByteString -> BL.ByteString
Prelude Data.Word B BL> binary == BL.toStrict (BL.pack [99])
False
```

### 14.1.3 附注：编码

你可能想知道为什么我们同时拥有 Text 和 ByteString。差异很微妙但又真实。当我们对 `Text` 进行操作时，我们会逐个字符地进行操作，而不管这些字符是什么以及它们是如何编码的。当我们对 `ByteString` 进行操作时，我们对字节进行操作，无论这些字节代表什么。

字符、数字和数据结构是帮助我们人类处理复杂编程任务的抽象。计算机内存本质上只是一个巨大的字节序列。机器并不关心我们如何解释这些字节。 `Text` 和 `ByteString` 之间的本质区别在于字节的分组和解释方式。

为了说明这种差异，我们将看看 [UTF-8 文本编码](https://en.wikipedia.org/wiki/UTF-8)。文本编码是将*字符*表示为*字节*的方法。 UTF-8可以表示[Unicode](https://en.wikipedia.org/wiki/Unicode)定义的所有数百万个字符。由于字节只能存储 0 到 255 之间的值，这意味着一个字符可以编码为多个字节。 UTF-8 字符串“Ha∫keλ!”的位和字节可以有多种解释：

![](img/string.svg)

（如果你在图片和“Ha∫keλ！”中看到不同的字符，则意味着你的浏览器错误地解释了编码，或者你使用的字体不支持所有字符。）

如果我们使用不同的编码读取相同的位流，我们会看到其他字符。例如，上面的字符串将被解释为“Haâˆ«keÎ»!”使用[Latin-1 文本编码](https://en.wikipedia.org/wiki/Latin-1)。

顺便说一句，在处理原始二进制数据时，使用十六进制数字系统通常很方便，它使用单个符号 `0`、`1`、...、`9`、`A`、`B`、...、`F` 来表示四位的所有 16 种可能的组合。在本课程中我们不需要十六进制，但如果你有兴趣了解有关十六进制的更多信息，你可以查看[维基百科](https://en.wikipedia.org/wiki/Hexadecimal)。

我们可以使用代码探索相同的示例。函数 `Data.Text.Encoding.encodeUtf8 :: Text -> ByteString` 使用 UTF-8 将 Text 中的字符编码为 ByteString 中的字节。

``` haskell
Prelude> import qualified Data.Text as T
Prelude T> import qualified Data.ByteString as B
Prelude T B> T.length (T.pack "haskell")
7
Prelude T B> T.length (T.pack "Ha∫keλ!")
7
Prelude T B> import Data.Text.Encoding
Prelude T B Data.Text.Encoding> encodeUtf8 (T.pack "haskell")
"haskell"
Prelude T B Data.Text.Encoding> encodeUtf8 (T.pack "Ha∫keλ!")
"Ha\226\136\171ke\206\187!"
Prelude T B Data.Text.Encoding> B.length (encodeUtf8 (T.pack "haskell"))
7
Prelude T B Data.Text.Encoding> B.length (encodeUtf8 (T.pack "Ha∫keλ!"))
10
```

如果我们处理的是[ASCII文本](https://en.wikipedia.org/wiki/ASCII)，即可以用单字节表示的字符，我们可以互换使用`Text`和`ByteString`。命名空间 `Data.ByteString.Char8` 和 `Data.ByteString.Lazy.Char8` 提供使用 `Char` 值（而不是 `Word8`）在 `ByteString` 上运行的函数。然而，必须小心确保所有字符确实都是纯 ASCII 字符，否则会发生令人惊讶的事情。

``` haskell
Prelude T B> import qualified Data.ByteString.Char8 as B8
Prelude T B B8> B8.pack "abc"
"abc"
Prelude T B B8> :t B8.pack
B8.pack :: String -> B.ByteString
Prelude T B B8> :t B.pack
B.pack :: [Word8] -> B.ByteString
Prelude T B B8> B8.cons 'a' (B8.pack "bc")
"abc"
Prelude T B B8> putStrLn (B8.unpack (B8.pack "€λ훈"))  -- non-ASCII characters get truncated
¬»È
Prelude T B B8> putStrLn (T.unpack (T.pack "€λ훈"))
€λ훈
```

## 14.2 Monad：回顾

接下来我们将研究 IO monad 内部的库。这是我们在上一课中学到的关于 monad 的简短回顾。

- 当 `M` 是 monad 时，`M X` 类型的值是可以“执行”以“生成”`X` 类型的值的“操作”。
- Monadic 操作可以使用以下方式实现
  - `Monad` 类型类的方法（`return`、`>>=`、`>>`），
  - `do`-符号，
  - 以及 `mapM` 等库函数。
- 与其他语言不同，`return` 不是关键字，不会导致操作停止执行。相反，`return x` *是始终生成 x 且不执行任何其他操作的操作*。
- 这就是 `do` 表示法的样子：

``` haskell
foo y = do
  operation1         -- run an operation
  val <- operation2  -- run an operation and keep the produced value
  operation3 val y   -- run an operation with parameters
  mapM_ (\x -> operation4 val x) things  -- use a generic monad operation and a lambda
  operation5 val     -- the final line of the do decides which value the whole block produces
```

## 14.3 编写 HTTP 服务器：WAI 和 Warp

有时感觉世界上的一切都发生在 [HTTP](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol) 和 [Web Apis](https://en.wikipedia.org/wiki/Web_API) 上。你的网络浏览器、你的智能手机Applicative、[你的银行](https://developer.nordeaopenbanking.com/)、[你的咖啡壶](https://tools.ietf.org/html/rfc2324)、[甚至你的门铃](https://support.ring.com/hc/en-us/articles/205385394-The-Protocols-and-Ports-Used-by-Ring-Devices)，都使用 HTTP 协议与服务器通信。

让我们看看如何在 Haskell 中设置一个简单的 HTTP 服务器。为此的标准低级组件称为 [WAI](https://hackage.haskell.org/package/wai-3.2.3/docs/Network-Wai.html) 和 [Warp](https://hackage.haskell.org/package/warp-3.3.23)。 WAI（Web 应用接口）为我们提供了一种定义如何处理 HTTP 请求的方法。 Warp 是一个简单的 HTTP 服务器，它运行我们使用 WAI 定义的逻辑。现在听起来可能有点抽象，但一个简单的例子会有所帮助。

文件 [`exercises/Examples/HelloServer.hs`](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Examples/HelloServer.hs) 实现了一个始终以“Hello World!”响应的 HTTP 服务器。你可以通过转到 `exercises/Examples` 目录并使用 `stack runhaskell HelloServer.hs` 运行来尝试一下。之后你可以在浏览器中访问<http://localhost:3421>来查看服务器的响应。

``` haskell
module Examples.HelloServer where

import qualified Data.ByteString.Lazy.Char8 as BL
import Network.HTTP.Types.Status (status200)
import Network.Wai (Application, responseLBS)
import Network.Wai.Handler.Warp (run)

port :: Int
port = 3421

main :: IO ()
main = run port application

-- type Application = Request -> (Response -> IO ResponseReceived) -> IO ResponseReceived
application :: Application
application request respond =
  respond (responseLBS status200 [] (BL.pack "Hello World!"))
```

让我们看看这个例子中的类型。这里发生了很多事情。首先，`Application` 是实现 Web 服务器逻辑的事物的“类型别名”。 [Warp 中的 `run` 函数](https://hackage.haskell.org/package/warp-3.3.23/docs/Network-Wai-Handler-Warp.html#v:run) 可以运行 `Application`：

``` haskell
run :: Port -> Application -> IO ()
type Application = Request -> (Response -> IO ResponseReceived) -> IO ResponseReceived
```

我们很快就会讨论 `Request` 和 `Response` 是什么，但从这个类型中我们可以看到 `Application` 是一个 IO 操作，它以 `Request` 类型的请求和一个 IO 操作 `respond :: Response -> IO ResponseReceived` 作为参数。像 `respond` 这样的参数在许多上下文中被称为“回调”。它们允许我们回拨调用该Applicative的库。 `Application` 操作必须生成与 `respond` 相同的特殊 `ResponseReceived` 类型。你可以将此类型视为证明 `respond` 被 `Application` 调用的令牌。

这可能听起来很吓人：但看看代码，事情相对简单：我们的 `server` 是一个 `Application` 并采用两个参数：`request` 和 `respond`。

WAI 使用许多类型（例如 `Port`、`Request`、`Response`、`Status`）来表示 HTTP 概念。当你遇到它们时，在文档中查找它们会很有用。例如 [`Port` 只是 `Int`](https://hackage.haskell.org/package/warp-3.3.23/docs/Network-Wai-Handler-Warp.html#t:Port) 的别名。作为另一个例子，我们可以看到 [`responseLBS` 函数](https://hackage.haskell.org/package/wai-3.2.3/docs/Network-Wai.html#v:responseLBS) 的类型

``` haskell
responseLBS :: Status -> ResponseHeaders -> ByteString -> Response
```

其中 [`Status` 在 `Network.HTTP.Types.Status`](https://hackage.haskell.org/package/http-types-0.12.3/docs/Network-HTTP-Types-Status.html#t:Status) 中定义，[`ResponseHeaders` 是来自 `Network.HTTP.Types.Header`](https://hackage.haskell.org/package/http-types-0.12.3/docs/Network-HTTP-Types-Header.html#t:ResponseHeaders) 的 `[Header]` 的类型别名，`ByteString` 是惰性 `ByteString`，结果类型 [`Response` 定义为`Network.WAI`](https://hackage.haskell.org/package/wai-3.2.3/docs/Network-Wai.html#t:Response)。

最后，请注意，我们采用了快捷方式，使用函数 `Data.ByteString.Lazy.Char8.pack` 将 `String` 转换为 `ByteString`。这仅适用于 ASCII 文本。

总是以相同文本响应的Web 服务器并不是那么有趣。接下来我们看看如何针对不同的请求给出不同的响应。 HTTP 请求中有很多部分，但在本次讲座中，我们将重点关注*路径*。在 `http://example.com/abcd/ef/file` 这样的 URL 中，`/abcd/ef/file` 部分是路径。 WAI有函数

``` haskell
pathInfo :: Request -> [Text]
```

这为我们提供了所请求 URL 的路径，以 `/` 字符分割。

文件 [`exercises/Examples/PathServer.hs`](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Examples/PathServer.hs) 实现了一个具有三个不同页面的 Web 服务器：

- <http://localhost:3421/source> 是Applicative本身的源，从文件系统读取
- <http://localhost:3421/secret/file> 是一个秘密字符串
- <http://localhost:3421/anything/else> - 对于所有其他路径，显示“未找到：任何内容/其他”文本

和以前一样，你可以通过进入 `exercises/Examples` 目录并运行 `stack runhaskell PathServer.hs` 来运行服务器。

## 14.4 使用数据库：sqlite-simple

实现 HTTP 服务器后，我们可以参与Applicative相互通信的全局图，即互联网。但如果我们不记得了，说话还有什么用呢？真正的Applicative即使在重新启动时也需要能够“保留数据”。实现此目的的常见方法是使用数据库。

数据库有很多种，但可以说使用最广泛的简单数据库是[SQLite](https://www.sqlite.org/index.html)。 SQLite 是一个库，可让你将数据存储在文件中并使用 [SQL，结构化查询语言]() 对其进行处理。使用 SQLite，无需像 [PostgreSQL](https://www.postgresql.org/) 或 [MySQL](https://www.mysql.com/) 那样运行单独的数据库服务器。

如果你不熟悉 SQL，请不要担心，你不需要在练习中编写自己的任何查询。如果你现在想学习一些 SQL，网上有很多教程。请参阅 [W3Schools](https://www.w3schools.com/sql/)、[SQL Zoo](https://sqlzoo.net/) 或 [Codecademy](https://www.codecademy.com/learn/learn-sql)。

Haskell 有许多用于使用 SQLite 的库，但我们在这里看一个名为 [sqlite-simple](https://hackage.haskell.org/package/sqlite-simple-0.4.18.2) 的库。让我们稍微探索一下 GHCi 中的库。

所有函数都位于 `Database.SQLite.Simple` 内部。你可以通过为 `open` 指定文件名来打开数据库，这是一个生成 `Connection` 的 IO 操作。

``` haskell
Prelude> import Database.SQLite.Simple
Prelude Database.SQLite.Simple> :t open
open :: String -> IO Connection
Prelude Database.SQLite.Simple> db <- open "example.sqlite"
```

要运行 SQL 查询，你可以使用 IO 操作 `query_`，该操作采用 `Connection` 和 `Query`，并生成结果列表。 `Query`类型只是围绕`Text`的简单`newtype`。 `query_`的结果类型是多态的：可以从数据库中读取任何满足`FromRow`类型类的类型。如果这让人感到困惑，请将其与 `read` 的类型进行比较：`Read a => String -> a`。 `FromRow` 类类似于此数据库的 `Read`：它表示可以从数据库中读取的类型。无论如何，让我们从数据库中读取数字 `1`：

``` haskell
Prelude Database.SQLite.Simple> :t query_
query_ :: FromRow r => Connection -> Query -> IO [r]
Prelude Database.SQLite.Simple> :info Query
newtype Query = Query {fromQuery :: Data.Text.Internal.Text}
    -- Defined in ‘Database.SQLite.Simple.Types’
    -- ... rest of output omitted
Prelude Database.SQLite.Simple> import qualified Data.Text as T
Prelude Database.SQLite.Simple T> q = Query (T.pack "SELECT 1;")
Prelude Database.SQLite.Simple T> res <- query_ db q :: IO [[Int]]
Prelude Database.SQLite.Simple T> res
[[1]]
```

顺便说一句，所有这些初始示例都使用仅返回常量数据的简单 `SELECT x, y, z;` 查询。稍后我们将担心数据库中的实际表。

如果没有类型签名，我们会从 GHCi 收到错误，它无法决定我们要从数据库中读取哪种类型：

``` haskell
Prelude Database.SQLite.Simple T> res <- query_ db q

<interactive>:17:8: error:
    • Ambiguous type variable ‘r0’ arising from a use of ‘query_’
      prevents the constraint ‘(FromRow r0)’ from being solved.
      Probable fix: use a type annotation to specify what ‘r0’ should be.
      -- rest of error omitted
```

在继续之前，让我们仔细看看 `FromRow`。如果你以前接触过 SQL，你就会知道 SQL 查询返回许多*行*，每行由许多*值*（也称为*列*）组成。为了能够将 SQL 查询的结果解释为 Haskell 数据，我们需要一种方法来解释这些值和行。因此 sqlite-simple 定义了两个类，`FromField` 和 `FromRow`，以及一堆如下所示的实例。 （你可以从[文档](https://hackage.haskell.org/package/sqlite-simple-0.4.18.2/docs/Database-SQLite-Simple.html#t:FromRow) 或通过使用 `:info FromRow` 等询问 GHCi 来找到这些实例。）

``` haskell
instance FromField Int
instance FromField Bool
instance FromField String
instance FromField Text
instance FromField a => FromRow [a]
instance (FromField a, FromField b) => FromRow (a,b)
instance (FromField a, FromField b, FromField c) => FromRow (a,b,c)
```

本质上，基本的 Haskell 数据类型满足 `FromField` 类，各种 Haskell 集合满足 `FromRow` 类。我们之前的示例是使用 `FromRow [a]` 和 `FromField Int` 实例从 `query_` 中获取 `[[Int]]`。这是一个使用其他一些数据类型的简单查询：

``` haskell
Prelude Database.SQLite.Simple T> q = Query (T.pack "SELECT 1, true, 'string';")
Prelude Database.SQLite.Simple T> query_ db q :: IO [(Int,Bool,String)]
[(1,True,"string")]
```

如果 SQL 和 Haskell 类型不匹配会发生什么？好吧，你会遇到运行时错误，就像你尝试调用 `read "True" :: Int` 一样。

``` haskell
Prelude Database.SQLite.Simple T> query_ db q :: IO [(Int,Int,Int)]
*** Exception: ConversionFailed {errSQLType = "TEXT", errHaskellType = "Int", errMessage = "need an int"}
```

为了镜像 `FromRow` 和 `FromField` 类，sqlite-simple 还定义了 `ToRow` 和 `ToField` 类用于写入数据库。这是 `query` 函数的类型，它允许我们使用*参数化查询*。

``` haskell
query :: (ToRow q, FromRow r) => Connection -> Query -> q -> IO [r]
```

以下是 `ToRow` 和 `ToField` 的一些实例：

``` haskell
instance ToField Int
instance ToField Bool
instance ToField String
instance ToField Text
instance ToField Int

instance ToField a => ToRow [a]
instance (ToField a, ToField b) => ToRow (a, b)
instance (ToField a, ToField b, ToField c) => ToRow (a, b, c)
instance ToField a => ToRow (Only a)
```

参数化查询使用 `?` 字符来表示可以传入参数的槽。下面是一个简单的示例：

``` haskell
Prelude Database.SQLite.Simple T> input = (1,"hello") :: (Int,String)
Prelude Database.SQLite.Simple T> parameterized = Query (T.pack "SELECT ?+1, true, ?;")
Prelude Database.SQLite.Simple T> query db parameterized input :: IO [(Int,Bool,String)]
[(2,True,"hello")]
```

**注意！** 当仅使用一个参数执行查询时，你可以使用两个 `ToRow` 实例：`ToField a => ToRow [a]` 和 `ToField a => Only a`。 `Only` 数据类型在 `Data.Tuple.Only` 中定义，是 Haskell 没有单元素元组这一事实的一种解决方法。或者，大小为 1 的列表也可以。这同样适用于仅返回只有一列的行的查询：你可以使用 `[[X]]` 或 `[Only X]` 作为返回类型。这是一个例子：

``` haskell
Prelude Database.SQLite.Simple T> q = Query (T.pack "SELECT lower(?);")
Prelude Database.SQLite.Simple T> query db q (Only "HELLO") :: IO [Only String]
[Only {fromOnly = "hello"}]
Prelude Database.SQLite.Simple T> query db q ["HELLO"] :: IO [[String]]
[["hello"]]
```

这几乎就是你需要了解的有关 sqlite-simple 的全部信息：`open`、`query_`、`query`、`FromRow`、`ToRow`。哦，对了，还有一件事。如果不需要查询结果，可以使用`execute`和`execute_`函数。例如，它们对于将内容插入数据库很有用。

``` haskell
execute_ :: Connection -> Query -> IO ()
execute :: ToRow q => Connection -> Query -> q -> IO ()
```

你将在 [`exercises/Examples/Phonebook.hs`](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Examples/Phonebook.hs) 下找到一个使用 sqlite-simple 维护电话簿的示例程序。该程序将电话簿保存在名为 `phonebook.db` 的文件中，其工作方式如下（从课程仓库中的 `exercises/Examples` 目录运行）：

    $ stack runhaskell Phonebook.hs
    (a)dd or (q)uery?
    a
    Name?
    bob
    Phone?
    1234
    $ stack runhaskell Phonebook.hs
    (a)dd or (q)uery?
    a
    Name?
    bob
    Phone?
    5678
    $ stack runhaskell Phonebook.hs
    (a)dd or (q)uery?
    a
    Name?
    samantha
    Phone?
    1357
    $ stack runhaskell Phonebook.hs
    (a)dd or (q)uery?
    q
    Name?
    bob
    2 numbers:
    ["1234"]
    ["5678"]

P.S. 如果你因缺乏 SQL 查询的编译时类型检查而感到沮丧，你可以看看 Haskell 的一些更高级的 SQL 库，例如 [Beam](https://haskell-beam.github.io/beam/) 或 [Opaleye](https://hackage.haskell.org/package/opaleye)。本课程使用 sqlite-simple 是为了简单起见，并避免过多地关注 SQL 的细节。

## 14.5 练习

- [Set14a](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set14a.hs): Text 和 ByteString
- [Set14b](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set14b.hs): HTTP 和 SQLite

# 15 第 15 讲：没有 Monad 也能验证

## 15.1 Applicative 简介

`Applicative` 类型类是 `Functor`（你不能用它做那么多事情）和 `Monad`（它几乎允许你编写任意程序）之间的中间立场。使用 `Applicative` 而不是 `Monad` 的原因包括：

- 性能：由于`Applicative`允许的操作较少，因此可以比`Monad`更好地优化。
- 简单性：`Applicative` 接口更容易推理。
- 必要性：无法为你的类型定义 `Monad` 实例，但有一个 `Applicative` 实例。这是很少见的。

那么什么是`Applicative`？我们来看一个定义。

``` haskell
class Functor f => Applicative f where
  pure :: a -> f a
  liftA2 :: (a -> b -> c) -> f a -> f b -> f c
  -- other operations omitted for now
```

因此，`Applicative` 是 `Functor`，它允许我们通过 `pure` 构建单例值，并使用 `liftA2` 将两个值合并为一个值。与裸 Functor 相比，这增加了很多函数。使用 Functor 的计算必然是线性的：`fmap :: (a -> b) -> f a -> f b` 接受一个 Functor 值，并输出另一个 Functor 值。相比之下，`pure` 不接收任何 Functor 值并输出 1 个，而 `liftA2` 接收 2 个并返回 1 个。

附注：Applicative 这个术语来自术语 [Applicative Functor](https://en.wikipedia.org/wiki/Applicative_functor)，听起来像是来自范畴论，但实际上是在一篇编程论文中引入的。

现在抽象的胡言乱语已经够多了。让我们看看我们可以使用Applicative 运算（和 `fmap`）来表达什么样的计算。我们将从 `Maybe` Applicative 开始。这是一个简化的定义：

``` haskell
instance Applicative Maybe where
  pure x = Just x
  liftA2 f (Just x) (Just y) = Just (f x y)
  liftA2 f _        _        = Nothing
```

你将看到该定义使用与 `Monad Maybe` 实例相同类型的故障传播。让我们在解析货币值时使用它：

``` haskell
data Currency = EUR | USD
  deriving (Show, Eq)
data Money = Money Int Currency
  deriving (Show, Eq)

parseCurrency :: String -> Maybe Currency
parseCurrency "e" = pure EUR
parseCurrency "€" = pure EUR
parseCurrency "$" = pure USD
parseCurrency _ = Nothing

parseAmount :: String -> Maybe Int
parseAmount = readMaybe

parseMoney :: String -> String -> Maybe Money
parseMoney amountString currencyString =
  liftA2 Money (parseAmount amountString) (parseCurrency currencyString)
```

``` haskell
parseMoney "123" "€"  ==> Just (Money 123 EUR)
parseMoney "45" "$"   ==> Just (Money 45 USD)
parseMoney "4x" "€"   ==> Nothing
parseMoney "45" "£"   ==> Nothing
```

效果很好。然而，如果我们尝试对此进行扩展，我们很快就会遇到 `Applicative` 的限制。例如，考虑这个 `sumMoney` 函数，该函数对 `Money` 值求和，但如果它们不是相同的货币，则会失败：

``` haskell
sumMoney :: Money -> Money -> Maybe Money
sumMoney (Money a c) (Money b c')
    | c == c'   = Just (Money (a+b) c)
    | otherwise = Nothing
```

我们无法使用 `Applicative` 操作将其应用于两个 `Maybe Money` 值。为此，我们需要 `Maybe` monad：

``` haskell
example :: Maybe Money
example = do x <- parseMoney "123" "€"
             y <- parseMoney "45" "$"
             sumMoney x y
```

如果我们尝试使用 `liftA2`，我们就会陷入 `Maybe (Maybe Money)` 类型。此外，我们现在可以获得两种不同类型的故障：`Nothing` 和 `Just Nothing`，具体取决于错误发生的级别。这是切换到 `Monad` 实例的明显情况。

    liftA2 sumMoney (parseMoney "123" "e") (parseMoney "45" "€")
      ==> Just (Just (Money 168 EUR))
    liftA2 sumMoney (parseMoney "123" "e") (parseMoney "45" "$")
      ==> Just Nothing
    liftA2 sumMoney (parseMoney "123" "e") (parseMoney "xxx" "e")
      ==> Nothing

附注：`liftA2` 这个名字听起来有点麻烦，但它与 monad 的 `liftM`、`liftM2` 等函数进行类比。回想一下，`liftM` 只是 `fmap`，所以也许 `liftA2` 应该被称为 `fmap2`。

## 15.2 列表 Applicative

让我们看看我们见过的另一个`Functor`的应用实例。列表Functor的 `Applicative` 实例会遍历所有可能的值组合（就像列表 monad 一样）。这是例子：

``` haskell
instance Applicative [] where
  pure x = [x]
  liftA2 f xs ys = [f x y | x <- xs, y <- ys]
```

这是一个例子：生成一些短语。

``` haskell
things :: [String]
things = ["tangerine","bandit","diamond"]

fruits :: [String]
fruits = ["apple", "tangerine"]

phrases :: [String]
phrases = liftA2 combine things fruits
  where combine t f = "a " ++ t ++ " the size of a " ++ f

bunches = liftA2 copy [1,2,3] fruits
  where copy n f = unwords (replicate n f)
```

``` haskell
phrases ==> ["a tangerine the size of a apple",
             "a tangerine the size of a tangerine",
             "a bandit the size of a apple",
             "a bandit the size of a tangerine",
             "a diamond the size of a apple",
             "a diamond the size of a tangerine"]

bunches ==> ["apple","tangerine",
             "apple apple","tangerine tangerine",
             "apple apple apple","tangerine tangerine tangerine"]
```

## 15.3 新运算符

有一些非常方便的Applicative 运算符。它们是 `<$>`、`<*>`、`<*` 和 `*>`。

让我们从 `<$>` 开始，它只是 `fmap` 的中缀版本：

``` haskell
(<$>) :: Functor f => (a -> b) -> f a -> f b
f <$> x = fmap f x
```

``` haskell
not <$> Just True   ==> Just False
not <$> Nothing     ==> Nothing
negate <$> [1,2,3]  ==> [-1,-2,-3]
```

这本身就很好，但是当与这个 Applicative 运算符结合使用时，它真的会大放异彩：

``` haskell
(<*>) :: Applicative f => f (a -> b) -> f a -> f b
```

该类型告诉你 `<*>` 的作用：它的函数Applicative“提升”为Applicative。以下是一些独立的示例：

``` haskell
Just not <*> Just True    ==> Just False
Nothing  <*> Just True    ==> Nothing
Just not <*> Nothing      ==> Nothing
[(+1),(*2)] <*> [10,100]  ==> [11,101,20,200]
```

当我们将 `<$>` 和 `<*>` 结合起来时，真正的魔力发生了：然后我们可以将任意多个参数的函数提升为 Applicative！

``` haskell
say :: String -> Int -> String -> String
say x i y = x ++ " has " ++ show i ++ " " ++ y
```

``` haskell
say <$> Just "haskell" <*> Just 99 <*> Just "operators"
  ==> Just "haskell has 99 operators"
say <$> Nothing <*> Just 99 <*> Just "operators"
  ==> Nothing
say <$> ["bob","jake"] <*> [2,3] <*> ["bananas","cars"]
  ==> ["bob has 2 bananas",
       "bob has 2 cars",
       "bob has 3 bananas",
       "bob has 3 cars",
       "jake has 2 bananas",
       "jake has 2 cars",
       "jake has 3 bananas",
       "jake has 3 cars"]
```

这是怎么回事？让我们逐步进行评估。关键是每个 `<*>` 部分地向函数应用一个以上的参数。

``` haskell
    say <$> Just "haskell" <*> Just 99 <*> Just "operators"
=== ((say <$> Just "haskell") <*> Just 99) <*> Just "operators"
=== (fmap say (Just "haskell") <*> Just 99) <*> Just "operators"
==> (Just (say "haskell") <*> Just 99) <*> Just "operators"
==> Just (say "haskell" 99) <*> Just "operators"
==> Just (say "haskell" 99 "operators")
==> Just "haskell has 99 operators"
```

也许查看类型会更清楚：

``` haskell
say <$> Just "haskell"                                  :: Maybe (Int -> String -> String)
say <$> Just "haskell" <*> Just 99                      :: Maybe (       String -> String)
say <$> Just "haskell" <*> Just 99 <*> Just "operators" :: Maybe (                 String)
```

接下来的两个运算符稍微简单一些：

``` haskell
(*>) :: Applicative f => f a -> f b -> f b
x *> y = liftA2 (\a b -> b) x y

(<*) :: Applicative f => f a -> f b -> f a
x <* y = liftA2 (\a b -> a) x y
```

你可以将这些类型与更熟悉的运算符进行比较：

``` haskell
(>>) :: Monad m => m a -> m b -> m b
```

运算符 `<*` 和 `*>` 的含义是：运行这两个操作，但只保留一个结果。箭头指向保留的结果：

``` haskell
Just 1 *> Just 2  ==> Just 2
Just 1 <* Just 2  ==> Just 1
Just 1 <* Nothing ==> Nothing
Nothing <* Just 2 ==> Nothing
```

这些运算符可能看起来微不足道，但它们在组合检查时非常有用。例如：

``` haskell
decrease :: Int -> Maybe Int
decrease i = if i>0 then Just (i-1) else Nothing

small :: Int -> Maybe Int
small i = if i<10 then Just i else Nothing

decreaseSmall :: Int -> Maybe Int
-- do what decrease does, but fail if small fails
decreaseSmall i = decrease i <* small i
```

``` haskell
decreaseSmall 4   ==> Just 3
decreaseSmall 0   ==> Nothing
decreaseSmall 11  ==> Nothing
```

现在我们已经了解了所有这些运算符，我们可以理解 `Applicative` 的完整定义。所有运算符都有 `liftA2` 的定义，因此在实现 `Applicative` 实例时定义 `liftA2` 和 `pure` 就足够了。

``` haskell
class Functor f => Applicative f where
  pure :: a -> f a
  liftA2 :: (a -> b -> c) -> f a -> f b -> f c
  (<*>) :: f (a -> b) -> f a -> f b
  (*>) :: f a -> f b -> f b
  (<*) :: f a -> f b -> f a
```

## 15.4 Validation Applicative

让我们看一个比 Maybe 或列表更有趣的 Applicative。在编程中，我们通常需要“验证”用户的一些输入。在这些情况下，将输入可能存在的所有错误收集在一起很有用。文件 [`exercises/Examples/Validation.hs`](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Examples/Validation.hs) 实现 `Validation` 数据类型：

``` haskell
data Validation a = Ok a | Errors [String]
  deriving (Show,Eq)
```

`Validation` 的 `Applicative` 实例的工作方式如下：

``` haskell
liftA2 (+) (Ok 1) (Ok 2)
  ==> Ok 3
liftA2 (+) (Errors ["oh no"]) (Errors ["boom"])
  ==> Errors ["oh no","boom"]
```

请注意，与 `Maybe` Applicative相比，我们有许多不同类型的故障。

这是一个有效的示例，介绍了一些助手，然后使用它们来祝贺某人的生日：

``` haskell
invalid :: String -> Validation a
invalid err = Errors [err]

check :: Bool -> String -> a -> Validation a
check b err x
  | b = pure x
  | otherwise = invalid err

birthday :: String -> Int -> Validation String
birthday name age = liftA2 congratulate checkedName checkedAge
  where checkedName = check (length name < 10) "Name too long" name
        checkedAge = check (age < 99) "Too old" age
        congratulate n a = "Happy "++show a++"th birthday "++n++"!"
```

``` haskell
birthday "Guy" 31
  ==> Ok "Happy 31th birthday Guy!"
birthday "Guybrush Threepwood" 31
  ==> Errors ["Name too long"]
birthday "Yog-sothoth" 10000
  ==> Errors ["Name too long","Too old"]
```

哦，对了，这是 `Validation` 的 `Functor` 和 `Applicative` 实例：

``` haskell
instance Functor Validation where
  fmap f (Ok x) = Ok (f x)
  fmap _ (Errors e) = Errors e

instance Applicative Validation where
  pure x = Ok x
  liftA2 f (Ok x)      (Ok y)      = Ok (f x y)
  liftA2 f (Errors e1) (Ok y)      = Errors e1
  liftA2 f (Ok x)      (Errors e2) = Errors e2
  liftA2 f (Errors e1) (Errors e2) = Errors (e1++e2)
```

`Validation` 的 `liftA2` 的定义表明错误是从左到右收集在一起的。这可以在上面的示例中看到，其中表达式 `liftA2 congratulate checkedName checkedAge` 首先输出来自 `checkedName` 的错误 (`"Name too long"`)，最后输出来自 `checkedAge` 的错误 (`"Too old"`)。

## 15.5 验证列表：`traverse`

到目前为止，我们已经处理了固定大小的事物和Applicative：我们已经将两个或三个参数的函数应用于某些事物。如果我们有任意数量的输入怎么办？如果我们需要验证列表怎么办？

让我们看一下实现这样的函数的一些方法：

``` haskell
allPositive [1,2,3]
  ==> Ok [1,2,3]
allPositive [1,2,3,-4]
  ==> Errors ["Not positive: -4"]
allPositive [1,-2,3,-4]
  ==> Errors ["Not positive: -2","Not positive: -4"]
```

与往常一样，在使用列表时，模式匹配和递归通常是最佳选择。这是一个递归解决方案：

``` haskell
allPositive :: [Int] -> Validation [Int]
allPositive [] = Ok []
allPositive (x:xs) = liftA2 (:) checkThis checkRest
  where checkThis = check (x>=0) ("Not positive: "++show x) x
        checkRest = allPositive xs
```

总是拼写出这样的递归有点麻烦。如果我们在 `Monad` 中工作，我们可以使用像 `mapM` 这样的助手：

``` haskell
mapM (\x -> if x>=0 then Just x else Nothing) [1,2,3]
  ==> Just [1,2,3]
mapM (\x -> if x>=0 then Just x else Nothing) [1,2,3,-4]
  ==> Nothing
```

`Applicative` 的 `mapM` 等效项称为 `traverse`。它是类型类 `Traversable` 的成员：

``` haskell
traverse :: (Traversable t, Applicative f) => (a -> f b) -> t a -> f (t b)
```

这是一个很糟糕的类型签名，所以让我们稍微简化一下。列表是 `Traversable`，所以我们可以将这种类型专门化为：

``` haskell
traverse :: Applicative f => (a -> f b) -> [a] -> f [b]
```

这看起来正是我们所需要的！对于同样是 Monad 的 Applicatives，`traverse` 只是 `mapM` 的另一个名称：

``` haskell
traverse (\x -> if x>=0 then Just x else Nothing) [1,2,3]
  ==> Just [1,2,3]
traverse (\x -> if x>=0 then Just x else Nothing) [1,2,3,-4]
  ==> Nothing
```

但对于我们的 `Validation`（不是 `Monad`）来说，`traverse` 正是我们想要的：

``` haskell
allPositive :: [Int] -> Validation [Int]
allPositive xs = traverse checkNumber xs
  where checkNumber x = check (x>=0) ("Not positive: "++show x) x
```

``` haskell
allPositive [1,2,3]
  ==> Ok [1,2,3]
allPositive [1,2,3,-4]
  ==> Errors ["Not positive: -4"]
allPositive [1,-2,3,-4]
  ==> Errors ["Not positive: -2","Not positive: -4"]
```

请注意 `Validation` 的 `traverse` 如何按照原始列表中出现的顺序将所有错误收集在一起。

P.S. 事实上，`Validation` 是 `Applicative` 不可能是 `Monad` 的少数示例之一。你能弄清楚为什么吗？

## 15.6 附注：`Traversable`

那么`Traversable`是什么东西呢？很多熟悉的结构。以下是一些示例：

``` haskell
decrease :: Int -> Maybe Int
decrease i = if i>0 then Just (i-1) else Nothing
```

``` haskell
-- Lists are Traversable
traverse decrease [1,2,3] ==> Just [0,1,2]
traverse decrease [1,0,3] ==> Nothing

-- Arrays are Traversable
traverse decrease (array (1,3) [(1,10),(2,11),(3,12)])
         ==> Just (array (1,3) [(1,9),(2,10),(3,11)])

-- Maps are Traversable
traverse decrease (M.fromList [("a",1),("b",2)])
         ==> Just (M.fromList [("a",0),("b",1)])
traverse decrease (M.fromList [("a",1),("b",0)])
         ==> Nothing

-- Either is Traversable
traverse decrease (Left "abc") ==> Just (Left "abc")
traverse decrease (Right 3)    ==> Just (Right 2)
traverse decrease (Right 0)    ==> Nothing
```

所以 `Traversable` 是各种容器的类型类，有点像 `Foldable`。确实，如果你看一下定义，`Traversable` 是 `Foldable` 的子类。事实证明，`traverse` 和 `mapM` 是该类的方法！

``` haskell
class (Functor t, Foldable t) => Traversable t where
  traverse :: Applicative f => (a -> f b) -> t a -> f (t b)
  mapM :: Monad m => (a -> m b) -> t a -> m (t b)
```

在这里很难保持类型的正确性。我们回到`traverse`的类型：

``` haskell
traverse :: (Traversable t, Applicative f) => (a -> f b) -> t a -> f (t b)
```

这里我们有两个Functor：`t` 和 `f`。 `t` Functor也是 `Foldable` 和 `Traversable`，`f` Functor也是 `Applicative`。 `traverse` 函数允许我们在 `t` 容器内运行 `f` 操作。

如果这感觉很抽象，请不要担心。实际上，你几乎总是在列表上使用 `traverse`。

## 15.7 处理失败：`Alternative`

如果你稍微尝试一下Applicative，你就会开始注意到它们的函数有一些限制。例如，当像我们在 `parseMoney` 示例中那样编写解析器时，如果能够尝试几个不同的解析器并获取任何非失败结果，那就太好了。对于像 `Maybe` 这样的具体Applicative来说，这很容易编写，如下所示。

``` haskell
data Answer = Yes | No
  deriving (Show, Eq)

parseYes :: String -> Maybe Answer
parseYes "y" = Just Yes
parseYes "yes" = Just Yes
parseYes "maybe" = Just Yes
parseYes _ = Nothing

parseNo :: String -> Maybe Answer
parseNo "n" = Just No
parseNo "no" = Just No
parseNo "maybe" = Just No
parseNo _ = Nothing

eitherOf :: Maybe x -> Maybe x -> Maybe x
eitherOf (Just x) _  = Just x
eitherOf Nothing  mx = mx

parseAnswer :: String -> Maybe Answer
-- prefer positive answers!
parseAnswer s = eitherOf (parseYes s) (parseNo s)
```

``` haskell
parseAnswer "yes"    ==> Just Yes
parseAnswer "y"      ==> Just Yes
parseAnswer "n"      ==> Just No
parseAnswer "maybe"  ==> Just Yes
parseAnswer "x"      ==> Nothing
```

我们如何概括 `eitherOf`？我们不能给它类型 `Applicative f => f x -> f x -> f x`，因为这样实现就需要有效地类似于 `eitherOf a b = liftA2 something a b`，但是 `eitherOf Nothing (Just x)` 将是 `Nothing`（因为这就是 Applicative 实例的工作方式）！

事实证明我们需要一个新的类型类：`Alternative`。 Alternative 在 Applicative 中添加了两个操作：`empty` 表示没有结果，`<|>` 表示合并结果。

``` haskell
class Applicative f => Alternative f where
  empty :: f a
  (<|>) :: f a -> f a -> f a
  -- some other operations omitted
```

现在我们可以使用通用操作重写我们的解析代码：

``` haskell
data Answer = Yes | No
  deriving (Show, Eq)

parseYes :: Alternative f => String -> f Answer
parseYes "y" = pure Yes
parseYes "yes" = pure Yes
parseYes "maybe" = pure Yes
parseYes _ = empty

parseNo :: Alternative f => String -> f Answer
parseNo "n" = pure No
parseNo "no" = pure No
parseNo "maybe" = pure No
parseNo _ = empty

parseAnswer :: Alternative f => String -> f Answer
parseAnswer s = parseYes s <|> parseNo s
```

我们还可以选择在哪个 `Alternative` 中运行解析器以获得不同的行为。 `Maybe` 只给我们一个结果，而 `[]` 给我们所有可能的结果。

``` haskell
> parseAnswer "yes" :: Maybe Answer
Just Yes
> parseAnswer "maybe" :: Maybe Answer
Just Yes
> parseAnswer "yes" :: [Answer]
[Yes]
> parseAnswer "maybe" :: [Answer]
[Yes,No]
```

`[]` 和 `Maybe` 的 `Alternative` 实例并不令人意外：

``` haskell
instance Alternative [] where
  empty = []
  (<|>) = (++)

instance Alternative Maybe where
  empty = Nothing
  Just x  <|> _  = Just x
  Nothing <|> mx = mx
```

`Validation` 类型也是 `Alternative`。该实例将所有错误消息收集在一起，就像 `Applicative` 实例一样。

``` haskell
instance Alternative Validation where
  empty = Errors []
  Ok x <|> _ = Ok x
  Errors e1 <|> Ok y = Ok y
  Errors e1 <|> Errors e2 = Errors (e1++e2)
```

这是最后一个示例：验证联系信息，可以是电话数字或电子邮件地址。

``` haskell
data ContactInfo = Email String | Phone String
  deriving Show

validateEmail :: String -> Validation ContactInfo
validateEmail s = check (elem '@' s) "Not an email: should contain a @" (Email s)

checkLength :: String -> Validation ContactInfo
checkLength s = check (length s <= 10) "Not a phone number: should be at most 10 digits" (Phone s)

checkDigits :: String -> Validation ContactInfo
checkDigits s = check (all isDigit s) "Not a phone number: should be all numbers" (Phone s)

validatePhone :: String -> Validation ContactInfo
validatePhone s = checkDigits s *> checkLength s

validateContactInfo :: String -> Validation ContactInfo
validateContactInfo s = validateEmail s <|> validatePhone s
```

``` haskell
validateContactInfo "user@example.com"
  ==> Ok (Email "user@example.com")
validateContactInfo "01234"
  ==> Ok (Phone "01234")
validateContactInfo "01234567890"
  ==> Errors ["Not an email: should contain a @","Not a phone number: should be at most 10 digits"]
validateContactInfo "01234567890x"
  ==> Errors ["Not an email: should contain a @",
              "Not a phone number: should be all numbers",
              "Not a phone number: should be at most 10 digits"]
validateContactInfo "x"
  ==> Errors ["Not an email: should contain a @",
              "Not a phone number: should be all numbers"]
```

请注意，与前面的示例一样，错误是从左到右收集的：来自 `validateEmail` 的错误出现在来自 `validatePhone` 的错误之前。来自 `checkDigits` 的错误先于来自 `checkLength` 的错误。

## 15.8 附注：语境中的 Applicative

### 15.8.1 为什么是Applicative？

学习 Applicatives 的原因有多种，即使它们不提供任何比 Monad 更强大的函数。首先，正如第 13 讲中所讨论的，GHC 标准库现在强制所有 Monad 的应用实例。因此，一个工作中的 Haskell 程序员一定会看到很多 Applicative 实例。

其次，即使在 Monadic 代码中，你也会经常遇到Applicative 运算符。像 `f <$> x <*> y <*> z` 这样的表达式在许多 Monadic 上下文中都很有用。此外，由于 `Traversable` 类型类是根据 `Applicative` 构建的，因此你经常会对其使用应用操作。

第三，Applicative是理解函数设计模式的绝佳练习。它们将 Functor 模式与 Monoid 模式结合起来，而 Alternative 则带来了另一个类似 Monoid 的维度。能够有效地使用 Applicatives 将使使用 *monad 转换器* 或 *lenses* 等进一步的抽象变得更容易。

最后，有几种类型是 Applicatives 但不是 Monads。 `Validation` 就是一个例子，而且是一个非常实用的例子。如果不了解 Applicative，我们就无法识别和概括此类类型的操作。另一种这样的类型是[`ZipList`](https://hackage.haskell.org/package/base-4.16.4.0/docs/Control-Applicative.html#t:ZipList)。

### 15.8.2 野外应用

尽管我们在本次讲座中只介绍了一些非常简单且具体的 Applicatives，但仍有大量 Haskell 库使用 Applicatives 来完成重要任务。以下是一些示例。

与我们的 `Validation` Applicative相同的想法已在[验证](https://hackage.haskell.org/package/validation) 和[任一](https://hackage.haskell.org/package/either) 库中实现。

有多个使用 Applicatives 的解析器库。例如，[regex-applicative](https://hackage.haskell.org/package/regex-applicative)、[optparse-applicative](https://hackage.haskell.org/package/optparse-applicative)、[yamlparse-applicative](https://cs-syd.eu/posts/2020-06-28-yamlparse-applicative)、[json-stream](https://hackage.haskell.org/package/json-stream) 等。

### 15.8.3 Monad 和Applicative

那么 Monad 和 Applicative 之间有什么关系呢？如果Applicative也是 Monad，则以下定律成立：

``` haskell
pure             === return

fmap             === liftM

fmap f op        === do x <- op
                        return (f x)

liftA2           === liftM2

liftA2 f op1 op2 === do x <- op1
                        y <- op2
                        return (f x y)

op1 *> op2       === op1 >> op2

op1 <*> op2      === do f <- op1
                        x <- op2
                        return (f x)
```

在 `Monad` 中工作时，你可以将 `Applicative` 和 `Functor` 与 `Monad` 操作自由混合。作为一个例子，让我们重写 `mapM` 直到它只使用应用操作，从而得到 `traverse` 的实现。这是我们的出发点：

``` haskell
myMapM op [] = return []
myMapM op (x:xs) = do y <- op x
                      ys <- myMapM op xs
                      return (y:ys)
```

GHCi 告诉我们它只适用于 Monad：

``` haskell
Prelude> :t myMapM
myMapM :: Monad m => (a -> m b) -> [a] -> m [b]
```

让我们应用上面的 `pure === return` 和 `liftA2` 定律：

``` haskell
myMapM op [] = pure []
myMapM op (x:xs) = liftA2 (:) (op x) (myMapM op xs)
```

哒哒！现在 `myMapM` 适用于任何 `Applicative`：

``` haskell
Prelude> :t myMapM
myMapM :: Applicative f => (a -> f b) -> [a] -> f [b]
```

## 15.9 测验

`liftA2 (&&) Nothing x`中的`x`是什么类型？

1.`Applicative f => f Bool`
2.`Applicative Bool`
3.`Maybe Bool`

当`xs`和`ys`是列表时，`liftA2 f xs ys`有多少个元素？

1.`length xs + length ys`
2.`length xs * length ys`
3.`min (length xs) (length ys)`

以下哪个表达式相当于 `liftA2 f x y`？可能有多个正确答案。

1.`f <$> x <*> y`
2.`f <*> x <*> y`
3.`f <*> x <$> y`
4.`fmap f x <*> y`
5.`pure f <*> x <*> y`

如果 `f :: a -> Maybe b` 和 `xs :: [a]`，其中哪个表达式的类型与其他表达式不同？

1.`fmap f xs`
2.`traverse f xs`
3.`map f xs`

对于哪个 `Applicative`，表达式 `pure x <* pure y` 和 `pure x <|> pure y` 会产生不同的结果？

1.`Maybe`
2.`[]`
3.`Validation`

## 15.10 练习

- [Set15](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set15.hs)

# 16 第 16 讲：零碎内容

最后一讲将讨论一些其他地方不适合的小主题。你已经完成了课程的所有困难部分。现在是时候坐下来，放松一下，享受一些很酷的 Haskell 了！

## 16.1 使用 QuickCheck 测试

纯性的好处之一是纯函数易于测试：你不需要设置任何全局状态，你只需传入参数并检查结果是否正常即可。在本节中，我们将快速浏览*基于属性的测试*库 QuickCheck，该库也用于检查你对本课程的练习答案是否正确！

让我们看一下测试 `reverse` 的（错误）实现。你可以在文件 [`exercises/Examples/QuickCheck.hs`](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Examples/QuickCheck.hs) 中找到此示例和以下示例。

``` haskell
rev :: [a] -> [a]
rev [] = []
rev (x:xs) = xs ++ [x]
```

我们可以使用 QuickCheck 中的 `===` 运算符编写单独的测试用例：

``` haskell
(===) :: (Eq a, Show a) => a -> a -> Property
```

``` haskell
propRevSmall :: Property
propRevSmall = rev [1,2] === [2,1]
```

我们可以要求 QuickCheck 在 GHCi 中运行它们：

``` haskell
*Examples.QuickCheck> quickCheck propRevSmall
+++ OK, passed 1 test.
```

到目前为止，一切都很好。然而，这并不是 QuickCheck 的真正用途。 QuickCheck 专为“基于属性的测试”而设计，你可以在其中声明代码应具有的属性，QuickCheck 使用随机输入运行代码，每次都会检查该属性。 `reverse` 的正确实现具有什么简单属性？将列表反转两次肯定会返回相同的列表。我们把它写出来：

``` haskell
propRevTwice :: [Int] -> Property
propRevTwice xs = rev (rev xs) === xs
```

我们的 `Property` 有一个参数，这意味着 QuickCheck 将生成随机值并运行测试。我们可以使用 `verboseCheck` 函数来查看运行了哪些值。如果我们想检查特定值，我们也可以自己给测试提供一个参数。

``` haskell
*Examples.QuickCheck> quickCheck propRevTwice
+++ OK, passed 100 tests.
*Examples.QuickCheck> verboseCheck propRevTwice
Passed:
[]
[] == []

Passed:
[1]
[1] == [1]

Passed:
[-2,1,-1]
[-2,1,-1] == [-2,1,-1]
-- lots of output
+++ OK, passed 100 tests.
*Examples.QuickCheck> quickCheck (propRevTwice [1,2,3])
+++ OK, passed 1 test.
```

即使这个属性在我们的实现中也没有发现错误。让我们尝试另一个。这是关于 `rev (xs ++ ys)` 行为方式的属性。你可能需要花点时间说服自己，它应该适用于正确的 `rev` 函数。

``` haskell
propRevTwo :: [Int] -> [Int] -> Property
propRevTwo xs ys = rev (xs ++ ys) === rev ys ++ rev xs
```

让我们看看它是否适用于我们的实现：

``` haskell
*Examples.QuickCheck> quickCheck propRevTwo
*** Failed! Falsified (after 5 tests and 3 shrinks):
[0,0]
[1]
[0,1,0] /= [1,0,0]
```

最后还是失败了！这里有一些需要解压的内容。首先，QuickCheck 告诉我们属性失败的参数：它们是 `[0,0]` 和 `[1]`。我们可以自己检查一下：

``` haskell
*Examples.QuickCheck> quickCheck (propRevTwo [0,0] [1])
*** Failed! Falsified (after 1 test):
[0,1,0] /= [1,0,0]
```

接下来，“经过5次测试和3次收缩”是什么意思？ QuickCheck 的一个很酷的函数是，当它发现故障时，它会尝试一些相关值，以便找到更好、更小的故障。我们可以通过 `verboseShrinking` 看到这一点，它打印出 QuickCheck 经历的所有失败：

``` haskell
*Examples.QuickCheck> quickCheck (verboseShrinking propRevTwo)
Failed:
[4,-1,-4]
[1,4]
[-1,-4,1,4,4] /= [4,1,-1,-4,4]

Failed:
[-1,-4]
[1,4]
[-4,1,4,-1] /= [4,1,-4,-1]

Failed:
[-4]
[1,4]
[1,4,-4] /= [4,1,-4]

Failed:
[4]
[1,4]
[1,4,4] /= [4,1,4]

Failed:
[0]
[1,4]
[1,4,0] /= [4,1,0]

Failed:
[0]
[0,4]
[0,4,0] /= [4,0,0]

Failed:
[0]
[0,2]
[0,2,0] /= [2,0,0]

Failed:
[0]
[0,1]
[0,1,0] /= [1,0,0]
```

QuickCheck 从 `[4,1,-1,4,4]` 的反例一直下降到 `[1,0,0]`。相当甜蜜！

### 16.1.1 修饰符

有时你需要限制 QuickCheck 生成的值。例如，你的函数可能不适用于所有输入？让我们尝试为 `last` 编写一个测试。

``` haskell
propLast :: [Int] -> Property
propLast xs = last xs === head (reverse xs)
```

``` haskell
*Examples.QuickCheck> quickCheck propLast
*** Failed! Exception: 'Prelude.last: empty list' (after 1 test):
[]
```

在这种情况下，我们只需切换到另一种输入类型即可修复测试。 QuickCheck 定义了 `NonEmptyList` 类型（不要与 `Data.List.NonEmpty` 混淆！），它只是普通列表的包装。但是，当生成 `NonEmptyList` 的值时，QuickCheck 不会生成空列表。

``` haskell
newtype NonEmptyList a = NonEmpty [a]
```

``` haskell
propLastFixed :: NonEmptyList Int -> Property
propLastFixed (NonEmpty xs) = last xs === head (reverse xs)
```

``` haskell
*Examples.QuickCheck> quickCheck propLastFixed
+++ OK, passed 100 tests.
```

还有像这样的[其他修饰符](https://hackage.haskell.org/package/QuickCheck-2.14.3/docs/Test-QuickCheck.html#g:16)，例如 `Positive` 表示正数，`NonNegative` 表示非负数，或 `SortedList` 表示排序列表。这是一个更复杂测试的示例。我们检查 `cycle xs` 的第 n 个元素是否正确。这两个修饰符都是必需的，因为 `!!` 不适用于负输入，并且 `cycle []` 是一个错误。

``` haskell
propCycle :: NonEmptyList Int -> NonNegative Int -> Property
propCycle (NonEmpty xs) (NonNegative n) =
  cycle xs !! n === xs !! (mod n (length xs))
```

### 16.1.2 发电机和 `forAll`

有时我们需要进一步限制测试的输入范围。作为一个简单的例子，下面是一个 `Data.Char.toUpper` 更改传递给它的字符的测试：

``` haskell
propToUpperChanges :: Char -> Property
propToUpperChanges c = toUpper c =/= c
```

``` haskell
quickCheck propToUpperChanges
*** Failed! Falsified (after 1 test and 1 shrink):
'A'
'A' == 'A'
```

当然，它只是改变*小写字母*。我们如何为此编写测试？没有可用的 `Lowercase` 修饰符可以像 `Positive` 或 `NonEmptyList` 一样工作。我们需要使用 `forAll` 显式生成值：

``` haskell
propToUpperChangesLetter :: Property
propToUpperChangesLetter = forAll (elements ['a'..'z']) propToUpperChanges
```

``` haskell
*Examples.QuickCheck> verboseCheck propToUpperChangesLetter
Passed:
's'
'S' /= 's'

Passed:
'z'
'Z' /= 'z'
-- lots of output omitted
+++ OK, passed 100 tests.
```

完美的！让我们看看这些类型，看看这里发生了什么。

``` haskell
elements :: [a] -> Gen a
elements ['a'..'z'] :: Gen Char
forAll :: (Show a, Testable prop) => Gen a -> (a -> prop) -> Property
forAll (elements ['a'..'z']) :: Testable prop => (Char -> prop) -> Property
```

这里有一些新类型。 `Gen a` 类型的值是 `a` 类型值的生成器。我们将在下一节中详细讨论 `Gen`，但在本节中，你将看到几个返回 `Gen` 的函数，以便我们可以将它们与 `forAll` 一起使用。正如你可能已经猜到的那样，`elements` 函数是一个生成器，它随机返回给定列表的元素之一。

`Testable` 类型类与 `quickCheck` 函数使用的类型类相同。它的存在使得 `quickCheck` 除了简单的 `Property` 值之外还可以测试 `[Int] -> Bool -> Property` 等类型。

``` haskell
quickCheck :: Testable prop => prop -> IO ()
```

此外，一些简单类型（例如 `Bool`）具有 `Testable` 实例，因此你可以使用普通 Haskell 谓词而不是 `===` 编写测试：

``` haskell
listHasZero :: [Int] -> Bool
listHasZero xs = elem 0 xs
```

``` haskell
*Examples.QuickCheck> quickCheck (listHasZero [1,0,2])
+++ OK, passed 1 test.
*Examples.QuickCheck> quickCheck listHasZero
*** Failed! Falsified (after 1 test):
[]
```

回到`forAll`，我们可以使用`forAll`来编写更复杂的测试。这是一个测试，检查 `sort xs` 是否具有与 `xs` 相同的元素。请注意我们如何使用 `NonEmptyList` 来保证 `forAll` 有一些元素可供选择。

``` haskell
propSort :: NonEmptyList Int -> Property
propSort (NonEmpty xs) =
  forAll (elements xs) (\x -> elem x (sort xs))
```

### 16.1.3 进一步使用 QuickCheck

我们仅仅触及了 QuickCheck 的皮毛。当你开始编写较大的 QuickCheck 测试时，以下是一些你会发现有用的内容的提示。

有时 QuickCheck 的输出不够详细。你可以使用 `counterexample` 组合器将自己的行添加到输出：

``` haskell
counterexample :: Testable prop => String -> prop -> Property
```

作为示例，我们将 `rev` 的输入日志记录添加到 `propRevTwo`：

``` haskell
propRevTwo' :: [Int] -> [Int] -> Property
propRevTwo' xs ys =
  let input = xs ++ ys
  in counterexample ("Input: " ++ show input) $
     rev input === rev ys ++ rev xs
```

``` haskell
*Examples.QuickCheck> quickCheck propRevTwo'
*** Failed! Falsified (after 4 tests and 5 shrinks):
[0]
[0,1]
Input: [0,0,1]
[0,1,0] /= [1,0,0]
```

你可能已经猜到，`Gen` 是 `Monad`。你可以通过组合 QuickCheck 定义的生成器来编写自己的生成器。你可以使用 `sample` 检查发电机的输出。

``` haskell
someLetters :: Gen String
someLetters = do
  c <- elements "xyzw"
  n <- choose (1,10)
  return (replicate n c)
```

``` haskell
*Examples.QuickCheck> sample someLetters
"yyyyyyyy"
"zzzzzzzzz"
"xxxxxxxxx"
"yyyyyyy"
"yyy"
"ww"
"xxxxxx"
"yyy"
"yyyyyyy"
"xxxxxxxxxx"
"y"
```

与生成器密切相关的是 `Arbitrary` 类型类。 `Arbitrary` 是 QuickCheck 自动生成所有这些输入的方式。

``` haskell
class Arbitrary a where
  arbitrary :: Gen a
  shrink :: a -> [a]
```

如果你正在为自定义类型编写测试，则需要使用 `forAll` 或实现 `Arbitrary` 实例。如果你缺少实例，会发生以下情况：

``` haskell
data Switch = On | Off
  deriving (Show, Eq)

toggle :: Switch -> Switch
toggle On = Off
toggle Off = On

propToggleTwice :: Switch -> Property
propToggleTwice s = s === toggle (toggle s)
```

``` haskell
*Examples.QuickCheck> quickCheck propToggleTwice
error:
    • No instance for (Arbitrary Switch)
        arising from a use of ‘quickCheck’
    • In the expression: quickCheck propToggleTwice
```

以下是修复它的两种方法：

``` haskell
*Examples.QuickCheck> quickCheck (forAll (elements [On,Off]) propToggleTwice)
+++ OK, passed 100 tests.
```

``` haskell
instance Arbitrary Switch where
  arbitrary = elements [On,Off]
```

## 16.2 幻影类型

嘘！类型系统里有鬼！让我们看看“幻像类型”可以为你做什么。

幻像类型是不带任何值的类型。它们与新类型（参见第 10 讲）相关，因为两者都是添加额外类型检查而不影响程序评估的一种方法。

让我们使用幻像类型来跟踪一笔钱所用的货币。我们定义幻像类型 `EUR` 和 `USD` （注意它们没有任何构造函数！），以及不使用类型参数 `a` 的参数化类型 `Money a` 。然后我们可以定义两个常量，一个以欧元为单位，另一个以美元为单位。你可以在文件 [`exercises/Examples/Phantom.hs`](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Examples/Phantom.hs) 中找到本节的所有代码。

``` haskell
data EUR
data USD
data Money currency = Money Double
  deriving Show

dollar :: Money USD
dollar = Money 1

twoEuros :: Money EUR
twoEuros = Money 2
```

请注意 `dollar` 和 `twoEuros` 的类型签名如何完成此处的所有工作。如果我们不将 `Money 1` 这样的表达式限制为更具体的类型，则它具有多态类型 `Money currency`。我们明确地给出了 `dollar` 和 `twoEuros` 更有限的类型。这类似于定义 `one :: Int; one = 1` 之类的东西，因为常量 `1` 具有多态类型 `Num p => p`，但我们给它一个更受限制的类型。

``` haskell
*Examples.Phantom> :t Money
Money :: Double -> Money currency
*Examples.Phantom> :t Money 1
Money 1 :: Money currency
```

现在我们有了一些常量，我们可以编写对它们进行操作的函数。让我们从一个将金额乘以数字的函数 `scaleMoney` 开始。货币保持不变。在这里，类型签名也起作用：没有类型签名，Haskell 会推断出 `Double -> Money a -> Money b` 的类型。

``` haskell
scaleMoney :: Double -> Money currency -> Money currency
scaleMoney factor (Money a) = Money (factor * a)
```

``` haskell
*Examples.Phantom> :t scaleMoney 3 twoEuros
scaleMoney 3 twoEuros :: Money EUR
*Examples.Phantom> :t scaleMoney 3 dollar
scaleMoney 3 dollar :: Money USD
```

下一步：添加两个相同货币的金额。如果我们尝试添加两种不同货币的值，我们会遇到一个很好的类型错误。

``` haskell
addMoney :: Money currency -> Money currency -> Money currency
addMoney (Money a) (Money b) = Money (a+b)
```

``` haskell
*Examples.Phantom> :t addMoney dollar dollar
addMoney dollar dollar :: Money USD
*Examples.Phantom> :t addMoney twoEuros twoEuros
addMoney twoEuros twoEuros :: Money EUR
*Examples.Phantom> :t addMoney twoEuros dollar
error:
    • Couldn't match type ‘USD’ with ‘EUR’
      Expected type: Money EUR
        Actual type: Money USD
    • In the second argument of ‘addMoney’, namely ‘dollar’
      In the expression: addMoney twoEuros dollar
```

和以前一样，类型签名至关重要。这是具有不受限制类型的相同实现。现在我们可以将任何东西添加到任何东西！

``` haskell
addMoneyUnsafe :: Money x -> Money y -> Money z
addMoneyUnsafe (Money a) (Money b) = Money (a+b)
```

``` haskell
*Examples.Phantom> addMoneyUnsafe twoEuros dollar
Money 3.0
```

我们可以继续采用这种方法，并定义货币换算。我们定义了类型 `Rate`，它使用幻像类型来跟踪其之间转换的货币。 `convert` 和 `invert` 的类型被限制为具有我们想要的属性。还有一个无限制版本的转换函数可让你比较类型。

``` haskell
data Rate from to = Rate Double
  deriving Show

eurToUsd :: Rate EUR USD
eurToUsd = Rate 1.22

convert :: Rate from to -> Money from -> Money to
convert (Rate r) (Money a) = Money (r*a)

invert :: Rate from to -> Rate to from
invert (Rate r) = Rate (1/r)

convertUnsafe :: Rate from to -> Money x -> Money y
convertUnsafe (Rate r) (Money a) = Money (r*a)
```

``` haskell
*Examples.Phantom> convert eurToUsd twoEuros
Money 2.44
*Examples.Phantom> convert eurToUsd dollar
error:
    • Couldn't match type ‘USD’ with ‘EUR’
      Expected type: Money EUR
        Actual type: Money USD
    • In the second argument of ‘convert’, namely ‘dollar’
      In the expression: convert eurToUsd dollar
      In an equation for ‘it’: it = convert eurToUsd dollar
*Examples.Phantom> convert (invert eurToUsd) dollar
Money 0.819672131147541
*Examples.Phantom> convertUnsafe eurToUsd dollar
Money 1.22
```

笔记！前面示例中的单词 `currency`、`from`、`to` 等“只是类型变量”。他们没有什么特别的事情发生。我们也可以给 `invert` 像 `Rate a b -> Rate b a` 这样的类型，而不需要对类型安全进行任何更改。

这种使用幻像类型的方法有明显的好处：为我们提供无效代码的类型错误。此外，与定义大量具体类型（如 `data MoneyEur = MoneyEur Double`）相比，使用幻像类型，我们只需实现 `scaleMoney` 和 `addMoney` 等函数一次。此外，我们还能够定义多态和可重用的概念，例如 `Rate`。你可以将此方法与第 7 讲的拳击部分进行对比。

然而，幻像类型也有缺点。如果没有高级技巧，我们就无法真正处理在运行时定义的货币（例如：从用户那里读取金额）。你也很容易开始需要语言扩展，例如 [*广义代数数据类型*](https://wiki.haskell.org/GADTs_for_dummies)、[*类型族*](https://wiki.haskell.org/GHC/Type_families) 和其他[*类型级编程*](https://aphyr.com/posts/342-typing-the-technical-interview) 构造。最终你就进入了[*依赖打字*](https://mitpress.mit.edu/books/little-typer)的世界。

那么幻像类型有哪些好的应用呢？当你需要跟踪一些简单但重要的信息时，这些信息在编译时就已知。比货币更好的一个例子是跟踪用户的输入是否经过清理，以防止 [SQL 注入](https://en.wikipedia.org/wiki/SQL_injection) 或 [跨站点脚本](https://en.wikipedia.org/wiki/Cross-site_scripting) 等攻击。

我们可以使用类型 `Input Safe` 和 `Input Unsafe` 来跟踪字符串是否可以安全地传递到数据库中。如果我们的模块仅导出 `makeInput` 函数，而不导出 `Input` 构造函数，则类型系统确保任何输入在进入 `addForumComment` 等数据库函数之前必须在某个时刻通过 `escapeInput` 函数。

``` haskell
data Safe
data Unsafe

data Input a = Input String

-- Public constructor function for Input, only allows constructing
-- Unsafe Inputs from Strings.
makeInput :: String -> Input Unsafe
makeInput xs = Input xs

-- Adds comment to the database.
addForumComment :: Input Safe -> IO Result
addForumComment = ...

-- We can combine inputs, but that won't change their safety
concatInputs :: Input a -> Input a -> Input a
concatInputs (Input xs) (Input ys) = Input (xs++ys)

-- Strip bad characters to turn an unsafe input safe
escapeInput :: Input Unsafe -> Input Safe
escapeInput (Input xs) = Input (filter (\c -> isAlpha c || isSpace c) xs)
```

## 16.3 同时性

### 16.3.1 并行性

纯性的一大好处是它使“并行性”（同时计算许多东西）变得非常容易。让我们看看如何在 Haskell 中做到这一点。首先，我们需要启动一个启用并行执行的新 GHCi。最简单的方法是：

    $ stack ghci --ghci-options "+RTS -N"

接下来，让我们定义一个非常简单的斐波那契函数版本（还记得第一讲吗？），使用 `:set +s` 启用性能统计，并看看计算该函数的五个值需要多长时间：

``` haskell
Prelude> fib 0 = 1; fib 1 = 1; fib n = fib (n-1) + fib (n-2)
Prelude> :set +s
Prelude> map fib [29,29,29,29,29]
[832040,832040,832040,832040,832040]
(7.54 secs, 2,440,860,632 bytes)
```

现在让我们引入模块 [Control.Parallel.Strategies](https://hackage.haskell.org/package/parallel-3.2.2.0/docs/Control-Parallel-Strategies.html)，它定义了并行计算值的方法。我们将使用 `parList rseq` 策略*并行*将列表中的所有元素评估为 WHNF。

``` haskell
Prelude> import Control.Parallel.Strategies
Prelude Control.Parallel.Strategies> withStrategy (parList rseq) (map fib [29,29,29,29,29])
[832040,832040,832040,832040,832040]
(4.80 secs, 488,531,384 bytes)
```

在运行本示例的 2 核机器上，速度几乎是原来的 2 倍。相当不错。这里最酷的事情是我们能够完全独立于*评估策略*（`parList rseq`）来定义*计算*（`map fib ...`），将*计算什么*与*如何计算*分开。

### 16.3.2 并发

计算机科学区分了并行和“并发”计算。并行计算是那些仅并行运行单独的独立计算的计算（换句话说，并行性是“纯的”）。并发计算是指存在多个交互计算线程的计算。并发通常涉及线程、锁、消息和死锁。

除了出色的并行工具之外，Haskell 还通过“线程”提供了出色的并发工具。由于并发性与副作用有关，因此并发计算发生在 `IO` Monad 中。线程的经典示例是两个线程，一个打印 As 流，另一个打印 Bs 流。这是 Haskell 中的：

``` haskell
printA :: IO ()
printA = putStrLn (replicate 40 'A')

printB :: IO ()
printB = putStrLn (replicate 40 'B')

concurrency :: IO ()
concurrency = do
  forkIO printA
  forkIO printB
  return ()
```

    Prelude Control.Concurrent> concurrency
    AABABABABABABABABABABABABABABABABABABABABABABABABABABABABABABABABABABABABABABABB

操作 `forkIO :: IO () -> IO ThreadId` 接受 IO 操作并开始在后台运行它。它产生 `ThreadId`，可用于例如 终止线程。

如果我们想在线程之间添加实际通信，我们可以使用 [`MVar`](https://hackage.haskell.org/package/base-4.16.4.0/docs/Control-Concurrent-MVar.html) （可变线程安全变量）或 [`Chan`](https://hackage.haskell.org/package/base-4.16.4.0/docs/Control-Concurrent-Chan.html) （队列）等抽象。

这是一个简单的示例，其中一个线程向 `MVar` 写入一个值，另一个线程等待它们并打印它们。 `MVar` 的工作方式类似于邮箱：它要么是空的，要么是满的。在空盒子上调用 `takeMVar` 等待盒子被填充（使用 `putMVar`）。对称地，尝试将 `putMVar` 放入已满的盒子中会等到盒子为空。

``` haskell
takeMVar :: MVar a -> IO a
putMVar :: MVar a -> a -> IO ()
newEmptyMVar :: IO (MVar a)
```

``` haskell
send :: [String] -> MVar String -> IO ()
send values var = mapM_ (putMVar var) values

receive :: MVar String -> IO ()
receive var = do val <- takeMVar var
                 print val
                 -- loop unless at last value
                 when (val/="end") (receive var)

concurrency2 :: IO ()
concurrency2 = do
  var <- newEmptyMVar
  forkIO (send ["hello","world","and","goodbye","end"] var)
  forkIO (receive var)
  return ()
```

``` haskell
Prelude Control.Concurrent Control.Monad> concurrency2
"hello"
"world"
"and"
"goodbye"
"end"
```

## 16.4 练习

- [Set16a](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set16a.hs)：快速检查
- [Set16b](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set16b.hs): 幻像类型
- 没有并行或并发 Haskell 练习，抱歉！

## 16.5 接下来去哪里？

恭喜！你已经完成了关于 Haskell 函数式编程的两部分课程的结尾。接下来怎么办？你绝对了解足够的 Haskell 来继续自学。 Haskell 在线社区非常友好，有大量博客文章和其他内容解释高级技术和函数。你可以通过以下示例找到很多有趣的东西：

- Reddit 上的 [/r/haskell](https://www.reddit.com/r/haskell/)
- [libera.chat](https://libera.chat) 上的 `#haskell`
- [Haskell周刊](https://haskellweekly.news/)
- 堆栈溢出

只要继续写Haskell，当你遇到东西（比如库和工具）时就研究它们，慢慢积累经验。你通过 Haskell 学到的很多东西都可以转移到其他语言，例如 [TypeScript](https://www.typescriptlang.org/)、[Elm](https://elm-lang.org/)、[Rust](https://www.rust-lang.org/) 或 [F#](https://fsharp.org/)。

最后，这里列出了本课程中未提及但值得研究的内容的不完整列表：

- 语言特征
  - [模块](https://en.wikibooks.org/wiki/Haskell/Modules)
  - 惰性模式（`~` 模式）和 `@` 模式。参见例如 [Haskell 简介](https://www.haskell.org/tutorial/patterns.html)。
  - 语言扩展，如 `MultiParamTypeClasses`、`ViewPatterns` 等。 [这是一本很好的指南](https://limperg.de/ghc-extensions/)
  - `fix`函数
  - 用于从 Haskell 调用 C 代码的外部函数接口
- 抽象
  - Monad 变压器：[RWH](https://book.realworldhaskell.org/read/monad-transformers.html)、[Wikibook](https://en.wikibooks.org/wiki/Haskell/Monad_transformers)
  - 免费 monad（高级主题）：[博客](https://www.haskellforall.com/2012/06/you-could-have-invented-free-monads.html)
  - 镜头（高级主题）：[教程](https://hackage.haskell.org/package/lens-tutorial-1.0.4/docs/Control-Lens-Tutorial.html) [玻璃](https://oleg.fi/gists/posts/2017-04-18-glassery.html)
  - [Bartosz Milewski](https://bartoszmilewski.com/) 在他的博客上涵盖了许多中级和高级主题
- 工装
  - 使用[Cabal](https://www.haskell.org/cabal/)和[Stack](https://www.haskellstack.org)构建自己的项目
  - 分析：[RWH](https://book.realworldhaskell.org/read/profiling-and-optimization.html)、[GHC](https://downloads.haskell.org/~ghc/latest/docs/html/users_guide/profiling.html)
  - [Hlint](https://github.com/ndmitchell/hlint#readme)
- 库
  - 秒差距（解析）：[RWH](https://book.realworldhaskell.org/read/using-parsec.html)
  - Scotty（简单的网络框架），Aeson（json）：[博客]（https://seanhess.github.io/2015/08/19/practical-haskell-json-api.html）
  - [Servant](https://haskell-servant.github.io/)（带有幻像类型的精美网络框架）
- 范畴论
  - 许多 Haskell 抽象都是基于范畴论
  - 范畴论可以成为编程新思想的宝贵来源
  - 范畴论可能会让人感到害怕，所以很高兴知道没有它你也能过得很好
  - Bartosz Milewski 有很多好资料，例如[程序员的范畴论](https://bartoszmilewski.com/2014/10/28/category-theory-for-programmers-the-preface/)
  - [Haskell Wiki](https://wiki.haskell.org/Category_theory) 和 [Wikibook](https://en.wikibooks.org/wiki/Haskell/Category_theory) 有范畴论部分

## 16.6 致谢

本课程是由 [Nitor](https://nitor.com/en) 完成的，他为这个项目捐赠了 Joel 的大量工作时间。谢谢你！

感谢整个 Haskell Mooc 团队，特别是

- John Lång 寻求有关材料的帮助
- Antti Laaksonen 设置课程并帮助安排
- Topi Talvitie 用于演习检查基础设施

感谢所有耐心等待第二部分并报告材料和练习错误的学生！
