# Haskell 慕课，第 1 部分

- [8 第 8 讲：回味](#lecture-8-the-aftertaste)
  - [8.1 IO 初体验](#a-taste-of-io)
  - [8.2 总结](#summary)
  - [8.3 接下来做什么？](#what-next)
  - [8.4 最终项目：图形](#final-project-graphics)
  - [8.5 致谢](#acknowledgements)


<a id="lecture-8-the-aftertaste"></a>

# 8 第 8 讲：回味


<a id="a-taste-of-io"></a>

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


<a id="summary"></a>

## 8.2 总结

到目前为止，我们已经了解了 Haskell 的语法和类型、相当多的函数式编程以及一些语言特性，例如类型类。

我们还看到了一些面向类型的编程，甚至体验了 Haskell 中的 I/O。

现在你已经知道如何用 Haskell 编写真正的计算机程序，但仍有很多东西需要学习。


<a id="what-next"></a>

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


<a id="final-project-graphics"></a>

## 8.4 最终项目：图形

打开练习文件 [`Set8.hs`](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set8.hs) 并按照其中的说明进行操作。玩得开心！


<a id="acknowledgements"></a>

## 8.5 致谢

这门课程是由 [Nitor](https://nitor.com/en) 完成的，他为这个项目捐赠了 Joel 的大量工作时间。谢谢你！如果你有兴趣在重视持续学习的地方工作，请查看我们的 [open positions](https://www.nitor.com/en/jobs)。

感谢整个 Haskell Mooc 团队，特别是

- John Lång 帮助完善课程材料
- Antti Laaksonen 设置课程并帮助安排
