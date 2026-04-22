# 第 1 讲：……就这样开始


## 1.1 课程简介

这是一门使用 Haskell 编程语言学习函数式编程的在线课程。你可以按照自己的进度学习。所有材料和练习都是公开可用的。

本课程面向希望学习函数式编程的初学者，也面向具有函数式编程经验、特别想学习 Haskell 的人。本课程不需要任何基础知识，但提前了解至少一种编程语言将使学习变得更容易。

练习包括了解如何使用命令行以及 Git 版本控制系统的基本用法。

这是一门两部分课程的第一部分。第 1 部分介绍 Haskell 语法和特性的基础知识。你将学习递归、高阶函数、代数数据类型和 Haskell 的一些高级特性。不过，第 1 部分将坚持纯函数式编程，不涉及副作用。I/O 和 Monad（单子）将在第 2 部分中介绍。

本课程共分 8 讲，长度大致相等，但有些讲的材料比其他讲更多。每讲最后都附有 10-30 个围绕该讲主题的小编程练习。


## 1.2 推荐资源

除了本课程材料之外，如果你觉得缺少示例或解释，以下资源可能会很有用。

- [课程页面](https://haskell.mooc.fi)
- 课程的 [Telegram 频道](https://t.me/haskell_mooc_fi)
- 课程的 [GitHub 仓库](https://github.com/moocfi/haskell-mooc) 包含练习和本材料
- 其他资源
  - [A Gentle Introduction to Haskell](https://www.haskell.org/tutorial/) - 一个较旧且较短的教程，但仍然值得阅读
  - [Learn You a Haskell for Great Good!](http://learnyouahaskell.com/chapters) - 一本很好的免费 Haskell 入门材料
  - [The Haskell School of Expression](https://www.cs.yale.edu/homes/hudak/SOE/index.htm) - 稍旧但仍然有价值的函数式编程入门材料
  - [Haskell Programming from First Principles](https://haskellbook.com/) - 59 美元的 Haskell 电子书，节奏慢、篇幅长
  - [libera.chat](https://libera.chat/) 上的 IRC 频道 `#haskell` 是初学者的好地方


## 1.3 Haskell

Haskell 是：

**函数式** – 程序的基本构建块是函数。函数可以返回函数，也可以接受函数作为参数。此外，Haskell 中唯一的循环方式是递归。

**纯** - Haskell 函数是纯的，也就是说，它们没有副作用。副作用是指读取文件、打印文本或更改全局变量等。函数的所有输入都必须位于其参数中，函数的所有输出都必须位于其返回值中。这听起来有限制，但使程序的推理变得更容易，并允许编译器进行更多优化。

**惰性** - 值只有在需要时才会被求值。这使得使用无限数据结构成为可能，并且也使纯程序更加高效。

**强类型** - 每个 Haskell 值和表达式都有一个类型。编译器在编译时检查类型，并保证运行时不会发生类型错误。这意味着不会出现 AttributeErrors（类似于 Python）、ClassCastExceptions（类似于 Java）或分段错误（类似于 C）。Haskell 类型系统非常强大，可以帮助你设计更好的程序。

**类型推断** - 除了检查类型之外，编译器还可以推断大多数程序的类型。这使得使用强类型语言变得更加容易。事实上，大多数 Haskell 函数都可以完全不用类型来编写。然而，程序员仍然可以为函数和值提供类型标注，以便更轻松地发现类型错误。类型标注还使阅读程序变得更加容易。

**垃圾收集** - 与当今大多数高级语言一样，Haskell 通过垃圾收集进行自动内存管理。这意味着程序员不需要担心分配或释放内存，语言运行时会自动处理这些事情。

**编译** - 虽然我们在这门课程中主要通过交互式 GHCi 环境使用 Haskell，但 Haskell 是一种编译语言。Haskell 程序可以编译为非常高效的二进制文件，并且 GHC 编译器非常擅长将函数式代码优化为高性能机器代码。

在本课程中，你将了解这些术语在实践中的含义。如果其中一些现在听起来有些抽象，请不用担心。

另请参见：[Haskell Wiki 的函数式编程页面](https://wiki.haskell.org/Functional_programming)。

### 1.3.1 特点

下面展示了 Haskell 的一些很酷的功能：

**高阶函数** – 函数可以接受函数作为参数：

```haskell
map length ["abc","abcdef"]
```

结果是 `[3,6]`。

**匿名函数又名 lambda** – 你可以定义一次性帮助函数而无需给它们命名

```haskell
filter (\x -> length x > 1) ["abc","d","ef"]
```

结果是 `["abc","ef"]`。

**偏应用** – 你可以通过仅向另一个函数提供它需要的一些参数来定义新函数。例如，把列表中的所有元素乘以 3：

```haskell
map (*3) [1,2,3]
```

**代数数据类型** – 用于定义可以包含多种不同情况的数据类型的语法：

```haskell
data Shape = Point | Rectangle Double Double | Circle Double
```

现在，类型 `Shape` 可以有值 `Point`、`Rectangle 3 6` 和 `Circle 5` 等。

**模式匹配** – 根据与你的数据定义相对应的情况定义函数：

```haskell
area Point = 0
area (Rectangle width height) = width * height
area (Circle radius) = 2 * pi * radius * radius
```

**列表** – 与许多语言不同，Haskell 具有简洁的内置列表语法。可以使用*列表推导式*从其他列表构建列表。下面是一个片段，它从一组名字和姓氏选项中生成偶数长度的名字：

```haskell
[whole | first <- ["Eva", "Mike"],
         last <- ["Smith", "Wood", "Odd"],
         let whole = first ++ last,
         even (length whole)]
```

结果是 `["EvaSmith","EvaOdd","MikeWood"]`。由于 Haskell 的惰性，我们甚至可以创建所谓的*无限列表*：

```haskell
primes = [ n | n <- [2..] , all (\k -> n `mod` k /= 0) [2..n `div` 2] ]
```

然后可以通过下面的表达式取得前十个素数：

```haskell
take 10 primes
```

其求值结果为 `[2,3,5,7,11,13,17,19,23,29]`。

**参数化类型** – 你可以定义由其他类型参数化的类型。例如，`[Int]` 是 `Int` 的列表，而 `[Bool]` 是布尔值的列表。你可以定义适用于各种列表的带类型函数，例如 `reverse` 的类型为 `[a] -> [a]`，这表示它接受包含任意类型 `a` 的列表，并返回相同类型的列表。

**类型类** – 另一种形式的多态：你可以根据参数类型为同一个函数提供不同实现。例如，`Show` 类型类定义了函数 `show`，它可以把各种类型的值转换成字符串。`Num` 类型类定义了像 `+` 这样的算术运算符，它们适用于所有数字类型（`Int`、`Double`、`Complex` 等）。

### 1.3.2 一些历史

Haskell 的简要时间表：

- 20 世纪 30 年代：lambda 演算
- 1950 年代-1970 年代：Lisp、模式匹配、Scheme、ML
- 1978 约翰·巴克斯：[Can programming be liberated from the von Neumann style?](https://dl.acm.org/doi/pdf/10.1145/359576.359579)
- 1987 决定统一纯函数式语言领域
- 1990 Haskell 1.0
- 1991 Haskell 1.1（let 语法、部分）
- 1992 Haskell 1.2，GHC
- 1996 Haskell 1.3（Monad、do 语法、类型系统改进）
- 1999 Haskell 98
- 2000 年代：GHC 开发，对该语言进行了许多扩展
- 2009 [Haskell 2010 标准](https://www.haskell.org/onlinereport/haskell2010/)
- 2010 年：GHC 开发、Haskell 平台、Haskell Stack

“haskel”这个词在希伯来语中的意思是智慧，但 Haskell 编程语言的名称来自逻辑学家 Haskell Curry。Haskell 这个名字来自古挪威语单词 áss（上帝）和 ketill（头盔）。

### 1.3.3 Haskell 的使用

以下是用 Haskell 编写的软件项目的一些示例。

- [Darcs](https://en.wikipedia.org/wiki/Darcs) 分布式版本控制系统
- [Sigma spam-prevention tool at Facebook](https://engineering.fb.com/security/fighting-spam-with-haskell/)
- [PureScript](https://www.purescript.org/) 和 [Elm](https://elm-lang.org/) 编程语言的实现是用 Haskell 编写的
- 用于在不同文档格式之间进行转换的[Pandoc](https://pandoc.org/)工具 - 它也用于生成本课程材料
- 为 PostgreSQL 数据库公开 HTTP REST API 的[PostgREST](https://postgrest.org/)服务器
- 像 [Galois](https://galois.com/) 和 [Well-Typed](https://well-typed.com/) 这样的函数式编程咨询公司在使用 Haskell 为客户开发关键系统方面拥有悠久的历史

请参阅 [Haskell Wiki](https://wiki.haskell.org/Haskell_in_industry) 和[这篇博客文章](https://serokell.io/blog/top-software-written-in-haskell)了解更多！


## 1.4 运行 Haskell

获取 Haskell 的最简单方法是安装 `stack` 工具，请参阅<https://haskellstack.org>。本课程的练习旨在与 Stack 配合使用，因此你现在应该使用它。

顺便说一句，如果你对 Stack 是什么以及它与 Cabal 和 GHC 等其他 Haskell 工具的关系感兴趣，请阅读[这篇说明](https://www.quora.com/What-is-the-difference-between-Cabal-and-Stack-in-Haskell-projects-Which-one-do-you-recommend-and-why)或[这篇 FAQ](https://docs.haskellstack.org/en/stable/faq/)。我们将在课程的第 2 部分中回顾 Haskell 包并详细使用它们。

现在，安装 Stack 后，只需运行 `stack ghci` 即可获得交互式 Haskell 环境。

**注意！** GHC 8.10.7 有一个 GHCi 错误，导致在基于 ARM 的系统上无法编辑行。作为解决方法，请使用 `TERM=dumb stack ghci`。更多信息见[这里](https://gitlab.haskell.org/ghc/ghc/-/issues/20022)。


## 1.5 开始吧！

GHCi 是交互式 Haskell 解释器。这是一个示例会话：

```haskell
$ stack ghci
GHCi, version 9.2.8: https://www.haskell.org/ghc/  :? for help
Prelude> 1+1
2
Prelude> "asdf"
"asdf"
Prelude> reverse "asdf"
"fdsa"
Prelude> :type "asdf"
"asdf" :: [Char]
Prelude> tail "asdf"
"sdf"
Prelude> :type tail "asdf"
tail "asdf" :: [Char]
Prelude> :type tail
tail :: [a] -> [a]
Prelude> :quit
Leaving GHCi.
```

顺便说一下，第一次运行 `stack ghci` 时，它会下载 GHC 和一些库，所以如果你看到一些输出并且需要等待一段时间才能收到 `Prelude>` 提示，请不要担心。

让我们来分析一下这个例子。如果你还不明白，请不用担心，这只是对表达式和类型的初步接触。

```haskell
Prelude> 1+1
2
```

`Prelude>` 是 GHCi 提示符。它表明我们可以使用 Haskell 基础库中名为 Prelude 的函数。我们求值 1 加 1，结果是 2。

```haskell
Prelude> "asdf"
"asdf"
```

这里我们计算一个字符串文字，结果是相同的字符串。

```haskell
Prelude> reverse "asdf"
"fdsa"
```

在这里，我们通过将函数 `reverse` 应用到值 `"asdf"` 来计算字符串的反转。

```haskell
Prelude> :type "asdf"
"asdf" :: [Char]
```

除了计算表达式之外，我们还可以使用 `:type`（缩写为 `:t`）GHCi 命令询问其类型。`"asdf"` 的类型是字符列表。以 `:` 开头的命令是 GHCi 用户界面的一部分，而不是 Haskell 语言的一部分。

```haskell
Prelude> tail "asdf"
"sdf"
Prelude> :t tail "asdf"
tail "asdf" :: [Char]
```

`tail` 函数适用于列表并返回除列表的第一个元素之外的所有元素。在这里我们看到 `tail` 应用于 `"asdf"`。我们还检查表达式的类型，它是一个字符列表，正如预期的那样。

```haskell
Prelude> :t tail
tail :: [a] -> [a]
```

最后，这是 `tail` 函数的类型。它接受任何类型的列表作为参数，并返回相同类型的列表。

```haskell
Prelude> :quit
Leaving GHCi.
```

这就是你退出 GHCi 的方式。


## 1.6 表达式和类型

就像我们在上面的 GHCi 示例中看到的那样，*表达式*和*类型*是 Haskell 的核心基础。事实上，Haskell 程序中的几乎所有值都是表达式。特别是，没有像 Python、Java 或 C 中那样的*语句*。

表达式具有*值*和*类型*。我们编写一个表达式及其类型，如下所示：`expression :: type`。以下是一些示例：

| 表达式 | 类型 | 值 |
|:---------------|:---------|:---------|
| `True` | `Bool` | `True` |
| `not True` | `Bool` | `False` |
| `"as" ++ "df"` | `[Char]` | `"asdf"` |

### 1.6.1 表达式的语法

表达式由*应用于*参数的函数组成。通过将参数放置在函数名称后面来“应用”（即调用）函数——函数调用没有特殊的语法。

| Haskell | Python、Java 或 C |
|:--------|:------------------|
| `f 1` | `f(1)` |
| `f 1 2` | `f(1,2)` |

括号可用于*分组*表达式（就像在数学和其他语言中一样）。

| Haskell | Python、Java 或 C |
|:--------------|:------------------|
| `g h f 1` | `g(h,f,1)` |
| `g h (f 1)` | `g(h,f(1))` |
| `g (h f 1)` | `g(h(f,1))` |
| `g (h (f 1))` | `g(h(f(1)))` |

一些函数名称被制成特殊字符，并用作运算符：在它们的参数之间而不是在它们之前。函数调用比运算符“绑定更紧密”，就像乘法比加法绑定更紧密一样。

| Haskell | Python、Java 或 C |
|:--------------|:------------------|
| `a + b` | `a + b` |
| `f a + g b` | `f(a) + g(b)` |
| `f (a + g b)` | `f(a+g(b))` |

附注。在 Haskell 中，函数应用是*左关联*的，即 `f g x y` 实际上与 `(((f g) x) y)` 相同。我们稍后会回到这个话题。现在，你可以把 `f g x y` 理解为对参数 `g`、`x` 和 `y` 应用函数 `f`。

### 1.6.2 类型语法

以下是一些可以帮助你入门的 Haskell 基本类型。

| 类型 | 文字 | 使用 | 操作 |
|:---|:---|:---|:---|
| `Int` | `1`, `2`, `-3` | 数字类型（有符号，64 位） | `+`, `-`, `*`, `div`, `mod` |
| `Integer` | `1`, `-2`, `900000000000000000` | 无界数字类型 | `+`, `-`, `*`, `div`, `mod` |
| `Double` | `0.1`, `1.2e5` | 浮点数 | `+`, `-`, `*`, `/`, `sqrt` |
| `Bool` | `True`, `False` | 布尔值 | `&&`, `||`, `not` |
| `String` 又名 `[Char]` | `"abcd"`, `""` | 字符串 | `reverse`, `++` |

如你所见，Haskell 中的类型名称以大写字母开头。某些值（例如 `True`）也以大写字母开头，但变量和函数以小写字母开头（`reverse`、`not`、`x`）。我们将在第二讲中回到大写字母的含义。

函数类型使用 `->` 语法编写：

- 只有一个参数的函数：`argumentType -> returnType`
- ...两个参数：`argument1Type -> argument2Type -> returnType`
- ...三个参数：`argument1Type -> argument2Type -> argument3Type -> returnType`

看起来有点奇怪，对吧？我们也会回到这个话题。

### 1.6.3 关于容易误解的类型

有时，你在 GHCi 中看到的类型与你想象的有点不同。以下是两种常见情况。

```haskell
Prelude> :t 1+1
1+1 :: Num a => a
```

现在，你可以暂时将约束类型 `Num a => a` 理解为“任意数字类型”。在 Haskell 中，数字文字是“重载”的，这表示它们可以被解释为任何数字类型（例如 `Int` 或 `Double`）。当我们稍后讨论“类型类”时，我们将回到 `Num a` 的实际含义。

```haskell
Prelude> :t "asdf"
"asdf" :: [Char]
```

类型 `String` 只是类型 `[Char]` 的别名，意思是“字符列表”。我们将在下一讲中回到列表！在任何情况下，你都可以互换使用 `String` 和 `[Char]`，但 GHCi 在向你描述类型时主要使用 `[Char]`。


## 1.7 Haskell 程序的结构

这是一个简单的 Haskell 程序，它执行一些算术并打印一些值。

```haskell
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

如果你将其放入名为 `Gold.hs` 的文件中并使用（例如）`stack runhaskell Gold.hs` 运行它，你应该看到以下输出：

```text
0.0
-1.0
```

让我们浏览一下该文件。

```haskell
module Gold where
```

每个源文件有一个 Haskell *模块*。模块由*定义*组成。

```haskell
-- The golden ratio
```

这是一条评论。注释不是实际程序的一部分，而是供程序读者使用的文本。

```haskell
phi :: Double
phi = (sqrt 5 + 1) / 2
```

这是常量 `phi` 的定义，带有随附的*类型标注*（也称为*类型签名*）`phi :: Double`。类型标注意味着 `phi` 具有类型 `Double`。带有等号 (`=`) 的线称为*方程*。`=` 的左侧是我们正在定义的表达式， `=` 的右侧是定义。

一般来说，（函数或常量的）定义由可选的*类型标注*和一个或多个*方程*组成

```haskell
polynomial :: Double -> Double
polynomial x = x^2 - x - 1
```

这是名为 `polynomial` 的函数的定义。它有一个类型标注和一个方程。请注意函数方程与常数方程的不同之处在于 `=` 符号左侧存在参数 `x`。另请注意， `^` 是 Haskell 中的幂运算符，而不是像许多其他语言中的按位异或。

```haskell
f x = polynomial (polynomial x)
```

这是名为 `f` 的函数的定义。请注意缺少类型标注。`f` 是什么类型？

```haskell
main = do
  print (polynomial phi)
  print (f phi)
```

这是对运行程序时发生的情况的描述。它使用 do-syntax 和 IO Monad。我们将在课程的第二部分中回顾这些内容。


## 1.8 使用示例

当你看到这样的示例定义时

```haskell
polynomial :: Double -> Double
polynomial x = x^2 - x - 1
```

你通常应该尝试一下它。首先运行它。有几种方法可以做到这一点。

如果定义适合一行，你可以在 GHCi 中定义它：

```haskell
Prelude> polynomial x = x^2 - x - 1
Prelude> polynomial 3.0
5.0
```

对于多行定义，你可以使用 `;` 来分隔行，或者使用特殊的 `:{ :}` 语法将代码块粘贴到 GHCi 中：

```haskell
Prelude> :{
Prelude| polynomial :: Double -> Double
Prelude| polynomial x = x^2 - x - 1
Prelude| :}
Prelude> polynomial 3.0
5.0
```

最后，你可以将代码粘贴到新的或现有的 `.hs` 文件中，然后将 `:load` 粘贴到 GHCi 中。如果文件已经加载，你也可以使用 `:reload`。

```haskell
-- first copy and paste the definition into Example.hs, then run GHCi
Prelude> :load Example.hs
[1 of 1] Compiling Main             ( Example.hs, interpreted )
Ok, one module loaded.
*Main> polynomial 3.0
5.0
-- now you can edit the definition
*Main> :reload
[1 of 1] Compiling Main             ( Example.hs, interpreted )
Ok, one module loaded.
*Main> polynomial 3
3.0
```

运行示例后，尝试修改它，或创建另一个类似但不同的函数。你通过编程来学习编程，而不是通过阅读！

### 1.8.1 处理错误

由于 Haskell 是一种类型语言，因此你很快就会遇到类型错误。以下是 GHCi 会话期间的错误示例：

```haskell
Prelude> "string" ++ True

<interactive>:1:13: error:
    • Couldn't match expected type ‘[Char]’ with actual type ‘Bool’
    • In the second argument of ‘(++)’, namely ‘True’
      In the expression: "string" ++ True
      In an equation for ‘it’: it = "string" ++ True
```

这是最常见的类型错误，“无法匹配预期类型”。尽管该错误看起来又长又吓人，但如果你仔细阅读它，就会发现它非常简单。

- 错误消息的第一行 `<interactive>:1:13: error:` 告诉我们错误发生在 GHCi 中。如果我们加载了一个文件，我们可能会得到类似 `Sandbox.hs:3:17: error:` 的内容，其中 `Sandbox.hs` 是文件名， `3` 是行号， `17` 是行中字符的编号。

- `• Couldn't match expected type ‘[Char]’ with actual type ‘Bool’` 行告诉我们，错误的直接原因是存在 `Bool` 类型的表达式，而 GHCi 期望找到 `[Char]` 类型的表达式。错误消息的第一行指示了此错误的位置。请注意，预期的类型并不总是正确的。手动给出类型标注可以帮助调试类型错误。

- `• In the second argument of ‘(++)’, namely ‘True’` 行告诉我们，类型错误的表达式是运算符 `(++)` 的第二个参数。稍后我们将了解为什么它被括号包围。

- 有错误的完整表达式是 `"string" ++ True`。如上所述， `String` 是 `[Char]`（字符列表类型）的类型别名。`++` 的第一个参数是字符列表，由于 `++` 只能组合相同类型的两个列表，因此第二个参数也应该是 `[Char]` 类型。

- `In an equation for ‘it’: it = "string" ++ True` 行表示该表达式出现在变量 `it` 的定义中，这是 GHCi 用于独立表达式的默认变量名称。如果文件中有一行 `x = "string" ++ True`，或者 GHCi 中有一个声明 `let x = "string" ++ True`，GHCi 将打印 `In an equation for ‘x’: x = "string" ++ True`。

还有其他类型的错误。

```haskell
Prelude> True + 1

<interactive>:6:1: error:
    • No instance for (Num Bool) arising from a use of ‘+’
    • In the expression: True + 1
      In an equation for ‘it’: it = True + 1
```

当你尝试对非数字的内容使用 `+` 等数字函数时，就会出现这种错误。

最难追踪的错误通常是这样的：

```haskell
Prelude> True +

<interactive>:10:7: error:
    parse error (possibly incorrect indentation or mismatched brackets)
```

引起它的方法有很多种。可能你在某个地方遗漏了一些字符。我们将在本讲座的稍后部分回到缩进。

### 1.8.2 算术

Haskell 算术中有一件事经常让初学者犯错，那就是除法。

Haskell 中有两个除法函数， `/` 运算符和 `div` 函数。`div` 函数进行整数除法：

```haskell
Prelude> 7 `div` 2
3
```

`/` 运算符执行通常的除法：

```haskell
Prelude> 7.0 / 2.0
3.5
```

但是，你只能在整数类型（如 `Int` 和 `Integer`）上使用 `div`，并且只能在小数类型（如 `Double`）上使用 `/`。以下是如果你尝试将它们混合在一起会发生什么情况的示例：

```haskell
halve :: Int -> Int
halve x = x / 2
```

```text
error:
    • No instance for (Fractional Int) arising from a use of ‘/’
    • In the expression: x / 2
      In an equation for ‘halve’: halve x = x / 2
```

现在请记住这一点。我们将回到 `/` 和 `div` 之间的区别，以及 `Num` 和 `Fractional` 在谈论类型类时的含义。


## 1.9 如何编写实际程序？

到目前为止，你已经了解了一些算术、反转字符串等等。如何用 Haskell 编写实际的程序？Haskell 中缺少许多常见的编程结构，例如循环、语句和赋值。接下来，我们将介绍 Haskell 程序的基本构建块：

- 条件表达式
- 局部定义
- 模式匹配
- 递归

### 1.9.1 条件表达式

在其他语言中， `if` 是一个*语句*。它没有值，只是有条件地执行其他语句。

在 Haskell 中， `if` 是一个*表达式*。它有一个值。它在其他两个表达式之间进行选择。它对应于C或Java中的 `?:` 运算符。

```java
// Java
int price = product.equals("milk") ? 1 : 2;
```

Python 的条件表达式与 Haskell 的 `if` 非常接近：

```python
# Python
price = 1 if product == "milk" else 2
```

这是同一示例在 Haskell 中的样子：

```haskell
price = if product == "milk" then 1 else 2
```

因为 Haskell 的 `if`*返回*一个值，所以你**总是**需要一个 `else`！

#### 1.9.1.1 返回 `Bool` 的函数

为了编写 if 表达式，你需要知道如何获取 `Bool` 类型的值。最常见的方法是比较。通常的 `==`、`<`、`<=`、`>` 和 `>=` 运算符在 Haskell 中工作。你可以对各种数字做大小比较（`<`、`>`），并对几乎所有值进行相等比较（`==`）：

```haskell
Prelude> "foo" == "bar"
False
Prelude> 5.0 <= 7.2
True
Prelude> 1 == 1
True
```

Haskell 的一个奇怪之处是不等运算符被写为 `/=` 而不是通常的 `!=`：

```haskell
Prelude> 2 /= 3
True
Prelude> "bike" /= "bike"
False
```

请记住，除了这些比较之外，你还可以使用 `&&`（“与”）和 `||`（“或”）运算符以及 `not` 函数从其他 `Bool` 值中获取 `Bool` 值。

#### 1.9.1.2 示例

```haskell
checkPassword password = if password == "swordfish"
                         then "You're in."
                         else "ACCESS DENIED!"
```

```haskell
absoluteValue n = if n < 0 then -n else n
```

```haskell
login user password = if user == "unicorn73"
                      then if password == "f4bulous!"
                           then "unicorn73 logged in"
                           else "wrong password"
                      else "unknown user"
```

### 1.9.2 局部定义

Haskell 有两种不同的方式来创建局部定义：`let...in` 和 `where`。

`where` 将局部定义添加到定义中：

```haskell
circleArea :: Double -> Double
circleArea r = pi * rsquare
    where pi = 3.1415926
          rsquare = r * r
```

`let...in` 是一个表达式：

```haskell
circleArea r = let pi = 3.1415926
                   rsquare = r * r
               in pi * rsquare
```

局部定义也可以是函数：

```haskell
circleArea r = pi * square r
    where pi = 3.1415926
          square x = x * x
```

```haskell
circleArea r = let pi = 3.1415926
                   square x = x * x
               in pi * square r
```

我们将回到 `let` 和 `where` 之间的差异，但大多数情况下你可以使用你喜欢的任何一个。

### 1.9.3 关于不变性的一句话

尽管像上面的 `pi` 这样的东西通常被称为“变量”，但我在这里选择将它们称为“定义”。这是因为与 Python 或 Java 中的变量不同，这些定义的值无法更改。 Haskell 变量不是可以放入新值的盒子，Haskell 变量命名一个值（或者更确切地说，一个表达式），仅此而已。

我们将在本课程稍后再次讨论不变性，但现在只要知道这样的事情行不通就足够了。

```haskell
increment x = let x = x+1
              in x
```

这只是一个无限循环，因为它尝试使用属性 `x = x+1` 定义一个新变量 `x`。因此，在计算 `x` 时，Haskell 只是无限期地继续计算 `1+1+1+1+...`。

```haskell
compute x = let a = x+1
                a = a*2
            in a
```

```text
error:
    Conflicting definitions for ‘a’
    Bound at: <interactive>:14:17
              <interactive>:15:17
```

当我们尝试“更新”`a` 的值时，我们会得到一个简单的错误。

需要注意的是，局部定义可以“隐藏”其他地方定义的变量名称。名称遮蔽（shadowing）不是副作用。相反，遮蔽会在更受限制的范围内创建一个新变量，该变量使用与外部范围中的某些变量相同的名称。例如，下面的所有函数 `f`、`g` 和 `h` 都是合法的：

```haskell
x :: Int
x = 5

f :: Int -> Int
f x = 2 * x

g :: Int -> Int
g y = x where x = 6

h :: Int -> Int
h x = x where x = 3
```

如果我们将它们应用于全局常量 `x`，我们会看到名称遮蔽的效果：

```haskell
f 1 ==> 2
g 1 ==> 6
h 1 ==> 3

f x ==> 10
g x ==> 6
h x ==> 3
```

最好始终为局部变量选择新名称，这样就不会发生名称遮蔽。这样，代码的读者将了解表达式中使用的变量来自何处。请注意，在以下示例中， `f` 和 `g` 不会隐藏彼此的参数：

```haskell
f :: Int -> Int
f x = 2 * x + 1

g :: Int -> Int
g x = x - 2
```

### 1.9.4 模式匹配

（函数的）定义可以由多个*方程*组成。这些方程按照参数的顺序进行匹配，直到找到合适的方程。这称为“模式匹配”。

Haskell 中的模式匹配非常强大，我们将在本课程中不断学习有关它的新知识，但这里有几个第一个示例：

```haskell
greet :: String -> String -> String
greet "Finland" name = "Hei, " ++ name
greet "Italy"   name = "Ciao, " ++ name
greet "England" name = "How do you do, " ++ name
greet _         name = "Hello, " ++ name
```

函数 `greet` 生成给定国家/地区和名称（均为 `String`）的问候语。它具有针对三个国家的特殊情况和一个默认情况。它是这样工作的：

```haskell
Prelude> greet "Finland" "Pekka"
"Hei, Pekka"
Prelude> greet "England" "Bob"
"How do you do, Bob"
Prelude> greet "Italy" "Maria"
"Ciao, Maria"
Prelude> greet "Greenland" "Jan"
"Hello, Jan"
```

特殊模式 `_` 可以匹配任何内容。它通常用于默认情况。由于模式是按顺序匹配的，因此（*通常*）将 `_` 情况放在最后非常重要。考虑：

```haskell
brokenGreet _         name = "Hello, " ++ name
brokenGreet "Finland" name = "Hei, " ++ name
```

现在为所有输入匹配第一种情况。

```haskell
Prelude> brokenGreet "Finland" "Varpu"
"Hello, Varpu"
Prelude> brokenGreet "Sweden" "Ole"
"Hello, Ole"
```

GHC 甚至会向你发出有关此代码的警告：

```text
<interactive>:1:1: warning: [-Woverlapping-patterns]
    Pattern match is redundant
    In an equation for ‘brokenGreet’: brokenGreet "Finland" name = ...
```

下面是一些更多的例子。但首先让我们介绍一下标准库函数 `show`，它可以将（几乎！）任何东西转换为字符串：

```haskell
Prelude> show True
"True"
Prelude> show 3
"3"
```

因此，这是一个具有模式匹配的函数示例和实际使用该值的默认情况（而不是仅仅使用 `_` 忽略它）：

```haskell
describe :: Integer -> String
describe 0 = "zero"
describe 1 = "one"
describe 2 = "an even prime"
describe n = "the number " ++ show n
```

它是这样工作的：

```haskell
Prelude> describe 0
"zero"
Prelude> describe 2
"an even prime"
Prelude> describe 7
"the number 7"
```

你甚至可以对多个参数进行模式匹配。再次按顺序尝试方程。以下是之前 `login` 函数的重新实现：

```haskell
login :: String -> String -> String
login "unicorn73" "f4bulous!" = "unicorn73 logged in"
login "unicorn73" _           = "wrong password"
login _           _           = "unknown user"
```

### 1.9.5 递归

在 Haskell 中，各种循环都是通过递归实现的。函数调用非常高效，因此你无需担心性能。 （我们稍后会讨论性能）。

学习如何在 Haskell 中使用递归做简单的事情将有助于你以后在更复杂的问题上使用递归。递归通常也是思考解决更困难问题的有用方法。

这是我们计算阶乘的第一个递归函数。在数学中，阶乘是 *n* 个前正整数的乘积，写为 *n!*。阶乘的定义是

> *n！ = n \* (n-1) \* … \* 1*

例如，*4！ = 4\*3\*2\*1 = 24*。无论如何，这是阶乘的 Haskell 实现：

```haskell
factorial :: Int -> Int
factorial 1 = 1
factorial n = n * factorial (n-1)
```

这就是它的工作原理。我们使用 `==>` 来表示“求值到”。

```haskell
factorial 3
  ==> 3 * factorial (3-1)
  ==> 3 * factorial 2
  ==> 3 * 2 * factorial 1
  ==> 3 * 2 * 1
  ==> 6
```

当你求值 `factorial (-1)` 时会发生什么？

这是另一个例子：

```haskell
-- compute the sum 1^2+2^2+3^2+...+n^2
squareSum 0 = 0
squareSum n = n^2 + squareSum (n-1)
```

一个函数可以多次递归调用自身。作为一个例子，让我们考虑数学中的“斐波那契数列”。斐波那契数列是具有以下定义的整数序列。

> 该序列从 1, 1 开始。要获取序列的下一个元素，请将序列的前两个元素相加。

斐波那契数列的第一个元素是 1、1、2、3、5、8、13 等。这是一个函数 `fibonacci`，它计算斐波那契序列中的第 `n` 元素。请注意它如何反映数学定义。

```haskell
-- Fibonacci numbers, slow version
fibonacci 1 = 1
fibonacci 2 = 1
fibonacci n = fibonacci (n-2) + fibonacci (n-1)
```

`fibonacci 5` 的求值方式如下：

```haskell
fibonacci 5
  ==> fibonacci 3                 + fibonacci 4
  ==> (fibonacci 1 + fibonacci 2) + fibonacci 4
  ==> (    1       +       1    ) + fibonacci 4
  ==> (    1       +       1    ) + (fibonacci 2 + fibonacci 3)
  ==> (    1       +       1    ) + (fibonacci 2 + (fibonacci 1 + fibonacci 2))
  ==> (    1       +       1    ) + (    1       + (    1       +     1      ))
  ==> 5
```

请注意 `fibonacci 3` 如何求值两次， `fibonacci 2` 如何求值三次。这不是 `fibonacci` 函数最有效的实现。我们将在下一讲中再讨论这个问题。考虑斐波那契函数求值的另一种方法是将其可视化为一棵树（我们将 `fibonacci` 缩写为 `fib`）：

![Fibonacci 步骤 1](img/Fibonacci-step1.svg)

![Fibonacci 步骤 2](img/Fibonacci-step2.svg)

![Fibonacci 步骤 3](img/Fibonacci-step3.svg)

![Fibonacci 步骤 4](img/Fibonacci-step4.svg)

![Fibonacci 步骤 5](img/Fibonacci-step5.svg)

![Fibonacci 步骤 6](img/Fibonacci-step6.svg)

![Fibonacci 步骤 7](img/Fibonacci-step7.svg)

![Fibonacci 步骤 8](img/Fibonacci-step8.svg)

该树精确地对应于表达式 `(1 + 1) + (1 + (1 + 1))`。递归通常可以产生链状、树状、嵌套或循环结构和计算。递归是函数式编程的主要技术之一，因此值得花一些精力来学习它。


## 1.10 综合示例

最后，这是一个完整的 Haskell 模块，它使用 if、模式匹配、局部定义和递归。该模块关注 [*Collatz conjecture*](https://en.wikipedia.org/wiki/Collatz_conjecture)，这是一个著名的数学开放问题。它问：

> 对于所有正整数初始值，Collatz 序列最终是否都会达到 1？

Collatz 序列的定义是，以任意数字为起始值，然后重复执行以下操作：

- 如果数字是偶数，则将其除以二
- 如果数字是奇数，则将其增加三倍并加一

举个例子，3 的 Collatz 序列是：3, 10, 5, 16, 8, 4, 2, 1, 4, 2, 1, 4, 2, 1 … 正如你所看到的，一旦数字达到 1，它就会陷入循环。

```haskell
module Collatz where

-- one step of the Collatz sequence
step :: Integer -> Integer
step x = if even x then down else up
  where down = div x 2
        up = 3*x+1

-- collatz x computes how many steps it takes for the Collatz sequence
-- to reach 1 when starting from x
collatz :: Integer -> Integer
collatz 1 = 0
collatz x = 1 + collatz (step x)

-- longest finds the number with the longest Collatz sequence for initial values
-- between 0 and upperBound
longest :: Integer -> Integer
longest upperBound = longest' 0 0 upperBound

-- helper function for longest
longest' :: Integer -> Integer -> Integer -> Integer
-- end of recursion, return longest length found
longest' number _ 0 = number
-- recursion step: check if n has a longer Collatz sequence than the current known longest
longest' number maxlength n =
  if len > maxlength
  then longest' n len (n-1)
  else longest' number maxlength (n-1)
  where len = collatz n
```

我们可以在 GHCi 中加载该程序并使用它。

```haskell
$ stack ghci
GHCi, version 9.2.8: https://www.haskell.org/ghc/  :? for help
Prelude> :load Collatz.hs
[1 of 1] Compiling Collatz          ( Collatz.hs, interpreted )
Ok, one module loaded.
*Collatz>
```

让我们验证一下我们的程序是否正确计算了 3 的 Collatz 序列的开头。

```haskell
*Collatz> step 3
10
*Collatz> step 10
5
*Collatz> step 5
16
```

3 需要多少步才能到达 1？

```haskell
*Collatz> collatz 3
7
```

起始值小于 10 时，最长的 Collatz 序列从哪个数开始？100 呢？

```haskell
*Collatz> longest 10
9
*Collatz> longest 100
97
```

这些 Collatz 序列的长度为：

```haskell
*Collatz> collatz 9
19
*Collatz> collatz 97
118
```


## 1.11 关于缩进

前面的例子已经被巧妙地缩进了。在 Haskell 中缩进很重要，有点像在 Python 中。完整的缩进规则很难描述，但你应该能够很好地遵循这些经验规则：

1.  分组在一起的事物从同一列开始
2.  如果必须将表达式（或方程）拆分为多行，请增加缩进

虽然你可以不使用制表符，但强烈建议在所有缩进中使用空格。

一些例子是有序的。

这些都可以：

```haskell
i x = let y = x+x+x+x+x+x in div y 5

-- let and in are grouped together, an expression is split
j x = let y = x+x+x
              +x+x+x
      in div y 5

-- the definitions of a and b are grouped together
k = a + b
  where a = 1
        b = 1

l = a + b
  where
    a = 1
    b = 1
```

这些都不行：

```haskell
-- indentation not increased even though expression split on many lines
i x = let y = x+x+x+x+x+x
in div y 5

-- indentation not increased even though expression is split
j x = let y = x+x+x
      +x+x+x
      in div y 5

-- grouped things are not aligned
k = a + b
  where a = 1
      b = 1

-- grouped things are not aligned
l = a + b
  where
    a = 1
     b = 1

-- where is part of the equation, so indentation needs to increase
l = a + b
where
  a = 1
  b = 1
```

如果你在缩进方面犯了错误，通常会收到如下解析错误：

```text
Indent.hs:2:1: error: parse error on input ‘where’
```

该错误包括行号，因此只需再次检查该行即可。如果你似乎无法让缩进发挥作用，请尝试首先将所有内容都放在一根长线上。


## 1.12 测验

在每堂课结束时，你都会发现这样的测验。测验不评分，只是为了帮助你检查你是否理解了本章。你可以通过单击选项来检查你的答案。如果你是对的，你会看到绿色背景，如果你错了，你会看到红色背景。你可以随意猜测多次，只要确保你明白为什么正确的选项最终是正确的。

C/Java/Python 表达式 `combine(prettify(lawn),construct(house,concrete))` 的 Haskell 等价物是什么？

1.  `combine prettify (lawn) construct (house concrete)`
2.  `combine (prettify lawn (construct house concrete))`
3.  `combine (prettify lawn) (construct house concrete)`

Haskell 表达式 `send metric (double population + increase)` 的 C/Java/Python 等价物是什么？

1.  `send(metric(double(population+increase)))`
2.  `send(metric(double(population)+increase))`
3.  `send(metric,double(population)+increase)`
4.  `send(metric,double(population+increase))`

在 Haskell 中，以下哪一项说法是正确的？

1.  每个值都有一个类型
2.  每种类型都有一个值
3.  每个语句都有一个类型

在 Haskell 中，以下哪一项说法是正确的？

1.  不可能重用变量的名称
2.  可以为变量重新赋值
3.  `if` 始终需要 `then` 和 `else`

函数 `f x = if even (x + 1) then x + 1 else f (x - 1)` 有什么作用？

1.  将每个值 `x` 映射到大于或等于 `x` 的最小偶数
2.  将每个值 `x` 映射到小于或等于 `x` 的最大偶数
3.  将每个值映射到其自身

为什么 `3 * "F00"` 不是有效的 Haskell？

1.  `3` 和 `"F00"` 有不同的类型
2.  所有数值都需要小数点
3.  `"F00"` 需要前缀“0x”

为什么 ``7.0 `div` 2`` 会出错？

1.  因为 `div` 没有为类型 `Double` 定义
2.  因为 `div` 没有为类型 `Int` 定义
3.  因为三个反引号用于分隔字符串。


## 1.13 练习

课程材料（包括练习）可在 GitHub 上的 Git 存储库（<https://github.com/moocfi/haskell-mooc>）中获取。如果你不熟悉 Git，请参阅[GitHub’s instructions on cloning a repository](https://help.github.com/en/github/creating-cloning-and-archiving-repositories/cloning-a-repository)。

克隆 `haskell-mooc` 存储库后，进入 `exercises` 目录。要下载并构建运行练习测试所需的依赖项（例如正确版本的 GHC 和各种库），请在终端中运行以下命令：

```sh
$ stack build
```

请注意，依赖项有多个千兆字节，命令需要一段时间才能完成。

**注意！** 以下是 `stack build` 常见问题的一些修复：

- 如果出现类似 `While building package zlib-0.6.2.3` 的错误，则需要安装 zlib 库头文件。Ubuntu 的正确命令是 `sudo apt install zlib1g-dev`。
- 如果你收到类似 `Downloading lts-18.18 build plan ... RedownloadInvalidResponse` 的错误，则说明你的 Stack 版本太旧。运行 `stack upgrade` 以获得更新的版本。

`exercises` 目录中主要有两类文件：名为 `SetNX.hs` 的练习集，以及名为 `SetNXTest.hs` 的配套测试程序。两者都是 Haskell 源文件，但做习题时只需编辑练习文件。每道练习的说明都作为注释嵌入到练习文件中。

使用测试文件检查你的答案。例如，当你解决了 `Set1.hs` 中的一些练习时，请运行以下命令：

```sh
$ stack runhaskell Set1Test.hs
```

测试的输出看起来像这样：

```text
===== EXERCISE 1
+++++ Pass
===== EXERCISE 2
+++++ Pass
===== EXERCISE 3
*** Failed! Falsified (after 2 tests and 1 shrink):
quadruple 1
  Expected: 4
  Was: 2

----- Fail
===== EXERCISE 4
+++++ Pass
===== EXERCISE 5
+++++ Pass
===== EXERCISE 6
+++++ Pass
===== EXERCISE 7
+++++ Pass
===== EXERCISE 8
+++++ Pass
===== EXERCISE 9
+++++ Pass
===== EXERCISE 10
+++++ Pass
===== EXERCISE 11
+++++ Pass
===== EXERCISE 12
+++++ Pass
===== EXERCISE 13
+++++ Pass
===== EXERCISE 14
+++++ Pass
===== EXERCISE 15
+++++ Pass
===== EXERCISE 16
+++++ Pass
===== EXERCISE 17
+++++ Pass
===== EXERCISE 18
+++++ Pass
===== EXERCISE 19
+++++ Pass
===== TOTAL
1101111111111111111
18 / 19
```

在上面的例子中，我在练习 3 中犯了一个错误。

为了使调试更快、更直接，我可以在 GHCi 中加载练习文件，这使我能够手动求值任何顶层函数。例如，我可以通过以下方式验证上述错误：

```haskell
$ stack ghci Set1.hs
GHCi, version 9.2.8: https://www.haskell.org/ghc/  :? for help
[1 of 2] Compiling Mooc.Todo        ( Mooc/Todo.hs, interpreted )
[2 of 2] Compiling Set1             ( Set1.hs, interpreted )
Ok, two modules loaded.
*Set1> quadruple 1
2
```

完成一组练习后，你可以将其提交到课程页面上的 [Submit page](https://haskell.mooc.fi/submit.php)。之后，你可以在 [Results page](https://haskell.mooc.fi/results.php) 查看提交结果，并在 [My status page](https://haskell.mooc.fi/status.php) 查看总分。

**注意！** 你可以根据需要多次提交练习集。

**注意！** 如果你不想使用 Stack 或无法使其工作，你还应该能够使用[Cabal](https://www.haskell.org/cabal/download.html)运行测试，如下所示：

```sh
$ cabal v2-build
$ cabal v2-exec runhaskell Set1Test.hs
```

### 1.13.1 参考解

成功完成一组练习的所有题目后，你可以在 [My status page](https://haskell.mooc.fi/status) 查看参考解。浏览参考解很有用，它们可能会展示你尚未想到的技巧。


## 1.14 练习

- [Set1](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set1.hs)
