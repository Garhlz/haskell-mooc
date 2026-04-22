# Haskell 慕课，第 1 部分

- [2 第 2 讲：要么作为英雄而死……](#lecture-2-either-you-die-a-hero)
  - [2.1 递归和辅助函数](#recursion-and-helper-functions)
  - [2.2 守卫](#guards)
  - [2.3 列表](#lists)
  - [2.4 关于不变性](#a-word-about-immutability-1)
  - [2.5 关于类型推断和多态性](#a-word-about-type-inference-and-polymorphism)
  - [2.6 `Maybe` 类型](#the-maybe-type)
  - [2.7 附注：构造函数](#sidenote-constructors)
  - [2.8 `Either` 类型](#the-either-type)
  - [2.9 表达式的情况](#the-case-of-expression)
  - [2.10 回顾：模式匹配](#recap-pattern-matching)
  - [2.11 测验](#quiz-1)
  - [2.12 练习](#exercises-1)


<a id="lecture-2-either-you-die-a-hero"></a>

# 2 第 2 讲：要么作为英雄而死……

- 有关递归的更多信息
- 守卫
- 更多类型：列表、 `Maybe`、`Either`
- 多态性


<a id="recursion-and-helper-functions"></a>

## 2.1 递归和辅助函数

通常，你会发现递归中需要辅助变量来跟踪事物。你可以通过定义带有更多参数的辅助函数来获取它们。类比：辅助函数的参数是你在循环中更新的变量。

下面是如何将循环（Java 或 Python 中的）转换为 Haskell 中的递归辅助函数的示例。

Java：

``` java
public String repeatString(int n, String str) {
    String result = "";
    while (n>0) {
        result = result+str;
        n = n-1;
    }
    return result;
}
```

Python：

``` python
def repeatString(n, str):
    result = ""
    while n>0:
        result = result+str
        n = n-1
    return result
```

Haskell：

``` haskell
repeatString n str = repeatHelper n str ""

repeatHelper n str result = if (n==0)
                            then result
                            else repeatHelper (n-1) str (result++str)
```

``` haskell
Prelude> repeatString 3 "ABC"
"ABCABCABC"
```

你可能已经注意到，Java 和 Python 实现看起来有点奇怪，因为它们使用 while 循环而不是 for 循环。这是因为这种方式到 Haskell 的转换更加简单。

通过使用模式匹配而不是 `if` 可以使这变得更整洁：

``` haskell
repeatString n str = repeatHelper n str ""

repeatHelper 0 _   result = result
repeatHelper n str result = repeatHelper (n-1) str (result++str)
```

这是另一个具有更多变量的示例：有效计算斐波那契数。

Java：

``` java
public int fibonacci(int n) {
    int a = 0;
    int b = 1;
    while (n>1) {
        int c = a+b;
        a=b;
        b=c;
        n--;
    }
    return b;
}
```

Python：

``` python
def fibonacci(n):
    a = 0
    b = 1
    while n>1:
        c = a+b
        a = b
        b = c
        n = n-1
    return b
```

Haskell：

``` haskell
-- fibonacci numbers, fast version
fibonacci :: Integer -> Integer
fibonacci n = fibonacci' 0 1 n

fibonacci' :: Integer -> Integer -> Integer -> Integer
fibonacci' a b 1 = b
fibonacci' a b n = fibonacci' b (a+b) (n-1)
```

花一些时间研究这些并注意 Haskell 递归如何与循环具有相同的格式。

附注：Haskell 程序经常使用撇号来命名辅助函数和函数的替代版本。因此，上面的辅助函数的名称为 `fibonacci'`。像 `foo'` 这样的名字通常读作 *foo prime* （就像数学中一样）。

我之前说过这个版本的斐波那契更有效。你能明白为什么吗？答案是递归调用更少。表达式 `fibonacci' _ _ n` 调用 `fibonacci' _ _ (n-1)` 一次，这表示我们可以在 `n` 步中计算 `fibonacci' _ _ n`。

这种函数直接使用不同参数调用自身的递归类型称为“尾递归”。正如你在上面看到的，尾递归对应于循环。这就是尾递归通常很快的原因：编译器在看到尾递归时可以在机器代码中生成循环。


<a id="guards"></a>

## 2.2 守卫

在我们继续讨论新类型之前，让我们再回顾一下 Haskell 语法。

`if then else` 通常有点麻烦，尤其是当你有多个案例时。一个更简单的替代方案是 Haskell 的“条件定义”或“受保护的定义”。这有点像模式匹配，因为你有多个方程，但你可以使用任意代码来决定使用哪个方程。受保护的定义如下所示：

``` haskell
f x y z
  | condition1 = something
  | condition2 = other
  | otherwise  = somethingother
```

条件可以是 `Bool` 类型的任何表达式。选择计算结果为 `True` 的第一个条件。单词 `otherwise` 只是 `True` 的别名。它用于标记默认情况。

### 2.2.1 示例

以下是使用守卫的一些示例。首先，我们有一个描述给定数字的函数。请注意， `"Two"` 案例位于 `"Even"` 案例之前是多么重要。

``` haskell
describe :: Int -> String
describe n
  | n==2      = "Two"
  | even n    = "Even"
  | n==3      = "Three"
  | n>100     = "Big!!"
  | otherwise = "The number "++show n
```

这是阶乘，用守卫而不是模式匹配来实现。与模式匹配版本不同，这个版本不会在负输入的情况下永远循环。

``` haskell
factorial n
  | n<0       = -1
  | n==0      = 1
  | otherwise = n * factorial (n-1)
```

你甚至可以将防护与模式匹配结合起来。这是一个简单的年龄猜测游戏的实现：

``` haskell
guessAge :: String -> Int -> String
guessAge "Griselda" age
    | age < 47 = "Too low!"
    | age > 47 = "Too high!"
    | otherwise = "Correct!"
guessAge "Hansel" age
    | age < 12 = "Too low!"
    | age > 12 = "Too high!"
    | otherwise = "Correct!"
guessAge name age = "Wrong name!"
```

``` haskell
Prelude> guessAge "Griselda" 30
"Too low!"
Prelude> guessAge "Griselda" 60
"Too high!"
Prelude> guessAge "Griselda" 47
"Correct!"
Prelude> guessAge "Bob" 30
"Wrong name!"
Prelude> guessAge "Hansel" 10
"Too low!"
```


<a id="lists"></a>

## 2.3 列表

到目前为止，我们一直使用数字或布尔值等单一值。字符串包含多个字符，但在某种意义上字符串仍然只是一条信息。为了能够进行实际编程，我们需要处理可变数量的项目。为此，我们需要*数据结构*。

Haskell 中的基本数据结构是列表。列表用于存储相同类型的多个值（换句话说，Haskell 列表是同质的）。这是列表文字的样子：

``` haskell
[0,3,4,1+1]
```

列表类型写为 `[Element]`，其中 `Element` 是列表元素的类型。以下是更多列表表达式及其类型：

``` haskell
[True,True,False] :: [Bool]
["Moi","Hei"] :: [String]
[] :: [a]                   -- more about this later
[[1,2],[3,4]] :: [[Int]]    -- a list of lists
[1..7] :: [Int]             -- range syntax, value [1,2,3,4,5,6,7]
```

Haskell 列表被实现为单链表。我们稍后再讨论这一点。

### 2.3.1 列表操作

Haskell 标准库附带了许多对列表进行操作的函数。以下是一些最重要的及其类型。我们稍后会回到 `[a]` 的实际含义，但现在你可以想象它的意思是“任何列表”。

``` haskell
head :: [a] -> a            -- returns the first element
last :: [a] -> a            -- returns the last element
tail :: [a] -> [a]          -- returns everything except the first element
init :: [a] -> [a]          -- returns everything except the last element
take :: Int -> [a] -> [a]   -- returns the n first elements
drop :: Int -> [a] -> [a]   -- returns everything except the n first elements
(++) :: [a] -> [a] -> [a]   -- lists are catenated with the ++ operator
(!!) :: [a] -> Int -> a     -- lists are indexed with the !! operator
reverse :: [a] -> [a]       -- reverse a list
null :: [a] -> Bool         -- is this list empty?
length :: [a] -> Int        -- the length of a list
```

附注：最后两个操作（`null` 和 `length`）实际上有更多通用类型，但在这里我假装你只能在列表上使用它们。

列表可以与熟悉的 `==` 运算符进行比较。

你还记得我们第一次 GHCi 会议上的内容吗？

``` haskell
Prelude> :t "asdf"
"asdf" :: [Char]
```

这表示 `String` 只是 `[Char]` 的别名，这表示 string 是字符列表。这表示你可以对字符串使用所有列表操作！

一些列表操作来自模块 `Data.List`。你可以使用 `import Data.List` 语法在代码或 GHCi 中导入模块。一个例子是 `sort` 函数，它对列表进行排序：

``` haskell
Prelude> import Data.List
Prelude Data.List> sort [1,0,5,3]
[0,1,3,5]
```

请注意导入的模块集如何显示在 GHCi 提示符中。

### 2.3.2 示例

以下是一些使用列表的示例。在本例中，我只是使用 `==>` 来显示表达式的计算结果，而不是向你显示 GHCi 的输出。

索引列表：

``` haskell
[7,10,4,5] !! 2
  ==> 4
```

使用 `take` 和 `drop` 定义一个丢弃列表的第三个和第四个元素的函数：

``` haskell
f xs = take 2 xs ++ drop 4 xs
```

``` haskell
f [1,2,3,4,5,6]  ==>  [1,2,5,6]
f [1,2,3]        ==>  [1,2]
```

通过获取第一个元素并将其移动到末尾来旋转列表：

``` haskell
g xs = tail xs ++ [head xs]
```

``` haskell
g [1,2,3]      ==>  [2,3,1]
g (g [1,2,3])  ==>  [3,1,2]
```

这是范围语法的示例：

``` haskell
reverse [1..4] ==> [4,3,2,1]
```


<a id="a-word-about-immutability-1"></a>

## 2.4 关于不变性

因为 Haskell 是纯的，这也意味着函数不能*修改*（改变）它们的输入。变更是一种副作用，Haskell 函数只允许通过其返回值进行输出。这表示 Haskell 列表函数总是返回一个新列表。在实践中：

``` haskell
Prelude> list = [1,2,3,4]
Prelude> reverse list
[4,3,2,1]
Prelude> list
[1,2,3,4]
Prelude> drop 2 list
[3,4]
Prelude> list
[1,2,3,4]
```

这可能看起来效率很低，但事实证明它既高性能又非常有用。我们将在后面的讲座中回顾 Haskell 数据结构的工作原理。


<a id="a-word-about-type-inference-and-polymorphism"></a>

## 2.5 关于类型推断和多态性

那么像 `head :: [a] -> a` 这样的类型是什么意思呢？这表示给定一个包含任何类型 `a` 元素的列表，返回值将具有相同类型 `a`。

在这种类型中， `a` 是一个*类型变量*。类型变量是以小写字母开头的类型，例如 `a`、`b`、`thisIsATypeVariable`。类型变量意味着未知的类型，或者换句话说，可以是任何类型的类型。类型变量可以通过*类型推断*（也称为*统一*）过程变成*具体类型*（例如 `Bool`）。

让我们看一些例子。如果我们将 `head` 应用于布尔值列表，类型推断会将头部参数的类型 `[a]` 与实际参数的类型 `[Bool]` 进行比较，并推断出 `a` 必须是 `Bool`。这表示在这种情况下 `head` 的返回类型也将是 `Bool`。

``` haskell
head :: [a] -> a
head [True,False] :: Bool
```

函数 `tail` 接受一个列表，并返回一个相同类型的列表。如果我们将 `tail` 应用于布尔值列表，则返回值也将是布尔值列表。

``` haskell
tail :: [a] -> [a]
tail [True,False] :: [Bool]
```

如果类型不匹配，我们会收到类型错误。考虑运算符 `++` 它接受两个相同类型的列表，正如我们从其类型 `[a] -> [a] -> [a]` 中看到的那样。如果我们尝试将 `++` 应用于布尔值列表和字符列表，我们会收到错误。这是 GHCi 中发生的情况：

    Prelude> [True,False] ++ "Moi"

    <interactive>:1:16:
        Couldn't match expected type `Bool' against inferred type `Char'
          Expected type: [Bool]
          Inferred type: [Char]
        In the second argument of `(++)', namely `"Moi"'
        In the expression: [True, False] ++ "Moi"

类型推断真的很强大。它使用简单的统一过程来为我们获取几乎任何 Haskell 表达式的类型。考虑这两个函数：

``` haskell
f xs ys = [head xs, head ys]
g zs = f "Moi" zs
```

我们可以向 GHCi 询问它们的类型，我们将看到类型推断已经发现 `f` 的两个参数必须具有相同的类型，因为它们的头被放入同一个列表中。

``` haskell
Prelude> :t f
f :: [a] -> [a] -> [a]
```

函数 `g` 将 `f` 的参数之一固定为字符串（即 `[Char]`），该函数获得更窄的类型。类型推断决定 `g` 的参数 `zs` 也必须具有类型 `[Char]`，否则 `f` 的类型将与对 `f` 的调用不匹配。

``` haskell
Prelude> :t g
g :: [Char] -> [Char]
```

### 2.5.1 附注：一些术语

在像 `[Char]` 这样的类型中，我们将 `Char` 称为*类型参数*。像列表类型这样需要类型参数的类型称为“参数化类型”。

像 `head` 这样的函数可以与许多不同类型的参数一起使用，这一事实被称为“多态性”。`head` 函数被认为是*多态*。多态性有多种形式，这种使用类型变量的 Haskell 形式称为“参数多态性”。

### 2.5.2 附注：类型标注

由于 Haskell 具有类型推断，因此你不需要给出任何类型标注。然而，即使类型标注不是必需的，添加它们的原因有多种：

1.  它们充当文档
2.  它们充当编译器检查的断言：帮助你发现错误
3.  你可以使用类型标注为函数提供比 Haskell 推断更窄的类型

一个好的经验法则是给出顶级定义类型标注。


<a id="the-maybe-type"></a>

## 2.6 `Maybe` 类型

除了列表类型之外，Haskell 还有其他参数化类型。让我们看一个非常常见且有用的类型：`Maybe` 类型。

有时，操作没有有效的返回值（例如除以零）。在这种情况下我们有几个选择。我们可以使用错误值，例如 `-1`。这有点难看，并不总是可能的。我们可以抛出异常。这是不纯洁的。在其他一些语言中，我们将返回（几乎）所有类型中都存在的特殊空值。然而 Haskell 没有 null。

Haskell 为我们提供的解决方案是将返回类型更改为 `Maybe` 类型。这是纯净、安全、整洁的。类型 `Maybe a` 有两个*构造函数*：`Nothing` 和 `Just`。`Nothing` 只是一个常量，但 `Just` 带有一个参数。更具体地说：

| 类型 | 值 |
|:--------------|:-----------------------------------------|
| `Maybe Bool` | `Nothing`, `Just False`, `Just True` |
| `Maybe Int` | `Nothing`, `Just 0`, `Just 1`, … |
| `Maybe [Int]` | `Nothing`, `Just []`, `Just [1,1337]`, … |

你可以将 `Maybe a` 视为有点像 `[a]`，只不过只能有 0 或 1 个元素，而不能更多。或者，你可以考虑 `Maybe a` 向类型 `a` 引入 null 值。如果你熟悉 Java， `Maybe Integer` 是 Java 的 `Optional<Integer>` 的 Haskell 等价物。

你可以通过指定 `Nothing` 或 `Just someOtherValue` 来创建 `Maybe` 值：

``` haskell
Prelude> :t Nothing
Nothing :: Maybe a
Prelude> Just "a camel"
Just "a camel"
Prelude> :t Just "a camel"
Just "a camel" :: Maybe [Char]   -- the same as Maybe String
Prelude> Just True
Just True
Prelude> :t Just True
Just True :: Maybe Bool
```

``` haskell
-- given a password, return (Just username) if login succeeds, Nothing otherwise
login :: String -> Maybe String
login "f4bulous!" = Just "unicorn73"
login "swordfish" = Just "megahacker"
login _           = Nothing
```

你可以通过模式匹配来使用 `Maybe` 值。通常你为 `Nothing` 和 `Just something` 情况定义模式。一些例子：

``` haskell
-- Multiply an Int with a Maybe Int. Nothing is treated as no multiplication at all.
perhapsMultiply :: Int -> Maybe Int -> Int
perhapsMultiply i Nothing = i
perhapsMultiply i (Just j) = i*j   -- Note how j denotes the value inside the Just
```

``` haskell
Prelude> perhapsMultiply 3 Nothing
3
Prelude> perhapsMultiply 3 (Just 2)
6
```

``` haskell
intOrZero :: Maybe Int -> Int
intOrZero Nothing = 0
intOrZero (Just i) = i

safeHead :: [a] -> Maybe a
safeHead xs = if null xs then Nothing else Just (head xs)

headOrZero :: [Int] -> Int
headOrZero xs = intOrZero (safeHead xs)
```

``` haskell
headOrZero []  ==> intOrZero (safeHead [])  ==> intOrZero Nothing  ==> 0
headOrZero [1] ==> intOrZero (safeHead [1]) ==> intOrZero (Just 1) ==> 1
```


<a id="sidenote-constructors"></a>

## 2.7 附注：构造函数

正如你在上面所看到的，我们可以在 `Maybe` 的构造函数上进行模式匹配：`Just` 和 `Nothing`。稍后我们会回到构造函数的含义。现在只要注意到构造函数是以大写字母开头的特殊值就足够了，你可以对其进行模式匹配。

我们已经见过的其他构造函数包括 `Bool` 的构造函数 -`True` 和 `False`。我们将在下一讲介绍列表类型的构造函数。

构造函数可以像 Haskell 值一样使用。像 `Nothing` 和 `False` 这样不带参数的构造函数只是常量。像 `Just` 这样带有参数的构造函数的行为类似于函数。它们甚至还有函数类型！

    Prelude> :t Just
    Just :: a -> Maybe a


<a id="the-either-type"></a>

## 2.8 `Either` 类型

有时，如果你可以向 `Nothing` 添加错误消息或其他内容，那就太好了。这就是我们拥有 `Either` 类型的原因。`Either` 类型采用两个类型参数。类型 `Either a b` 有两个构造函数：`Left` 和 `Right`。两者都带有一个参数， `Left` 是 `a` 类型的参数， `Right` 是 `b` 类型的参数。

| 类型 | 值 |
|:---|:---|
| `Either Int Bool` | `Left 0`, `Left 1`, `Right False`, `Right True`, … |
| `Either String [Int]` | `Left "asdf"`, `Right [0,1,2]`, … |
| `Either Integer Integer` | `Left 0`, `Right 0`, `Left 1`, `Right 1`, … |

这是一个简单的例子：一个 `readInt` 函数只知道几个数字并返回其余数字的描述性错误。请注意 Haskell 约定，使用 `Left` 表示错误，使用 `Right` 表示成功。

``` haskell
readInt :: String -> Either String Int
readInt "0" = Right 0
readInt "1" = Right 1
readInt s = Left ("Unsupported string: " ++ s)
```

附注：`Either` 的构造函数称为 `Left` 和 `Right`，因为它们引用 `Either` 的左边和右边的类型参数。请注意在 `Either a b` 中， `a` 是左参数， `b` 是右参数。因此 `Left` 包含 `a` 类型的值，同样包含 `b` 类型的 `Right`。通常用 `Right` 表示成功，可能只是因为 right 也有“正确”的意思。这里没有冒犯左撇子的意思。

这是另一个示例：匹配 `Either` 的模式。就像 `Maybe` 一样， `Either` 有两种模式，每个构造函数都有一种模式。

``` haskell
iWantAString :: Either Int String -> String
iWantAString (Right str)   = str
iWantAString (Left number) = show number
```

你还记得，Haskell 列表只能包含相同类型的元素。你不能拥有像 `[1,"foo",2]` 这样的值。但是，你可以使用 `Either` 之类的类型来表示可以包含两种不同类型值的列表。例如，我们可以跟踪讲座的人数，如果缺少值，则可以添加解释：

    lectureParticipants :: [Either String Int]
    lectureParticipants = [Right 10, Right 13, Left "easter vacation", Right 17, Left "lecturer was sick", Right 3]


<a id="the-case-of-expression"></a>

## 2.9 表达式的情况

我们已经在函数参数中看到了模式匹配，但还有一种在表达式中进行模式匹配的方法。它看起来像这样：

``` haskell
case <value> of <pattern> -> <expression>
                <pattern> -> <expression>
```

作为示例，让我们使用 `case` 重写第一讲中的 `describe` 示例：

``` haskell
describe :: Integer -> String
describe 0 = "zero"
describe 1 = "one"
describe 2 = "an even prime"
describe n = "the number " ++ show n
```

``` haskell
describe :: Integer -> String
describe n = case n of 0 -> "zero"
                       1 -> "one"
                       2 -> "an even prime"
                       n -> "the number " ++ show n
```

一个更有趣的例子是，当我们进行模式匹配的值不是函数参数时。例如：

``` haskell
-- parse country code into country name, returns Nothing if code not recognized
parseCountry :: String -> Maybe String
parseCountry "FI" = Just "Finland"
parseCountry "SE" = Just "Sweden"
parseCountry _ = Nothing

flyTo :: String -> String
flyTo countryCode = case parseCountry countryCode of Just country -> "You're flying to " ++ country
                                                     Nothing -> "You're not flying anywhere"
```

``` haskell
Prelude> flyTo "FI"
"You're flying to Finland"
Prelude> flyTo "DE"
"You're not flying anywhere"
```

我们可以使用模式匹配的辅助函数来编写 `flyTo` 函数，而不是使用 case-of 表达式：

``` haskell
flyTo :: String -> String
flyTo countryCode = handleResult (parseCountry countryCode)
  where handleResult (Just country) = "You're flying to " ++ country
        handleResult Nothing        = "You're not flying anywhere"
```

事实上，case-of 表达式总是可以用辅助函数替换。这是另外一个例子，用两种方式编写：

``` haskell
-- given a sentence, decide whether it is a statement, question or exclamation
sentenceType :: String -> String
sentenceType sentence = case last sentence of '.' -> "statement"
                                              '?' -> "question"
                                              '!' -> "exclamation"
                                              _   -> "not a sentence"
```

``` haskell
-- same function, helper function instead of case-of
sentenceType sentence = classify (last sentence)
  where classify '.' = "statement"
        classify '?' = "question"
        classify '!' = "exclamation"
        classify _   = "not a sentence"
```

``` haskell
Prelude> sentenceType "This is Haskell."
"statement"
Prelude> sentenceType "This is Haskell!"
"exclamation"
```

### 2.9.1 何时使用 Case 表达式

你可能会问，使用另一种模式匹配语法有什么意义。嗯， `case` 表达式比我们接下来讨论的方程有一些优点。

首先，也许最重要的是， `case` 表达式使我们能够针对函数输出进行模式匹配。我们可能想给工作（懒惰）的 Haskellers 写一大早的励志信息：

``` haskell
motivate :: String -> String
motivate "Monday"    = "Have a nice week at work!"
motivate "Tuesday"   = "You're one day closer to weekend!"
motivate "Wednesday" = "3 more day(s) until the weekend!"
motivate "Thursday"  = "2 more day(s) until the weekend!"
motivate "Friday"    = "1 more day(s) until the weekend!"
motivate _           = "Relax! You don't need to work today!"
```

使用 `case` 表达式，我们可以针对参数运行辅助函数并对结果进行模式匹配：

``` haskell
motivate :: String -> String
motivate day = case distanceToSunday day of
  6 -> "Have a nice week at work!"
  5 -> "You're one day closer to weekend!"
  n -> if n > 1
       then show (n - 1) ++ " more day(s) until the weekend!"
       else "Relax! You don't need to work today!"
```

顺便说一下，还有第三种方法，守卫：

``` haskell
motivate :: String -> String
motivate day
  | n == 6 = "Have a nice week at work!"
  | n == 5 = "You're one day closer to weekend!"
  | n > 1 = show (n - 1) ++ " more day(s) until the weekend!"
  | otherwise = "Relax! You don't need to work today!"
  where n = distanceToSunday day
```

稍后我们将看到如何使用方程和 `case` 表达式来定义 `distanceToSunday`。

其次，如果需要在许多模式之间共享辅助函数，那么方程就不起作用。例如：

``` haskell
area :: String -> Double -> Double
area "square" x = square x
area "circle" x = pi * square x
  where square x = x * x
```

这不会编译，因为 `where` 子句仅附加到 `"circle"` 情况，因此 `square` 辅助函数在 `"square"` 情况下不可用。另一方面，我们可以写

``` haskell
area :: String -> Double -> Double
area shape x = case shape of
  "square" -> square x
  "circle" -> pi * square x
  where square x = x*x
```

第三，在必须使用方程多次重复（长）函数名称的情况下， `case` 表达式可能有助于编写更简洁的代码。正如我们在上面看到的，我们可能需要一个函数来测量给定日期和星期日之间的距离：

``` haskell
distanceToSunday :: String -> Int
distanceToSunday "Monday"    = 6
distanceToSunday "Tuesday"   = 5
distanceToSunday "Wednesday" = 4
distanceToSunday "Thursday"  = 3
distanceToSunday "Friday"    = 2
distanceToSunday "Saturday"  = 1
distanceToSunday "Sunday"    = 0
```

使用 `case` 表达式可以实现更简洁的实现：

``` haskell
distanceToSunday :: String -> Int
distanceToSunday d = case d of
  "Monday"    -> 6
  "Tuesday"   -> 5
  "Wednesday" -> 4
  "Thursday"  -> 3
  "Friday"    -> 2
  "Saturday"  -> 1
  "Sunday"    -> 0
```

这三个优点使 `case` 表达式成为 Haskeller 工具箱中的多功能工具。值得记住的是 `case` 的工作原理。

（将工作日表示为字符串可能可以完成工作，但这不是完美的解决方案。如果我们将 `motivate` 应用于 `"monday"`（所有字母均为小写）或 `"keskiviikko"`，会发生什么？在第 5 讲中，我们将学习一种更好的方法来表示工作日等事物。）


<a id="recap-pattern-matching"></a>

## 2.10 回顾：模式匹配

可以用作模式的东西：

- `Int` 和 `Integer` 常量，例如 `(-1)`、`0`、`1`、`2` 等
- `Bool` 值 `True` 和 `False`
- `Char` 常量：`'a'`、`'b'`
- `String` 常量：`"abc"`、`""`
- `Maybe` 构造函数：`Nothing`、`(Just x)`
- `Either` 构造函数：`(Left x)`、`(Right y)`
- 特殊的 `_` 图案，意思是“任何事，我不在乎”
- 这些模式的组合，例如 `(Just 1)`
- 我们将在接下来的讲座中了解其他模式，例如列表。

可以使用模式的地方：

- 用方程定义函数：

``` haskell
f :: Bool -> Maybe Int -> Int
f False Nothing  = 1
f False _        = 2
f True  (Just i) = i
f True  Nothing  = 0
```

- 在 `case of` 表达式中：

``` haskell
case number of 0 -> "zero"
               1 -> "one"
               _ -> "not zero or one"
```

你真正“需要”模式匹配的唯一事情是在 `Just`、`Left` 或 `Right` 构造函数中“获取值”。这里还有两个例子：

``` haskell
-- getElement (Just i) gets the ith element (counting from zero) of a list, getElement Nothing gets the last element
getElement :: Maybe Int -> [a] -> a
getElement (Just i) xs = xs !! i
getElement Nothing xs = last xs
```

``` haskell
Prelude> getElement Nothing "hurray!"
'!'
Prelude> getElement (Just 3) [5,6,7,8,9]
8
```

``` haskell
direction :: Either Int Int -> String
direction (Left i) = "you should go left " ++ show i ++ " meters!"
direction (Right i) = "you should go right " ++ show i ++ " meters!"
```

``` haskell
Prelude> direction (Left 3)
"you should go left 3 meters!"
Prelude> direction (Right 5)
"you should go right 5 meters!"
```

模式匹配的其他用途（到目前为止我们已经看到了！）也可以使用 `==` 运算符来完成。然而，像 `x==Nothing` 这样的东西并不适用于所有情况。当我们在第 4 课讨论类型类时，我们会找到原因。


<a id="quiz-1"></a>

## 2.11 测验

`f x = [x,x]` 返回多少个值？

1.  零
2.  一
3.  二

为什么表达式 `Nothing 1` 会导致类型错误？

1.  因为 `Nothing` 不带参数
2.  因为 `Nothing` 什么也没返回
3.  因为 `Nothing` 是一个构造函数

函数 `f x y = if x && y then Right x else Left "foo"` 的类型是什么？

1.  `Bool -> Bool -> Either Bool String`
2.  `String -> String -> Either String String`
3.  `Bool -> Bool -> Either String Bool`

以下哪个函数的类型可能是 `Bool -> Int -> [Bool]`

1.  `f x y = [0, y]`
2.  `f x y = [x, True]`
3.  `f x y = [y, True]`

这个函数的类型是什么？`justBoth a b = [Just a, Just b]`

1.  `a -> b -> [Maybe a, Maybe b]`
2.  `a -> a -> [Just a]`
3.  `a -> b -> [Maybe a]`
4.  `a -> a -> [Maybe a]`


<a id="exercises-1"></a>

## 2.12 练习

- [Set2a](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set2a.hs)
- [Set2b](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set2b.hs)
