# Haskell 慕课，第 2 部分

- [16 第 16 讲：零碎内容](#lecture-16-odds-and-ends)
  - [16.1 使用 QuickCheck 测试](#testing-with-quickcheck)
  - [16.2 幻影类型](#phantom-types)
  - [16.3 同时性](#simultaneity)
  - [16.4 练习](#exercises-7)
  - [16.5 接下来去哪里？](#where-to-go-from-here)
  - [16.6 致谢](#acknowledgements)


<a id="lecture-16-odds-and-ends"></a>

# 16 第 16 讲：零碎内容

最后一讲将讨论一些其他地方不适合的小主题。你已经完成了课程的所有困难部分。现在是时候坐下来，放松一下，享受一些很酷的 Haskell 了！


<a id="testing-with-quickcheck"></a>

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


<a id="phantom-types"></a>

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


<a id="simultaneity"></a>

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


<a id="exercises-7"></a>

## 16.4 练习

- [Set16a](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set16a.hs)：快速检查
- [Set16b](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set16b.hs): 幻像类型
- 没有并行或并发 Haskell 练习，抱歉！


<a id="where-to-go-from-here"></a>

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


<a id="acknowledgements"></a>

## 16.6 致谢

本课程是由 [Nitor](https://nitor.com/en) 完成的，他为这个项目捐赠了 Joel 的大量工作时间。谢谢你！

感谢整个 Haskell Mooc 团队，特别是

- John Lång 寻求有关材料的帮助
- Antti Laaksonen 设置课程并帮助安排
- Topi Talvitie 用于演习检查基础设施

感谢所有耐心等待第二部分并报告材料和练习错误的学生！
