# Haskell 慕课，第 2 部分

- [9 第 9 讲：第 1 部分回顾](#lecture-9-recap-of-part-1)
  - [9.1 类型](#types)
  - [9.2 函数](#functions)
  - [9.3 函数式编程](#functional-programming)
  - [9.4 递归](#recursion)
  - [9.5 类型类](#type-classes)
  - [9.6 测验](#quiz)
  - [9.7 做练习](#working-on-the-exercises)
  - [9.8 练习](#exercises)


<a id="lecture-9-recap-of-part-1"></a>

# 9 第 9 讲：第 1 部分回顾

本讲座回顾了课程第 1 部分中介绍的 Haskell 的基本部分：类型、值、模式匹配、函数和递归。


<a id="types"></a>

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


<a id="functions"></a>

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


<a id="functional-programming"></a>

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


<a id="recursion"></a>

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


<a id="type-classes"></a>

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


<a id="quiz"></a>

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


<a id="working-on-the-exercises"></a>

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


<a id="exercises"></a>

## 9.8 练习

- [Set9a](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set9a.hs) - 回顾课程第 1 部分的小练习
- [Set9b](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set9b.hs) - 让我们解决 N 皇后难题！
