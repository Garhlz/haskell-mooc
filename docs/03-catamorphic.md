# 第 3 讲：折叠与高阶函数

- 列表，列表，列表
- 函数式编程
- 关于类型的一些知识

## 3.1 终于开始函数式编程

现在有了列表和多态性这些工具，我们终于可以开始研究函数式编程了。

在 Haskell 中，函数是一个值，就像数字或列表一样。函数可以作为参数传递给其他函数。这是一个简单的例子。函数 `applyTo1` 接受类型为 `Int->Int` 的函数，将其应用于数字 `1`，并返回结果。

```haskell
applyTo1 :: (Int -> Int) -> Int
applyTo1 f = f 1
```

让我们定义一个 `Int->Int` 类型的简单函数，并看看 `applyTo1` 的实际效果。

```haskell
addThree :: Int -> Int
addThree x = x + 3
```

```haskell
applyTo1 addThree
  ==> addThree 1
  ==> 1 + 3
  ==> 4
```

让我们回到 `applyTo1` 的类型标注。

```haskell
applyTo1 :: (Int -> Int) -> Int
```

这里需要括号，因为类型 `Int -> Int -> Int` 表示接受两个 `Int` 参数的函数。稍后会详细介绍这一点。

让我们看一个稍微有趣的例子。这次我们将实现一个多态函数 `doTwice`。请注意我们如何将它与各种类型的值和函数一起使用。

```haskell
doTwice :: (a -> a) -> a -> a
doTwice f x = f (f x)
```

```haskell
doTwice addThree 1
  ==> addThree (addThree 1)
  ==> 7
doTwice tail "abcd"
  ==> tail (tail "abcd")
  ==> "cd"
```

```haskell
makeCool :: String -> String
makeCool str = "WOW " ++ str ++ "!"
```

```haskell
doTwice makeCool "Haskell"
  ==> "WOW WOW Haskell!!"
```

### 3.1.1 列表上的函数式编程

那有点无聊。幸运的是，有许多有用的列表函数以函数作为参数。顺便说一句，以函数作为参数（或返回函数）的函数通常称为“高阶函数”。

这些处理列表的高阶函数里，最有名的是 `map`。它会把给定函数应用到列表的每个元素上，并返回一个新列表。

```haskell
map :: (a -> b) -> [a] -> [b]
```

```haskell
map addThree [1,2,3]
  ==> [4,5,6]
```

`filter` 常常和 `map` 搭配使用。`filter` 不是转换列表的所有元素，而是删除列表的一些元素并保留其他元素。换句话说， `filter` 从列表中选择满足条件的元素。

```haskell
filter :: (a -> Bool) -> [a] -> [a]
```

这是一个例子：从列表中选择正数元素

```haskell
positive :: Int -> Bool
positive x = x>0
```

```haskell
filter positive [0,1,-1,3,-3]
  ==> [1,3]
```

请注意 `map` 和 `filter` 的类型签名如何使用多态性。它们适用于各种列表。`map` 的类型甚至使用了两个类型参数！以下是使用 `map` 和 `filter` 进行类型推断的一些示例。

```haskell
onlyPositive xs = filter positive xs
mapBooleans f = map f [False,True]
```

```haskell
Prelude> :t onlyPositive
onlyPositive :: [Int] -> [Int]
Prelude> :t mapBooleans
mapBooleans :: (Bool -> b) -> [b]
Prelude> :t mapBooleans not
mapBooleans not :: [Bool]
```

还有一件事：还记得构造函数只是函数吗？这表示你可以将它们作为参数传递给其他函数！

```haskell
wrapJust xs = map Just xs
```

```haskell
Prelude> :t wrapJust
wrapJust :: [a] -> [Maybe a]
Prelude> wrapJust [1,2,3]
[Just 1,Just 2,Just 3]
```

### 3.1.2 列表处理示例

`1` 和 `n` 之间有多少个“回文数”？

```haskell
-- a predicate that checks if a string is a palindrome
palindrome :: String -> Bool
palindrome str = str == reverse str

-- palindromes n takes all numbers from 1 to n, converts them to strings using show, and keeps only palindromes
palindromes :: Int -> [String]
palindromes n = filter palindrome (map show [1..n])
```

```haskell
palindrome "1331" ==> True
palindromes 150 ==>
  ["1","2","3","4","5","6","7","8","9",
   "11","22","33","44","55","66","77","88","99",
   "101","111","121","131","141"]
length (palindromes 9999) ==> 198
```

字符串中有多少个以“a”开头的单词？这使用模块 `Data.List` 中的函数 `words` 将字符串拆分为单词。

```haskell
countAWords :: String -> Int
countAWords string = length (filter startsWithA (words string))
  where startsWithA s = head s == 'a'
```

```haskell
countAWords "does anyone want an apple?"
  ==> 3
```

`Data.List` 中的函数 `tails` 返回列表的所有后缀（“尾部”）的列表。我们可以使用 `tails` 来完成许多字符串处理任务。`tails` 的工作原理如下：

```haskell
tails "echo"
  ==> ["echo","cho","ho","o",""]
```

这是一个示例，我们可以查找字符串中给定字符之后的字符。首先，我们使用 `tails`、`map` 和 `take` 来获取一定长度的所有子串：

```haskell
substringsOfLength :: Int -> String -> [String]
substringsOfLength n string = map shorten (tails string)
  where shorten s = take n s
```

```haskell
substringsOfLength 3 "hello"
  ==> ["hel","ell","llo","lo","o",""]
```

最后还剩下一些较短的子字符串（你能明白为什么吗？），但它们现在就可以满足我们的目的。现在我们有了 `substringsOfLength`，我们可以实现函数 `whatFollows c k s` 来查找字符串 `s` 中出现的所有字符 `c`，并输出这些出现之后的 `k` 字母。

```haskell
whatFollows :: Char -> Int -> String -> [String]
whatFollows c k string = map tail (filter match (substringsOfLength (k+1) string))
  where match sub = take 1 sub == [c]
```

```haskell
whatFollows 'a' 2 "abracadabra"
  ==> ["br","ca","da","br",""]
```

## 3.2 偏应用

使用高阶函数时，你会发现自己定义了许多小辅助函数，例如前面示例中的 `addThree` 或 `shorten`。从长远来看，这有点麻烦，但幸运的是 Haskell 的函数表现得有点奇怪……

让我们从 GHCi 开始：

```haskell
Prelude> add a b = a+b
Prelude> add 1 5
6
Prelude> addThree = add 3
Prelude> addThree 2
5
```

因此，我们定义了 `add`，一个有两个参数的函数，并且只给它一个参数。结果不是类型错误而是新函数。新函数只是存储（或记住）给定的参数，等待另一个参数，然后将两者都提供给 `add`。

```haskell
Prelude> map addThree [1,2,3]
[4,5,6]
Prelude> map (add 3) [1,2,3]
[4,5,6]
```

在这里我们可以看到，我们甚至不需要为 `add 3` 返回的函数命名。我们可以在任何需要一个参数的函数的地方使用它。

这称为“偏应用”。Haskell 中的所有函数都是这样的。让我们仔细看看。这是一个带有多个参数的函数。

```haskell
between :: Integer -> Integer -> Integer -> Bool
between lo high x = x < high && x > lo
```

```haskell
Prelude> between 3 7 5
True
Prelude> between 3 6 8
False
```

我们可以给 `between` 更少的参数并返回新函数，就像我们在 `add` 中看到的那样：

```haskell
Prelude> (between 1 5) 2
True
Prelude> let f = between 1 5 in f 2
True
Prelude> map (between 1 3) [1,2,3]
[False,True,False]
```

查看偏应用 `between` 的类型。它们的行为很整齐，随着值被添加到表达式中，参数从类型中一一消失。

```haskell
Prelude> :t between
between :: Integer -> Integer -> Integer -> Bool
Prelude> :t between 1
between 1 :: Integer -> Integer -> Bool
Prelude> :t between 1 2
between 1 2 :: Integer -> Bool
Prelude> :t between 1 2 3
between 1 2 3 :: Bool
```

实际上，当我们编写像 `Integer -> Integer -> Integer -> Bool` 这样的类型时，它意味着 `Integer -> (Integer -> (Integer -> Bool))`。也就是说，多参数函数只是一个返回函数的函数。同样，像 `between 1 2 3` 这样的表达式与 `((between 1) 2) 3` 相同，因此通过多个单参数调用将多个参数传递给函数。像这样表示多参数函数称为“柯里化”（以逻辑学家 Haskell Curry 命名）。柯里化使得偏应用成为可能。

这是使用 `map` 偏应用的另一个示例：

```haskell
map (drop 1) ["Hello","World!"]
  ==> ["ello","orld!"]
```

除了普通函数外，运算符也可以偏应用。使用运算符时，你可以选择固定左参数还是右参数。（偏应用的运算符也称为 *section* 或*运算符 section*。）一些例子：

```haskell
Prelude> map (*2) [1,2,3]
[2,4,6]
Prelude> map (2*) [1,2,3]
[2,4,6]
Prelude> map (1/) [1,2,3,4,5]
[1.0,0.5,0.3333333333333333,0.25,0.2]
```

## 3.3 前缀和中缀表示法

普通的 Haskell 函数使用*前缀表示法*，也就是说函数名位于参数之前。相对地，运算符使用*中缀表示法*，也就是运算符位于参数之间。

通过在中缀运算符两边加上括号，可以将其转换为前缀函数。例如，

```haskell
(+) 1 2 ==> 1 + 2 ==> 3
```

这在需要将运算符作为参数传递给另一个函数时尤其有用。

例如，函数 `zipWith` 接受一个二元函数和两个列表，并用这个函数把两个列表逐个元素组合起来。我们可以使用 `zipWith (+)` 对两个列表逐个元素求和：

```haskell
Prelude> :t zipWith
zipWith :: (a -> b -> c) -> [a] -> [b] -> [c]
Prelude> zipWith (+) [0,2,5] [1,3,3]
[1,5,8]
```

如果无法将运算符转换为函数，我们就必须使用辅助函数——例如上面的 `add`。

请注意，省略括号会导致类型错误：

```haskell
Prelude> zipWith + [0,2,5,3] [1,3,3]

<interactive>:1:11: error:
    • Couldn't match expected type ‘[Integer]
                                    -> (a -> b -> c) -> [a] -> [b] -> [c]’
                  with actual type ‘[Integer]’
    • The function ‘[0, 2, 5, 3]’ is applied to one argument,
      but its type ‘[Integer]’ has none
      In the second argument of ‘(+)’, namely ‘[0, 2, 5, 3] [1, 3, 3]’
      In the expression: zipWith + [0, 2, 5, 3] [1, 3, 3]
    • Relevant bindings include
        it :: (a -> b -> c) -> [a] -> [b] -> [c]
          (bound at <interactive>:1:1)
```

出现这个奇怪错误的原因是 GHCi 理解错了表达式，它以为我们在尝试把 `zipWith` 和 `[0,2,5,3] [1,3,3]` 相加。按这个理解，`[0,2,5,3]` 必须是一个函数，因为它被应用到了 `[1,3,3]` 上（请记住，函数应用比运算符绑定得更紧）。

不幸的是，错误消息有时可能很难懂，因为编译器并不总是知道错误的“真正”原因（在这种情况下省略了括号）。奇怪的错误消息令人沮丧，但只有程序员知道代码背后的原本意图是什么。

Haskell 还有一个很好用的语法：你可以用反引号字符把二元函数名括起来，让它像中缀运算符一样使用。例如：

```haskell
6 `div` 2 ==> div 6 2 ==> 3
(+1) `map` [1,2,3] ==> map (+1) [1,2,3] ==> [2,3,4]
```

## 3.4 lambda 表达式

函数式编程工具箱里最后还需要一个工具：λ（lambda）。lambda 表达式是*匿名函数*。考虑这样一种情况：你只需要临时使用一个函数，例如下面这个表达式：

```haskell
let big x = x>7 in filter big [1,10,100]
```

lambda 表达式允许我们直接编写它，而无需为辅助函数定义名称 (`big`)：

```haskell
filter (\x -> x>7) [1,10,100]
```

以下是 GHCi 中的更多示例：

```haskell
Prelude> (\x -> x*x) 3
9
Prelude> (\x -> reverse x == x) "ABBA"
True
Prelude> filter (\x -> reverse x == x) ["ABBA","ACDC","otto","lothar","anna"]
["ABBA","otto","anna"]
Prelude> (\x y -> x^2+y^2) 2 3           -- multiple arguments
13
```

Haskell 的 lambda 语法一开始可能有点意外。反斜杠字符（`\`）代表希腊字母 lambda（λ）。Haskell 表达式 `\x -> x+1` 试图模仿数学记法 *λx. x+1*。其他语言会使用类似 `x => x+1`（JavaScript）或 `lambda x: x+1`（Python）的语法。

**注意！** 你并不是“必须”使用 lambda 表达式。你总是可以用 `let` 或 `where` 正常定义一个函数。

顺便说一句，lambda 表达式是非常强大的构造，它们有自己的深层理论，称为[Lambda calculus](https://en.wikipedia.org/wiki/Lambda_calculus)。有些人甚至认为 Haskell 等纯函数式编程语言是带有额外语法的 lambda 演算的类型扩展。

## 3.5 附注：`.` 和 `$` 运算符

Haskell 代码库中最常见的两个运算符可能是 `.` 和 `$`。它们在编写使用高阶函数的代码时非常有用。其中第一个 `.` 运算符是*函数组合*运算符。这是它的类型

```haskell
(.) :: (b -> c) -> (a -> b) -> a -> c
```

这就是它的作用

```haskell
(f.g) x ==> f (g x)
```

你可以使用函数组合从其他函数构建函数，而无需提及任何参数。例如：

```haskell
double x = 2*x
quadruple = double . double  -- computes 2*(2*x) == 4*x
f = quadruple . (+1)         -- computes 4*(x+1)
g = (+1) . quadruple         -- computes 4*x+1
third = head . tail . tail   -- fetches the third element of a list
```

我们还可以使用 `(.)` 重新实现 `doTwice`。请注意我们如何使用 `doTwice` 既可以仅应用于函数，也可以应用于函数和值。

```haskell
doTwice :: (a -> a) -> a -> a
doTwice f = f . f
```

```haskell
let ttail = doTwice tail
in ttail [1,2,3,4]
  ==> [3,4]

(doTwice tail) [1,2,3,4] ==> [3,4]

doTwice tail [1,2,3,4] ==> [3,4]
```

定义新函数时通常不使用函数组合，而是为了避免定义辅助函数。例如，考虑这两个表达式之间的差异：

```haskell
let notEmpty x = not (null x)
in filter notEmpty [[1,2,3],[],[4]]
  ==> [[1,2,3],[4]]
```

```haskell
filter (not . null) [[1,2,3],[],[4]]
  ==> [[1,2,3],[4]]
```

另一个运算符 `$` 更加微妙。我们来看看它的类型。

```haskell
($) :: (a -> b) -> a -> b
```

它采用 `a -> b` 类型的函数和 `a` 类型的值，并返回 `b` 类型的值。换句话说，它是一个函数应用运算符。表达式 `f $ x` 与 `f x` 相同。这看起来没什么用，但这表示 `$` 运算符可以用来消除括号！这些表达式是相同的：

```haskell
head (reverse "abcd")
head $ reverse "abcd"
```

当它用于消除一对括号时，这并不令人印象深刻，但 `.` 和 `$` 一起可以消除很多括号！例如我们可以重写

```haskell
reverse (map head (map reverse (["Haskell","pro"] ++ ["dodo","lyric"])))
```

作为

```haskell
(reverse . map head . map reverse) (["Haskell","pro"] ++ ["dodo","lyric"])
```

进而

```haskell
reverse . map head . map reverse $ ["Haskell","pro"] ++ ["dodo","lyric"]
```

有时，运算符 `.` 和 `$` 本身作为函数很有用。例如，可以使用 map 和 `$` 的一部分将函数列表应用到参数：

```haskell
map ($"string") [reverse, take 2, drop 2]
  ==> [reverse $ "string", take 2 $ "string", drop 2 $ "string"]
  ==> [reverse "string", take 2 "string", drop 2 "string"]
  ==> ["gnirts", "st", "ring"]
```

如果这看起来很复杂，请不要担心。在你熟悉 `.` 和 `$` 之前，不需要在自己的代码中使用它们。然而，当你在网上阅读 Haskell 示例和代码时，经常会遇到 `.` 和 `$`，因此了解它们是有好处的。[这篇文章](https://typeclasses.com/featured/dollar)也可能有帮助。

## 3.6 示例：重写 `whatFollows`

现在，让我们使用刚刚看到的工具重写之前的 `whatFollows` 示例。这是原始版本：

```haskell
substringsOfLength :: Int -> String -> [String]
substringsOfLength n string = map shorten (tails string)
  where shorten s = take n s

whatFollows :: Char -> Int -> String -> [String]
whatFollows c k string = map tail (filter match (substringsOfLength (k+1) string))
  where match sub = take 1 sub == [c]
```

首先，让我们摆脱辅助函数 `substringsOfLength` 并将所有代码移至 `whatFollows`：

```haskell
whatFollows c k string = map tail (filter match (map shorten (tails string)))
  where shorten s = take (k+1) s
        match sub = take 1 sub == [c]
```

现在让我们使用偏应用而不是定义 `shorten`：

```haskell
whatFollows c k string = map tail (filter match (map (take (k+1)) (tails string)))
  where match sub = take 1 sub == [c]
```

让我们使用 `.` 和 `$` 来消除其中一些括号：

```haskell
whatFollows c k string = map tail . filter match . map (take (k+1)) $ tails string
  where match sub = take 1 sub == [c]
```

我们还可以用 lambda 替换 `match`：

```haskell
whatFollows c k string = map tail . filter (\sub -> take 1 sub == [c]) . map (take (k+1)) $ tails string
```

最后，我们根本不需要提及 `string` 参数，因为我们可以将 `whatFollows` 表示为 `map`、`filter`、`map` 和 `tails` 的组合：

```haskell
whatFollows c k = map tail . filter (\sub -> take 1 sub == [c]) . map (take (k+1)) . tails
```

我们甚至可以更进一步，使用运算符部分重写 lambda

```haskell
    \sub -> take 1 sub == [c]
=== \sub -> (==[c]) (take 1 sub)
=== \sub -> (==[c]) ((take 1) sub)
=== \sub -> ((==[c]) . (take 1)) sub
=== ((==[c]) . (take 1))
=== ((==[c]) . take 1)
```

现在我们剩下的是：

```haskell
whatFollows c k = map tail . filter ((==[c]) . take 1) . map (take (k+1)) . tails
```

这是该函数的一个有点极端的版本，但是当适度使用时，此处显示的技术可以使代码更易于阅读。

## 3.7 更多函数式列表处理示例

以下是一些使用列表进行函数式编程的更多示例。让我们首先介绍几个新的列表函数：

```haskell
takeWhile :: (a -> Bool) -> [a] -> [a]   -- take elements from a list as long as they satisfy a predicate
dropWhile :: (a -> Bool) -> [a] -> [a]   -- drop elements from a list as long as they satisfy a predicate
```

```haskell
takeWhile even [2,4,1,2,3]   ==> [2,4]
dropWhile even [2,4,1,2,3]   ==> [1,2,3]
```

还有函数 `elem`，可用于检查列表是否包含元素：

```haskell
elem 3 [1,2,3]   ==> True
elem 4 [1,2,3]   ==> False
```

使用这些，我们可以实现一个函数 `findSubstring`，该函数查找仅由给定字符组成的字符串中最早和最长的子字符串。

```haskell
findSubstring :: String -> String -> String
findSubstring chars = takeWhile (\x -> elem x chars)
                      . dropWhile (\x -> not $ elem x chars)
```

```haskell
findSubstring "a" "bbaabaaaab"              ==> "aa"
findSubstring "abcd" "xxxyyyzabaaxxabcd"    ==> "abaa"
```

函数 `zipWith` 允许你逐个元素地组合两个列表：

```haskell
zipWith :: (a -> b -> c) -> [a] -> [b] -> [c]
```

```haskell
zipWith (++) ["John","Mary"] ["Smith","Cooper"]
  ==> ["JohnSmith","MaryCooper"]
zipWith take [4,3] ["Hello","Warden"]
  ==> ["Hell","War"]
```

有时，对于高阶函数，拥有一个不执行任何操作的函数会很有用。函数 `id :: a -> a` 是恒等函数，仅返回其参数。

```haskell
id 3 ==> 3
map id [1,2,3] ==> [1,2,3]
```

这似乎有点无用，但你可以将其与 `filter` 或 `dropWhile` 一起使用：

```haskell
filter id [True,False,True,True]  ==>  [True,True,True]
dropWhile id [True,True,False,True,False]  ==>  [False,True,False]
```

另一个非常简单但有时至关重要的函数是常量函数 `const :: a -> b -> a`。它总是返回它的第一个参数：

```haskell
const 3 True ==> 3
const 3 0    ==> 3
```

当偏应用时，当你需要一个始终返回相同值的函数时，可以使用它：

```haskell
map (const 5) [1,2,3,4] ==> [5,5,5,5]
filter (const True) [1,2,3,4] ==> [1,2,3,4]
```

## 3.8 列表和递归

这是一个新的运算符， `:`

```haskell
Prelude> 1:[]
[1]
Prelude> 1:[2,3]
[1,2,3]
Prelude> tail (1:[2,3])
[2,3]
Prelude> head (1:[2,3])
1
Prelude> :t (:)
(:) :: a -> [a] -> [a]
```

`:` 运算符根据头部和尾部构建一个列表。换句话说， `x : xs` 与 `[x] ++ xs` 相同。为什么我们需要一个操作员来做到这一点？

实际上， `:` 是列表的*构造函数*：它返回一个新的链表节点。另一个列表构造函数是 `[]`，即空列表。所有列表均使用 `:` 和 `[]` 构建。熟悉的 `[x,y,z]` 语法实际上只是一种更好的编写 `x:y:z:[]` 的方法，或者更明确地编写 `x:(y:(z:[]))`。事实上， `(++)` 是根据标准库中的 `:` 和递归来定义的。

下面是 `[1,2,3]` 在内存中的结构图：

![](img/list123.svg)

### 3.8.1 构建列表

使用 `:` 我们可以定义构建列表的递归函数。例如，这是一个构建类似 `[3,2,1]` 的列表的函数：

```haskell
descend 0 = []
descend n = n : descend (n-1)
```

```haskell
descend 4 ==> [4,3,2,1]
```

这是一个通过迭代函数 `n` 次来构建列表的函数：

```haskell
iterate f 0 x = [x]
iterate f n x = x : iterate f (n-1) (f x)
```

```haskell
iterate (*2) 4 3 ==> [3,6,12,24,48]

let xs = "terve"
in iterate tail (length xs) xs
  ==> ["terve","erve","rve","ve","e",""]
```

这是一个更复杂的示例：在给定字符处将字符串拆分为多个片段：

```haskell
split :: Char -> String -> [String]
split c [] = []
split c xs = start : split c (drop 1 rest)
  where start = takeWhile (/=c) xs
        rest = dropWhile (/=c) xs
```

```haskell
split 'x' "fooxxbarxquux"   ==>   ["foo","","bar","quu"]
```

### 3.8.2 列表的模式匹配

上一讲，有人说构造函数是可以进行模式匹配的东西。上面透露了列表类型的构造函数是 `:` 和 `[]`。我们可以将一加一放在一起，猜测我们可以在 `:` 和 `[]` 上进行模式匹配。这是真实的！以下是如何使用模式匹配定义自己的 `head` 和 `tail` 版本：

```haskell
myhead :: [Int] -> Int
myhead [] = -1
myhead (first:rest) = first

mytail :: [Int] -> [Int]
mytail [] = []
mytail (first:rest) = rest
```

你可以*嵌套*模式。也就是说，你可以从列表开头开始对多个元素进行模式匹配。在此示例中，我们使用与 `(a:(b:_))` 相同的模式 `(a:b:_)`：

```haskell
sumFirstTwo :: [Integer] -> Integer
-- this equation gets used for lists of length at least two
sumFirstTwo (a:b:_) = a+b
-- this equation gets used for all other lists (i.e. lists of length 0 or 1)
sumFirstTwo _       = 0
```

```haskell
sumFirstTwo [1]      ==> 0
sumFirstTwo [1,2]    ==> 3
sumFirstTwo [1,2,4]  ==> 3
```

这是一个使用许多不同列表模式的示例：

```haskell
describeList :: [Int] -> String
describeList []         = "an empty list"
describeList (x:[])     = "a list with one element"
describeList (x:y:[])   = "a list with two elements"
describeList (x:y:z:xs) = "a list with at least three elements"
```

```haskell
describeList [1,3]        ==> "a list with two elements"
describeList [1,2,3,4,5]  ==> "a list with at least three elements"
```

以 `:[]` 结尾的列表模式可以作为列表文字键入。也就是说，就像 `[1,2,3]` 与 `1:2:3:[]` 的值相同一样，模式 `[x,y]` 与模式 `x:y:[]` 相同。让我们重写前面的例子。

```haskell
describeList :: [Int] -> String
describeList []         = "an empty list"
describeList [x]        = "a list with exactly one element"
describeList [x,y]      = "a list with exactly two elements"
describeList (x:y:z:xs) = "a list with at least three elements"
```

嵌套模式的另一种方法是在头部进行模式匹配，而在列表上进行模式匹配。例如，此函数检查列表是否以 `0` 开头：

```haskell
startsWithZero :: [Integer] -> Bool
startsWithZero (0:xs) = True
startsWithZero (x:xs) = False
startsWithZero []     = False
```

### 3.8.3 使用列表

使用模式匹配和递归，我们可以递归地处理整个列表。以下是对列表中所有数字求和的方法：

```haskell
sumNumbers :: [Int] -> Int
sumNumbers [] = 0
sumNumbers (x:xs) = x + sumNumbers xs
```

以下是计算列表中最大数字的方法，这次使用辅助函数。

```haskell
myMaximum :: [Int] -> Int
myMaximum [] = 0       -- actually this should be some sort of error...
myMaximum (x:xs) = go x xs
  where go biggest [] = biggest
        go biggest (x:xs) = go (max biggest x) xs
```

**注意！**，“`go`”只是这里辅助函数的一个可爱的名字。这不是特殊的语法。

在使用列表时使用嵌套模式通常很方便。下面是一个计算 `Maybe` 列表中出现了多少个 `Nothing` 值的示例：

```haskell
countNothings :: [Maybe a] -> Int
countNothings [] = 0
countNothings (Nothing : xs) = 1 + countNothings xs
countNothings (Just _  : xs) = countNothings xs
```

```haskell
countNothings [Nothing,Just 1,Nothing]  ==>  2
```

### 3.8.4 构建和使用列表

现在我们可以构建和使用列表了，让我们同时进行这两个操作。此函数将列表中的所有元素加倍。

```haskell
doubleList :: [Int] -> [Int]
doubleList [] = []
doubleList (x:xs) = 2*x : doubleList xs
```

它的求值如下：

```haskell
doubleList [1,2,3]
=== doubleList (1:(2:(3:[])))
==> 2*1 : doubleList (2:(3:[]))
==> 2*1 : (2*2 : doubleList (3:[]))
==> 2*1 : (2*2 : (2*3 : doubleList []))
==> 2*1 : (2*2 : (2*3 : []))
=== [2*1, 2*2, 2*3]
==> [2,4,6]
```

一旦了解了列表的模式匹配，就可以直接定义 `map` 和 `filter`。实际上，我们只看GHC标准库的实现。[Here’s map](https://hackage.haskell.org/package/base-4.16.4.0/docs/src/GHC.Base.html#map):

```haskell
map :: (a -> b) -> [a] -> [b]
map _ []     = []
map f (x:xs) = f x : map f xs
```

和[here’s filter](https://hackage.haskell.org/package/base-4.16.4.0/docs/src/GHC.List.html#filter)：

```haskell
filter :: (a -> Bool) -> [a] -> [a]
filter _pred []    = []
filter pred (x:xs)
  | pred x         = x : filter pred xs
  | otherwise      = filter pred xs
```

（**注意！** 将参数命名为 `_pred` 是一种告诉代码读者该参数未使用的方法。也可以只是 `_`。）

### 3.8.5 尾递归和列表

当递归函数的计算结果是对具有不同参数的同一函数的新调用时，它被称为“尾递归”。 （递归调用被称为位于*尾部位置*。）这是与命令式循环相对应的递归类型。我们已经看到了许多尾递归函数的示例，但我们还没有真正比较编写同一函数的两种方法。这是本讲座前面的 `sumNumbers`：

```haskell
-- Not tail recursive!
sumNumbers :: [Int] -> Int
sumNumbers [] = 0
sumNumbers (x:xs) = x + sumNumbers xs
```

在第二个方程中，函数 `+` 位于顶层，即位于尾部位置。对 `sumNumbers` 的递归调用是 `+` 的参数。这是使用尾递归辅助函数编写的 `sumNumbers`：

```haskell
-- Tail recursive version
sumNumbers :: [Int] -> Int
sumNumbers xs = go 0 xs
  where go sum [] = sum
        go sum (x:xs) = go (sum+x) xs
```

请注意 `go` 的第二个方程：它在顶层（即尾部位置）递归调用 `go`。`+` 现在处于 `go` 的争论中。

对于像 `sumNumbers` 这样产生单个值（数字）的函数，选择哪种形式的递归并不重要。非尾递归函数更容易阅读，而尾递归函数更容易想出。你可以尝试用两种方式编写函数。尾递归形式可能更有效，但这取决于许多细节。我们将在本课程的第二部分中更多地讨论 Haskell 性能。

但是，当你返回列表时，这两种形式之间存在很大差异。考虑之前的函数 `doubleList`。又是这样，首先直接实现，然后通过尾递归辅助函数实现。

```haskell
-- Not tail recursive!
doubleList :: [Int] -> [Int]
doubleList [] = []
doubleList (x:xs) = 2*x : doubleList xs
```

```haskell
-- Tail recursive version
doubleList :: [Int] -> [Int]
doubleList xs = go [] xs
    where go result [] = result
          go result (x:xs) = go (result++[2*x]) xs
```

在这里，直接版本效率更高。`(:)` 运算符以恒定时间工作，而 `(++)` 运算符需要遍历整个列表，需要线性时间。因此，直接版本使用相对于列表长度的线性时间 (*O(n)*)，而尾递归版本是二次的 (*O(n²)*)！

人们可能会想通过在尾递归版本中使用 `(:)` 来解决此问题，但随后列表将以相反的顺序生成。这可以通过应用 `reverse` 来解决，但这会使生成的函数变得相当复杂。

更喜欢直接版本还有另一个原因：懒惰。我们将在课程的第 2 部分中回到惰性，但现在你知道**生成列表的直接方法更简单、更高效且更惯用**就足够了。你应该尝试在练习中练习它。查看上面 `map` 和 `filter` 的标准库实现，即使它们直接生成列表而没有尾递归！

## 3.9 趣味内容：列表推导式

Haskell 有*列表推导式*，这是一种很好的列表定义语法，结合了 `map` 和 `filter` 的功能。你可能已经熟悉 Python 的列表推导式。Haskell 的工作方式几乎相同，但语法略有不同。

映射：

```haskell
[2*i | i<-[1,2,3]]
  ==> [2,4,6]
```

过滤：

```haskell
[i | i <- [1..7], even i]
  ==> [2,4,6]
```

一般来说，这两种形式是等价的：

```haskell
[f x | x <- lis, p x]
map f (filter p lis)
```

列表推导式可以做更多的事情。你可以迭代多个列表：

```haskell
[ first ++ " " ++ last | first <- ["John", "Mary"], last <- ["Smith","Cooper"] ]
  ==> ["John Smith","John Cooper","Mary Smith","Mary Cooper"]
```

你可以进行局部定义：

```haskell
[ reversed | word <- ["this","is","a","string"], let reversed = reverse word ]
  ==> ["siht","si","a","gnirts"]
```

你甚至可以在列表推导中进行模式匹配！

```haskell
firstLetters string = [ char | (char:_) <- words string ]
```

```haskell
firstLetters "Hello World!"
  ==> "HW"
```

## 3.10 趣味内容：自定义运算符

在 Haskell 中，*operator* 是由字符 `!#$%&*+./<=>?@\^|-~` 构建的任何东西。运算符可以像函数一样定义（注意类型标注略有不同）：

```haskell
(<+>) :: [Int] -> [Int] -> [Int]
xs <+> ys = zipWith (+) xs ys
```

```haskell
(+++) :: String -> String -> String
a +++ b = a ++ " " ++ b
```

## 3.11 实用内容：类型孔

有时，在编写 Haskell 时，找到具有正确类型的表达式可能很棘手。幸运的是，编译器可以帮忙。名为“Typed Holes”的功能允许你在代码中留下一个孔，编译器会告诉你这个位置需要什么类型的表达式。

类型孔可以写成 `_` 或 `_name`。它们可能与“匹配任意值”的模式 `_` 混淆，但区别在于：孔出现在 `=` 的“右侧”，而模式出现在 `=` 的“左侧”。

让我们从 GHCi 中的一个简单示例开始：

```haskell
Prelude> filter _hole [True,False]

<interactive>: error:
    • Found hole: _hole :: Bool -> Bool
      Or perhaps ‘_hole’ is mis-spelled, or not in scope
    • In the first argument of ‘filter’, namely ‘_hole’
      In the expression: filter _hole [True, False]
      In an equation for ‘it’: it = filter _hole [True, False]
    • Relevant bindings include
        it :: [Bool] (bound at <interactive>:5:1)
      Valid hole fits include
        not :: Bool -> Bool
          (imported from ‘Prelude’
           (and originally defined in ‘ghc-prim-0.6.1:GHC.Classes’))
        id :: forall a. a -> a
          with id @Bool
          (imported from ‘Prelude’ (and originally defined in ‘GHC.Base’))
```

此消息的重要部分是第一行。这告诉你 Haskell 期望的洞是什么类型。

```text
<interactive>: error:
    • Found hole: _hole :: Bool -> Bool
```

错误消息的其余部分提供了有关 `_hole` 值的一些建议，例如 `id` 和 `not`。

让我们看一个更长的示例，其中我们尝试实现一个使用布尔值列表过滤列表的函数：

```haskell
keepElements [5,6,7,8] [True,False,True,False] ==> [5,7]
```

我们将从 `zip` 开始，因为我们知道这可以很好地配对两个列表的元素。我们添加一个类型化孔 `_doIt` 并用 `zip` 的结果调用它，看看接下来需要做什么。

```haskell
keepElements :: [a] -> [Bool] -> [a]
keepElements xs bs = _doIt (zip xs bs)
```

```haskell
<interactive>: error:
    • Found hole: _doIt :: [(a, Bool)] -> [a]
    ...
```

这看起来可以用 `map` 来完成。让我们看看会发生什么：

```haskell
keepElements :: [a] -> [Bool] -> [a]
keepElements xs bs = map _f (zip xs bs)
```

```haskell
<interactive>: error:
    • Found hole: _f :: (a, Bool) -> a
    ...
      Valid hole fits include
        fst :: forall a b. (a, b) -> a
```

很好！GHC 提醒我们可以使用函数 `fst`，它会从一对值中取出第一个。我们现在完成了吗？

```haskell
keepElements :: [a] -> [Bool] -> [a]
keepElements xs bs = map fst (zip xs bs)
```

```haskell
Prelude> keepElements [5,6,7,8] [True,False,True,False]
[5,6,7,8]
```

哦，对了，我们忘记做过滤部分了。让我们再次尝试输入孔：

```haskell
keepElements :: [a] -> [Bool] -> [a]
keepElements xs bs = map fst (filter _predicate (zip xs bs))
```

```haskell
<interactive>: error:
    • Found hole: _predicate :: (a, Bool) -> Bool
    ...
      Valid hole fits include
        snd :: forall a b. (a, b) -> b
        ...
        ... lots of other suggestions
```

GHC 再次提醒我们一个似乎做正确事情的函数：只需从元组中获取第二个元素。现在我们的函数已经完成并按预期工作。

```haskell
keepElements :: [a] -> [Bool] -> [a]
keepElements xs bs = map fst (filter snd (zip xs bs))
```

```haskell
Prelude> keepElements [5,6,7,8] [True,False,True,False]
[5,7]
```

**当你在练习时遇到类型错误时，请记住键入的孔**！尝试用类型化的洞替换函数或变量。它可能会帮助你弄清楚你需要什么。

## 3.12 测验

这个函数的类型是什么？`both p q x = p x && q x`

1.  `a -> Bool -> a -> Bool -> a -> Bool`
2.  `(a -> Bool) -> (a -> Bool) -> a -> Bool`
3.  `(a -> Bool) -> (b -> Bool) -> c -> Bool`

这个函数的（最通用的）类型是什么？`applyInOut f g x = f (g (f x))`

1.  `(a -> b) -> (b -> a) -> a -> b`
2.  `(a -> b) -> (b -> c) -> a -> c`
3.  `(a -> a) -> (a -> a) -> a -> a`

以下哪个函数将其第一个参数添加到第二个参数？

1.  `f x x = x + x`
2.  `f x = \y -> x + y`
3.  `f = \x y -> x + x`

以下哪一项函数不满足 `f 1 ==> 1`？

1.  `f x = (\y -> y) x`
2.  `f x = \y -> y`
3.  `f x = (\y -> x) x`

下列哪一项函数的输入正确？

1.  `f x y = not x; f :: (Bool -> Bool) -> Bool`
2.  `f x = x ++ "a"; f :: Char -> String`
3.  `f x = 'a' : x; f :: String -> String`

`drop 2` 有多少个参数？

1.  零
2.  一
3.  二

这个函数有什么作用？`f (_:x:_) = x`

1.  返回列表的第一个元素
2.  返回列表的任意元素
3.  返回列表中除第一个和最后一个元素之外的所有元素
4.  返回列表的第二个元素

`reverse $ take 5 . tail $ "This is a test"` 的结果是什么？

1.  `"i sih"`
2.  `"set a"`
3.  类型错误

如果是 `f :: a -> b`，那么 `map (.f)` 的类型是什么？

1.  `[b -> c] -> [a -> c]`
2.  `[c -> a] -> [c -> b]`
3.  `(b -> c) -> [a -> c]`
4.  `[a] -> [b]`

`id id` 中最左边的 `id` 的类型是什么？

1.  未指定
2.  `a`
3.  `a -> a`
4.  `(a -> a) -> (a -> a)`

`const const` 是什么类型？

1.  未指定
2.  `(c -> a -> b) -> a`
3.  `c -> (a -> b -> a)`
4.  `a -> b -> c -> a`

## 3.13 练习

- [Set3a](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set3a.hs)：列表练习
- [Set3b](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set3b.hs)：列表递归练习

### 3.13.1 常见错误

```haskell
No instance for (Eq a) arising from a use of ‘==’
```

你可能尝试过使用 `x==Nothing` 来检查值是否为 `Nothing`。请改用模式匹配。出现此错误的原因是无法比较 `Maybe a` 类型的值，因为 Haskell 不知道如何比较多态类型 `a` 的值。你将在下一讲中找到更多相关内容。现在使用模式匹配而不是 `==`。
