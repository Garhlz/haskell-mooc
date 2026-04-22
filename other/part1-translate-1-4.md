- [1 第 1 讲：……就这样开始](#lecture-1-and-so-it-begins)
  - [1.1 课程简介](#about-the-course)
  - [1.2 阅读这些内容](#read-these)
  - [1.3 Haskell](#haskell)
  - [1.4 运行 Haskell](#running-haskell)
  - [1.5 开始吧！](#lets-start)
  - [1.6 表达式和类型](#expressions-and-types)
  - [1.7 Haskell 程序的结构](#the-structure-of-a-haskell-program)
  - [1.8 使用示例](#working-with-examples)
  - [1.9 我如何完成任务？](#how-do-i-get-anything-done)
  - [1.10 现在大家在一起吧！](#all-together-now)
  - [1.11 关于缩进](#a-word-about-indentation)
  - [1.12 测验](#quiz)
  - [1.13 练习](#working-on-the-exercises)
  - [1.14 练习](#exercises)
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
- [3 第 3 讲：Catamorphic](#lecture-3-catamorphic)
  - [3.1 最后的函数式编程](#functional-programming-at-last)
  - [3.2 偏应用](#partial-application)
  - [3.3 前缀和中缀表示法](#prefix-and-infix-notations)
  - [3.4 lambda 表达式](#lambdas)
  - [3.5 附注：`.` 和 `$` 运算符](#sidenote-the-.-and-operators)
  - [3.6 示例：重写 `whatFollows`](#example-rewriting-whatfollows)
  - [3.7 更多函数式列表处理示例](#more-functional-list-wrangling-examples)
  - [3.8 列表和递归](#lists-and-recursion)
  - [3.9 有趣的事情：列表推导式](#something-fun-list-comprehensions)
  - [3.10 有趣的事情：自定义运算符](#something-fun-custom-operators)
  - [3.11 有用的东西：类型孔](#something-useful-typed-holes)
  - [3.12 测验](#quiz-2)
  - [3.13 练习](#exercises-2)
- [4 第 4 讲：真正的类型类](#lecture-4-real-classy)
  - [4.1 附注：元组](#sidenote-tuples)
  - [4.2 插曲：折叠](#interlude-folding)
  - [4.3 类型类](#type-classes)
  - [4.4 类型约束](#type-constraints)
  - [4.5 标准类型类](#standard-type-classes)
  - [4.6 更多数据结构](#more-data-structures)
  - [4.7 阅读文档](#reading-docs)
  - [4.8 测验](#quiz-3)
  - [4.9 练习](#exercises-3)


# Haskell 慕课，第 1 部分

作者：Joel Kaasinen ([Nitor](https://nitor.com/en)) 和 John Lång（赫尔辛基大学）

# 1 第 1 讲：……就这样开始

## 1.1 课程简介

这是使用 Haskell 编程语言的函数式编程在线课程。你可以按照自己的进度学习。所有材料和练习都是公开可用的。

本课程面向希望学习函数式编程的初学者，也针对具有函数式编程经验、特别是想学习 Haskell 的人。本课程不需要任何基础知识，但提前了解至少一种编程语言将使课程变得更容易。

练习包括了解如何使用命令行以及 Git 版本控制系统的基本用法。

这是由两部分组成的课程的第一部分。第 1 部分介绍 Haskell 语法和功能的基础知识。你将了解递归、高阶函数、代数数据类型和 Haskell 的一些高级功能。然而，第 1 部分将坚持纯函数式编程，没有副作用。I/O 和 Monad 将在第 2 部分中介绍。

该课程分为8个讲座。它们的大小大致相同，但有些讲座的材料比其他讲座更多。每套讲座最后都会有 10-30 个关于讲座主题的小型编程练习。

## 1.2 阅读这些内容

除了本课程材料之外，如果你觉得缺少示例或解释，以下资源可能会很有用。

- [课程页面](https://haskell.mooc.fi)
- 课程的 [Telegram 频道](https://t.me/haskell_mooc_fi)
- 课程的 [GitHub 仓库](https://github.com/moocfi/haskell-mooc)包含练习和本材料
- 其他资源
  - [A Gentle Introduction to Haskell](https://www.haskell.org/tutorial/) - 一个较旧且较短的教程，但仍然值得阅读
  - [Learn You a Haskell for Great Good!](http://learnyouahaskell.com/chapters) - 一本很好的免费 Haskell 入门材料
  - [The Haskell School of Expression](https://www.cs.yale.edu/homes/hudak/SOE/index.htm) - 稍旧但仍然有价值的函数式编程入门材料
  - [Haskell Programming from First Principles](https://haskellbook.com/) - 59 美元的 Haskell 电子书，节奏慢、篇幅长
  - [libera.chat](https://libera.chat/)上的 IRC 频道 `#haskell` 是初学者的好地方

## 1.3 Haskell

Haskell 是：

**函数式** – 程序的基本构建块是函数。函数可以返回函数，也可以接受函数作为参数。此外，Haskell 中唯一的循环结构是递归。

**纯** - Haskell 函数是纯的，也就是说，它们没有副作用。副作用是指读取文件、打印文本或更改全局变量等。函数的所有输入都必须位于其参数中，函数的所有输出都必须位于其返回值中。这听起来有限制，但使程序的推理变得更容易，并允许编译器进行更多优化。

**惰性** - 值只有在需要时才会被求值。这使得使用无限数据结构成为可能，并且也使纯程序更加高效。

**强类型** - 每个 Haskell 值和表达式都有一个类型。编译器在编译时检查类型，并保证运行时不会发生类型错误。这意味着不会出现 AttributeErrors（类似于 Python）、ClassCastExceptions（类似于 Java）或分段错误（类似于 C）。Haskell 类型系统非常强大，可以帮助你设计更好的程序。

**类型推断** - 除了检查类型之外，编译器还可以推断大多数程序的类型。这使得使用强类型语言变得更加容易。事实上，大多数 Haskell 函数都可以完全不用类型来编写。然而，程序员仍然可以为函数和值提供类型标注，以便更轻松地查找类型错误。类型标注还使阅读程序变得更加容易。

**垃圾收集** - 与当今大多数高级语言一样，Haskell 通过垃圾收集进行自动内存管理。这意味着程序员不需要担心分配或释放内存，语言运行时会自动处理这些事情。

**编译** - 尽管我们在本课程中主要通过交互式 GHCi 环境使用 Haskell，但 Haskell 是一种编译语言。Haskell 程序可以编译为非常高效的二进制文件，并且 GHC 编译器非常擅长将函数式代码优化为高性能机器代码。

在本课程中，你将了解这些术语在实践中的含义。如果其中一些现在听起来很抽象，请不要担心。

另请参见：[Haskell Wiki 的函数式编程页面](https://wiki.haskell.org/Functional_programming)。

### 1.3.1 特点

下面展示了 Haskell 的一些很酷的功能：

**高阶函数** – 函数可以接受函数作为参数：

``` haskell
map length ["abc","abcdef"]
```

结果是 `[3,6]`。

**匿名函数又名 lambda** – 你可以定义一次性帮助函数而无需给它们命名

``` haskell
filter (\x -> length x > 1) ["abc","d","ef"]
```

结果是 `["abc","ef"]`。

**偏应用** – 你可以通过仅向另一个函数提供它需要的一些参数来定义新函数。例如，把列表中的所有元素乘以 3：

``` haskell
map (*3) [1,2,3]
```

**代数数据类型** – 用于定义可以包含多种不同情况的数据类型的语法：

``` haskell
data Shape = Point | Rectangle Double Double | Circle Double
```

现在，类型 `Shape` 可以有 `Point`、`Rectangle 3 6` 和 `Circle 5` 等值。

**模式匹配** – 根据与你的数据定义相对应的情况定义函数：

``` haskell
area Point = 0
area (Rectangle width height) = width * height
area (Circle radius) = 2 * pi * radius * radius
```

**列表** – 与许多语言不同，Haskell 具有简洁的内置列表语法。可以使用*列表推导式*从其他列表构建列表。下面是一个片段，它从一组名字和姓氏选项中生成偶数长度的名字：

``` haskell
[whole | first <- ["Eva", "Mike"],
         last <- ["Smith", "Wood", "Odd"],
         let whole = first ++ last,
         even (length whole)]
```

结果是 `["EvaSmith","EvaOdd","MikeWood"]`。由于 Haskell 的惰性，我们甚至可以创建所谓的*无限列表*：

``` haskell
primes = [ n | n <- [2..] , all (\k -> n `mod` k /= 0) [2..n `div` 2] ]
```

然后可以通过下面的表达式取得前十个素数：

``` haskell
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

“haskel”这个词在希伯来语中的意思是智慧，但 Haskell 编程语言的名称来自逻辑学家 Haskell Curry。 Haskell 这个名字来自古挪威语单词 áss（上帝）和 ketill（头盔）。

### 1.3.3 Haskell 的使用

以下是用 Haskell 编写的软件项目的一些示例。

- [Darcs](https://en.wikipedia.org/wiki/Darcs)分布式版本控制系统
- [Sigma spam-prevention tool at Facebook](https://engineering.fb.com/security/fighting-spam-with-haskell/)
- [PureScript](https://www.purescript.org/)和[Elm](https://elm-lang.org/)编程语言的实现是用 Haskell 编写的
- 用于在不同文档格式之间进行转换的[Pandoc](https://pandoc.org/)工具 - 它也用于生成本课程材料
- 为 PostgreSQL 数据库公开 HTTP REST API 的[PostgREST](https://postgrest.org/)服务器
- 像[Galois](https://galois.com/)和[Well-Typed](https://well-typed.com/)这样的函数式编程咨询公司在使用 Haskell 为客户开发关键系统方面拥有悠久的历史

请参阅[Haskell Wiki](https://wiki.haskell.org/Haskell_in_industry)和[这篇博客文章](https://serokell.io/blog/top-software-written-in-haskell)了解更多！

## 1.4 运行 Haskell

获取 Haskell 的最简单方法是安装 `stack` 工具，请参阅<https://haskellstack.org>。本课程的练习旨在与 Stack 配合使用，因此你现在应该使用它。

顺便说一句，如果你对 Stack 是什么以及它与 Cabal 和 GHC 等其他 Haskell 工具的关系感兴趣，请阅读[这篇说明](https://www.quora.com/What-is-the-difference-between-Cabal-and-Stack-in-Haskell-projects-Which-one-do-you-recommend-and-why)或[这篇 FAQ](https://docs.haskellstack.org/en/stable/faq/)。我们将在课程的第 2 部分中回顾 Haskell 包并详细使用它们。

现在，安装 Stack 后，只需运行 `stack ghci` 即可获得交互式 Haskell 环境。

**注意！** GHC 8.10.7 有一个 GHCi 错误，导致在基于 ARM 的系统上无法编辑行。作为解决方法，请使用 `TERM=dumb stack ghci`。更多信息见[这里](https://gitlab.haskell.org/ghc/ghc/-/issues/20022)。

## 1.5 开始吧！

GHCi 是交互式 Haskell 解释器。这是一个示例会话：

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

顺便说一下，第一次运行 `stack ghci` 时，它会下载 GHC 和一些库，所以如果你看到一些输出并且需要等待一段时间才能收到 `Prelude>` 提示，请不要担心。

让我们来看看这个。如果你还不明白，请不要担心，这只是表达式和​​类型的第一次接触。

    Prelude> 1+1
    2

`Prelude>` 是 GHCi 提示符。它表明我们可以使用 Haskell 基础库中名为 Prelude 的函数。我们求值 1 加 1，结果是 2。

    Prelude> "asdf"
    "asdf"

这里我们计算一个字符串文字，结果是相同的字符串。

    Prelude> reverse "asdf"
    "fdsa"

在这里，我们通过将函数 `reverse` 应用到值 `"asdf"` 来计算字符串的反转。

    Prelude> :type "asdf"
    "asdf" :: [Char]

除了计算表达式之外，我们还可以使用 `:type`（缩写为 `:t`）GHCi 命令询问其类型。`"asdf"` 的类型是字符列表。以 `:` 开头的命令是 GHCi 用户界面的一部分，而不是 Haskell 语言的一部分。

    Prelude> tail "asdf"
    "sdf"
    Prelude> :t tail "asdf"
    tail "asdf" :: [Char]

`tail` 函数适用于列表并返回除列表的第一个元素之外的所有元素。在这里我们看到 `tail` 应用于 `"asdf"`。我们还检查表达式的类型，它是一个字符列表，正如预期的那样。

    Prelude> :t tail
    tail :: [a] -> [a]

最后，这是 `tail` 函数的类型。它接受任何类型的列表作为参数，并返回相同类型的列表。

    Prelude> :quit
    Leaving GHCi.

这就是你退出 GHCi 的方式。

## 1.6 表达式和类型

就像我们在上面的 GHCi 示例中看到的那样，*表达式*和*类型*是 Haskell 的面包和黄油。事实上，Haskell 程序中的几乎所有值都是表达式。特别是，没有像 Python、Java 或 C 中那样的*语句*。

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

附言。在 Haskell 中，函数应用*关联左*，即 `f g x y` 实际上与 `(((f g) x) y)` 相同。我们稍后再回到这个话题。现在，你可以认为 `f g x y` 是应用到参数 `g`、`x` 和 `y` 的 `f`。

### 1.6.2 类型语法

以下是一些可以帮助你入门的 Haskell 基本类型。

| 类型 | 文字 | 使用 | 运营 |
|:---|:---|:---|:---|
| `Int` | `1`, `2`, `-3` | 数字类型（有符号，64 位） | `+`, `-`, `*`, `div`, `mod` |
| `Integer` | `1`, `-2`, `900000000000000000` | 无界数字类型 | `+`, `-`, `*`, `div`, `mod` |
| `Double` | `0.1`, `1.2e5` | 浮点数 | `+`, `-`, `*`, `/`, `sqrt` |
| `Bool` | `True`, `False` | 真理值 | `&&`, ` |  | `, ` 不是` |
| `String` 又名 `[Char]` | `"abcd"`, `""` | 字符串 | `reverse`, `++` |

正如你所看到的，Haskell 中的类型名称以大写字母开头。某些值（例如 `True`）也以大写字母开头，但变量和函数以小写字母开头（`reverse`、`not`、`x`）。我们将在第二讲中回到大写字母的含义。

函数类型使用 `->` 语法编写：

- 只有一个参数的函数：`argumentType -> returnType`
- ...两个参数：`argument1Type -> argument2Type -> returnType`
- ...三个参数：`argument1Type -> argument2Type -> argument3Type -> returnType`

看起来有点奇怪，对吧？我们也会回到这个话题。

### 1.6.3 关于误导类型的注意事项

有时，你在 GHCi 中看到的类型与你想象的有点不同。以下是两种常见情况。

``` haskell
Prelude> :t 1+1
1+1 :: Num a => a
```

现在，你应该将类​​类型 `Num a => a` 理解为“任意数字类型”。在 Haskell 中，数字文字是“重载”的，这表示它们可以被解释为任何数字类型（例如 `Int` 或 `Double`）。当我们稍后讨论“类型类”时，我们将回到 `Num a` 的实际含义。

``` haskell
Prelude> :t "asdf"
"asdf" :: [Char]
```

类型 `String` 只是类型 `[Char]` 的别名，意思是“字符列表”。我们将在下一堂课中回到列表！在任何情况下，你都可以互换使用 `String` 和 `[Char]`，但 GHCi 在向你描述类型时主要使用 `[Char]`。

## 1.7Haskell 程序的结构

这是一个简单的Haskell 程序，它执行一些算术并打印一些值。

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

如果你将其放入名为 `Gold.hs` 的文件中并使用（例如）`stack runhaskell Gold.hs` 运行它，你应该看到以下输出

    0.0
    -1.0

让我们浏览一下该文件。

``` haskell
module Gold where
```

每个源文件有一个 Haskell *模块*。模块由*定义*组成。

``` haskell
-- The golden ratio
```

这是一条评论。注释不是实际程序的一部分，而是供程序读者使用的文本。

``` haskell
phi :: Double
phi = (sqrt 5 + 1) / 2
```

这是常量 `phi` 的定义，带有随附的*类型标注*（也称为*类型签名*）`phi :: Double`。类型标注意味着 `phi` 具有类型 `Double`。带有等号 (`=`) 的线称为*方程*。`=` 的左侧是我们正在定义的表达式， `=` 的右侧是定义。

一般来说，（函数或常量的）定义由可选的*类型标注*和一个或多个*方程*组成

``` haskell
polynomial :: Double -> Double
polynomial x = x^2 - x - 1
```

这是名为 `polynomial` 的函数的定义。它有一个类型标注和一个方程。请注意函数方程与常数方程的不同之处在于 `=` 符号左侧存在参数 `x`。另请注意， `^` 是 Haskell 中的幂运算符，而不是像许多其他语言中的按位异或。

``` haskell
f x = polynomial (polynomial x)
```

这是名为 `f` 的函数的定义。请注意缺少类型标注。`f` 是什么类型？

``` haskell
main = do
  print (polynomial phi)
  print (f phi)
```

这是对运行程序时发生的情况的描述。它使用 do-syntax 和 IOMonad。我们将在课程的第二部分中回顾这些内容。

## 1.8 使用示例

当你看到这样的示例定义时

``` haskell
polynomial :: Double -> Double
polynomial x = x^2 - x - 1
```

你通常应该尝试一下它。首先运行它。有几种方法可以做到这一点。

如果定义适合一行，你可以在 GHCi 中定义它：

    Prelude> polynomial x = x^2 - x - 1
    Prelude> polynomial 3.0
    5.0

对于多行定义，你可以使用 `;` 来分隔行，或者使用特殊的 `:{ :}` 语法将代码块粘贴到 GHCi 中：

``` haskell
Prelude> :{
Prelude| polynomial :: Double -> Double
Prelude| polynomial x = x^2 - x - 1
Prelude| :}
Prelude> polynomial 3.0
5.0
```

最后，你可以将代码粘贴到新的或现有的 `.hs` 文件中，然后将 `:load` 粘贴到 GHCi 中。如果文件已经加载，你也可以使用 `:reload`。

``` haskell
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

``` haskell
Prelude> "string" ++ True

<interactive>:1:13: error:
    • Couldn't match expected type ‘[Char]’ with actual type ‘Bool’
    • In the second argument of ‘(++)’, namely ‘True’
      In the expression: "string" ++ True
      In an equation for ‘it’: it = "string" ++ True
```

这是最常见的类型错误，“无法匹配预期类型”。尽管该错误看起来又长又吓人，但如果你仔细阅读它，就会发现它非常简单。

- 错误消息的第一行 `<interactive>:1:13: error:` 告诉我们错误发生在 GHCi 中。如果我们加载了一个文件，我们可能会得到类似 `Sandbox.hs:3:17: error:` 的内容，其中 `Sandbox.hs` 是文件名， `3` 是行号， `17` 是行中字符的编号。

- `• Couldn't match expected type ‘[Char]’ with actual type ‘Bool’` 行告诉我们，错误的直接原因是存在 `Bool` 类型的表达式，而 GHCi 期望找到 `[Char]` 类型的表达式。错误消息的第一行指示了此错误的位置。请注意，预期的类型并不总是正确的。手动给出类型标注可以帮助调试键入错误。

- `• In the second argument of ‘(++)’, namely ‘True’` 行告诉我们，类型错误的表达式是运算符 `(++)` 的第二个参数。稍后我们将了解为什么它被括号包围。

- 有错误的完整表达式是 `"string" ++ True`。如上所述， `String` 是 `[Char]`（字符列表类型）的类型别名。`++` 的第一个参数是字符列表，由于 `++` 只能组合相同类型的两个列表，因此第二个参数也应该是 `[Char]` 类型。

- `In an equation for ‘it’: it = "string" ++ True` 行表示该表达式出现在变量 `it` 的定义中，这是 GHCi 用于独立表达式的默认变量名称。如果文件中有一行 `x = "string" ++ True`，或者 GHCi 中有一个声明 `let x = "string" ++ True`，GHCi 将打印 `In an equation for ‘x’: x = "string" ++ True`。

还有其他类型的错误。

``` haskell
Prelude> True + 1

<interactive>:6:1: error:
    • No instance for (Num Bool) arising from a use of ‘+’
    • In the expression: True + 1
      In an equation for ‘it’: it = True + 1
```

当你尝试对非数字的内容使用 `+` 等数字函数时，就会出现这种错误。

最难追踪的错误通常是这样的：

``` haskell
Prelude> True +

<interactive>:10:7: error:
    parse error (possibly incorrect indentation or mismatched brackets)
```

引起它的方法有很多种。可能你在某个地方遗漏了一些字符。我们将在本讲座的稍后部分回到缩进。

### 1.8.2 算术

Haskell 算术中有一件事经常让初学者犯错，那就是除法。

Haskell 中有两个除法函数， `/` 运算符和 `div` 函数。`div` 函数进行整数除法：

``` haskell
Prelude> 7 `div` 2
3
```

`/` 运算符执行通常的除法：

``` haskell
Prelude> 7.0 / 2.0
3.5
```

但是，你只能在整数类型（如 `Int` 和 `Integer`）上使用 `div`，并且只能在小数类型（如 `Double`）上使用 `/`。以下是如果你尝试将它们混合在一起会发生什么情况的示例：

``` haskell
halve :: Int -> Int
halve x = x / 2
```

    error:
        • No instance for (Fractional Int) arising from a use of ‘/’
        • In the expression: x / 2
          In an equation for ‘halve’: halve x = x / 2

现在请记住这一点。我们将回到 `/` 和 `div` 之间的区别，以及 `Num` 和 `Fractional` 在谈论类型类时的含义。

## 1.9 我如何完成任务？

到目前为止，你已经了解了一些算术、反转字符串等等。如何用 Haskell 编写实际的程序？ Haskell 中缺少许多常见的编程结构，例如循环、语句和赋值。接下来，我们将介绍Haskell 程序的基本构建块：

- 条件表达式
- 局部定义
- 模式匹配
- 递归

### 1.9.1 条件表达式

在其他语言中， `if` 是一个*语句*。它没有值，只是有条件地执行其他语句。

在 Haskell 中， `if` 是一个*表达式*。它有一个值。它在其他两个表达式之间进行选择。它对应于C或Java中的 `?:` 运算符。

``` java
// Java
int price = product.equals("milk") ? 1 : 2;
```

Python 的条件表达式与 haskell 的 `if` 非常接近：

``` python
# Python
price = 1 if product == "milk" else 2
```

这是同一示例在 Haskell 中的样子：

``` haskell
price = if product == "milk" then 1 else 2
```

因为 Haskell 的 `if`*返回*一个值，所以你**总是**需要一个 `else`！

#### 1.9.1.1 返回 `Bool` 的函数

为了编写 if 表达式，你需要知道如何获取 `Bool` 类型的值。最常见的方法是比较。通常的 `==`、`<`、`<=`、`>` 和 `>=` 运算符在 Haskell 中工作。你可以对各种数字做大小比较（`<`、`>`），并对几乎所有值进行相等比较（`==`）：

``` haskell
Prelude> "foo" == "bar"
False
Prelude> 5.0 <= 7.2
True
Prelude> 1 == 1
True
```

Haskell 的一个奇怪之处是不等运算符被写为 `/=` 而不是通常的 `!=`：

``` haskell
Prelude> 2 /= 3
True
Prelude> "bike" /= "bike"
False
```

请记住，除了这些比较之外，你还可以使用 `&&`（“与”）和 `||`（“或”）运算符以及 `not` 函数从其他 `Bool` 值中获取 `Bool` 值。

#### 1.9.1.2 示例

``` haskell
checkPassword password = if password == "swordfish"
                         then "You're in."
                         else "ACCESS DENIED!"
```

``` haskell
absoluteValue n = if n < 0 then -n else n
```

``` haskell
login user password = if user == "unicorn73"
                      then if password == "f4bulous!"
                           then "unicorn73 logged in"
                           else "wrong password"
                      else "unknown user"
```

### 1.9.2 局部定义

Haskell 有两种不同的方式来创建局部定义：`let...in` 和 `where`。

`where` 将局部定义添加到定义中：

``` haskell
circleArea :: Double -> Double
circleArea r = pi * rsquare
    where pi = 3.1415926
          rsquare = r * r
```

`let...in` 是一个表达式：

``` haskell
circleArea r = let pi = 3.1415926
                   rsquare = r * r
               in pi * rsquare
```

局部定义也可以是函数：

``` haskell
circleArea r = pi * square r
    where pi = 3.1415926
          square x = x * x
```

``` haskell
circleArea r = let pi = 3.1415926
                   square x = x * x
               in pi * square r
```

我们将回到 `let` 和 `where` 之间的差异，但大多数情况下你可以使用你喜欢的任何一个。

### 1.9.3 关于不变性的一句话

尽管像上面的 `pi` 这样的东西通常被称为“变量”，但我在这里选择将它们称为“定义”。这是因为与 Python 或 Java 中的变量不同，这些定义的值无法更改。 Haskell 变量不是可以放入新值的盒子，Haskell 变量命名一个值（或者更确切地说，一个表达式），仅此而已。

我们将在本课程稍后再次讨论不变性，但现在只要知道这样的事情行不通就足够了。

``` haskell
increment x = let x = x+1
              in x
```

这只是一个无限循环，因为它尝试使用属性 `x = x+1` 定义一个新变量 `x`。因此，在计算 `x` 时，Haskell 只是无限期地继续计算 `1+1+1+1+...`。

``` haskell
compute x = let a = x+1
                a = a*2
            in a
```

    error:
        Conflicting definitions for ‘a’
        Bound at: <interactive>:14:17
                  <interactive>:15:17

当我们尝试“更新”`a` 的值时，我们会得到一个简单的错误。

需要注意的是，局部定义可以“隐藏”其他地方定义的变量名称。阴影不是副作用。相反，隐藏会在更受限制的范围内创建一个新变量，该变量使用与外部范围中的某些变量相同的名称。例如，下面的所有函数 `f`、`g` 和 `h` 都是合法的：

``` haskell
x :: Int
x = 5

f :: Int -> Int
f x = 2 * x

g :: Int -> Int
g y = x where x = 6

h :: Int -> Int
h x = x where x = 3
```

如果我们将它们应用于全局常量 `x`，我们会看到阴影的效果：

``` haskell
f 1 ==> 2
g 1 ==> 6
h 1 ==> 3

f x ==> 10
g x ==> 6
h x ==> 3
```

最好始终为局部变量选择新名称，这样就不会发生阴影。这样，代码的读者将了解表达式中使用的变量来自何处。请注意，在以下示例中， `f` 和 `g` 不会隐藏彼此的参数：

``` haskell
f :: Int -> Int
f x = 2 * x + 1

g :: Int -> Int
g x = x - 2
```

### 1.9.4 模式匹配

（函数的）定义可以由多个*方程*组成。这些方程按照参数的顺序进行匹配，直到找到合适的方程。这称为“模式匹配”。

Haskell 中的模式匹配非常强大，我们将在本课程中不断学习有关它的新知识，但这里有几个第一个示例：

``` haskell
greet :: String -> String -> String
greet "Finland" name = "Hei, " ++ name
greet "Italy"   name = "Ciao, " ++ name
greet "England" name = "How do you do, " ++ name
greet _         name = "Hello, " ++ name
```

函数 `greet` 生成给定国家/地区和名称（均为 `String`）的问候语。它具有针对三个国家的特殊情况和一个默认情况。它是这样工作的：

``` haskell
Prelude> greet "Finland" "Pekka"
"Hei, Pekka"
Prelude> greet "England" "Bob"
"How do you do, Bob"
Prelude> greet "Italy" "Maria"
"Ciao, Maria"
Prelude> greet "Greenland" "Jan"
"Hello, Jan"
```

特殊模式 `_` 可以匹配任何内容。它通常用于默认情况。由于模式是按顺序匹配的，因此（*通常*）将 `_` 案例放在最后非常重要。考虑：

``` haskell
brokenGreet _         name = "Hello, " ++ name
brokenGreet "Finland" name = "Hei, " ++ name
```

现在为所有输入选择第一个案例。

``` haskell
Prelude> brokenGreet "Finland" "Varpu"
"Hello, Varpu"
Prelude> brokenGreet "Sweden" "Ole"
"Hello, Ole"
```

GHC 甚至会向你发出有关此代码的警告：

    <interactive>:1:1: warning: [-Woverlapping-patterns]
        Pattern match is redundant
        In an equation for ‘brokenGreet’: brokenGreet "Finland" name = ...

下面是一些更多的例子。但首先让我们介绍一下标准库函数 `show`，它可以将（几乎！）任何东西转换为字符串：

``` haskell
Prelude> show True
"True"
Prelude> show 3
"3"
```

因此，这是一个具有模式匹配的函数示例和实际使用该值的默认情况（而不是仅仅使用 `_` 忽略它）：

``` haskell
describe :: Integer -> String
describe 0 = "zero"
describe 1 = "one"
describe 2 = "an even prime"
describe n = "the number " ++ show n
```

它是这样工作的：

``` haskell
Prelude> describe 0
"zero"
Prelude> describe 2
"an even prime"
Prelude> describe 7
"the number 7"
```

你甚至可以对多个参数进行模式匹配。再次按顺序尝试方程。以下是之前 `login` 函数的重新实现：

``` haskell
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

``` haskell
factorial :: Int -> Int
factorial 1 = 1
factorial n = n * factorial (n-1)
```

这就是它的工作原理。我们使用 `==>` 来表示“求值到”。

``` haskell
factorial 3
  ==> 3 * factorial (3-1)
  ==> 3 * factorial 2
  ==> 3 * 2 * factorial 1
  ==> 3 * 2 * 1
  ==> 6
```

当你求值 `factorial (-1)` 时会发生什么？

这是另一个例子：

``` haskell
-- compute the sum 1^2+2^2+3^2+...+n^2
squareSum 0 = 0
squareSum n = n^2 + squareSum (n-1)
```

一个函数可以多次递归调用自身。作为一个例子，让我们考虑数学中的“斐波那契数列”。斐波那契数列是具有以下定义的整数序列。

> 该序列从 1, 1 开始。要获取序列的下一个元素，请将序列的前两个元素相加。

斐波那契数列的第一个元素是 1、1、2、3、5、8、13 等。这是一个函数 `fibonacci`，它计算斐波那契序列中的第 `n` 元素。请注意它如何反映数学定义。

``` haskell
-- Fibonacci numbers, slow version
fibonacci 1 = 1
fibonacci 2 = 1
fibonacci n = fibonacci (n-2) + fibonacci (n-1)
```

`fibonacci 5` 的求值方式如下：

``` haskell
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

![](img/Fibonacci-step1.svg)

![](img/Fibonacci-step2.svg)

![](img/Fibonacci-step3.svg)

![](img/Fibonacci-step4.svg)

![](img/Fibonacci-step5.svg)

![](img/Fibonacci-step6.svg)

![](img/Fibonacci-step7.svg)

![](img/Fibonacci-step8.svg)

该树精确地对应于表达式 `(1 + 1) + (1 + (1 + 1))`。递归通常可以产生链状、树状、嵌套或循环结构和计算。递归是函数式编程的主要技术之一，因此值得花一些精力来学习它。

## 1.10 现在大家在一起吧！

最后，这是一个完整的 Haskell 模块，它使用 if、模式匹配、局部定义和递归。该模块对[*Collatz conjecture*](https://en.wikipedia.org/wiki/Collatz_conjecture)感兴趣，这是一个著名的数学开放问题。它问：

> 对于所有正整数初始值，Collat​​z 序列最终是否都达到 1？

Collat​​z序列的定义是，以任意数字为起始值，然后重复执行以下操作：

- 如果数字是偶数，则将其除以二
- 如果数字是奇数，则将其增加三倍并加一

举个例子，3 的 Collat​​z 序列是： 3, 10, 5, 16, 8, 4, 2, 1, 4, 2, 1, 4, 2, 1 … 正如你所看到的，一旦数字达到 1，它就会陷入循环。

``` haskell
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

    $ stack ghci
    GHCi, version 9.2.8: https://www.haskell.org/ghc/  :? for help
    Prelude> :load Collatz.hs
    [1 of 1] Compiling Collatz          ( Collatz.hs, interpreted )
    Ok, one module loaded.
    *Collatz>

让我们验证一下我们的程序是否正确计算了 3 的 Collat​​z 序列的开头。

    *Collatz> step 3
    10
    *Collatz> step 10
    5
    *Collatz> step 5
    16

3需要多少步才能到达1？

    *Collatz> collatz 3
    7

起始值小于 10 时，最长的 Collat​​z 序列是多少？ 100 呢？

    *Collatz> longest 10
    9
    *Collatz> longest 100
    97

这些 Collat​​z 序列的长度为：

    *Collatz> collatz 9
    19
    *Collatz> collatz 97
    118

## 1.11 关于缩进

前面的例子已经被巧妙地缩进了。在 Haskell 中缩进很重要，有点像在 Python 中。完整的缩进规则很难描述，但你应该能够很好地遵循这些经验规则：

1.  分组在一起的事物从同一列开始
2.  如果必须将表达式（或方程）拆分为多行，请增加缩进

虽然你可以不使用制表符，但强烈建议在所有缩进中使用空格。

一些例子是有序的。

这些都可以：

``` haskell
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

``` haskell
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

    Indent.hs:2:1: error: parse error on input ‘where’

该错误包括行号，因此只需再次检查该行即可。如果你似乎无法让缩进发挥作用，请尝试首先将所有内容都放在一根长线上。

## 1.12 测验

在每堂课结束时，你都会发现这样的测验。测验不评分，只是为了帮助你检查你是否理解了本章。你可以通过单击选项来检查你的答案。如果你是对的，你会看到绿色背景，如果你错了，你会看到红色背景。你可以随意猜测多次，只要确保你明白为什么正确的选项最终是正确的。

C/Java/Python 表达式 `combine(prettify(lawn),construct(house,concrete))` 的 Haskell 等价物是什么？

1.  `combine prettify (lawn) construct (house concerete)`
2.  `combine (prettify lawn (counstruct house concrete))`
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

为什么 `3 * "F00"` 无效 Haskell？

1.  `3` 和 `"F00"` 有不同的类型
2.  所有数值都需要小数点
3.  `"F00"` 需要前缀“0x”

为什么``7.0`div`2``会出错？

1.  因为 `div` 没有为类型 `Double` 定义
2.  因为 `div` 没有为类型 `Int` 定义
3.  因为```...```用于分隔字符串。

## 1.13 练习

课程材料（包括练习）可在 GitHub 上的 Git 存储库（<https://github.com/moocfi/haskell-mooc>）中获取。如果你不熟悉 Git，请参阅[GitHub’s instructions on cloning a repository](https://help.github.com/en/github/creating-cloning-and-archiving-repositories/cloning-a-repository)。

克隆 `haskell-mooc` 存储库后，进入 `exercises` 目录。要下载并构建运行练习测试所需的依赖项（例如正确版本的 GHC 和各种库），请在终端中运行以下命令：

``` sh
$ stack build
```

请注意，依赖项有多个千兆字节，命令需要一段时间才能完成。

**注意！** 以下是 `stack build` 常见问题的一些修复：

- 如果出现类似 `While building package zlib-0.6.2.3` 的错误，则需要安装 zlib 库标头。 Ubuntu 的正确命令是 `sudo apt install zlib1g-dev`。
- 如果你收到类似 `Downloading lts-18.18 build plan ... RedownloadInvalidResponse` 的错误，则说明你的堆栈版本太旧。运行 `stack upgrade` 以获得更新的版本。

`exercises` 目录中主要有两种类型的文件：名为 `SetNX.hs` 的练习集和名为 `SetNXTest.hs` 的练习的随附测试程序。两者都是Haskell源文件，但做习题时只需编辑练习文件。所有单独练习的说明都作为注释嵌入到练习文件中。

使用测试文件检查你的答案。例如，当你解决了 `Set1.hs` 中的一些练习时，请运行以下命令：

``` sh
$ stack runhaskell Set1Test.hs
```

测试的输出看起来像这样：

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

在上面的例子中，我在练习 3 中犯了一个错误。

为了使调试更快、更直接，我可以在 GHCi 中加载练习文件，这使我能够手动求值任何顶级函数。例如我可以通过以下方式验证上述错误：

    $ stack ghci Set1.hs
    GHCi, version 9.2.8: https://www.haskell.org/ghc/  :? for help
    [1 of 2] Compiling Mooc.Todo        ( Mooc/Todo.hs, interpreted )
    [2 of 2] Compiling Set1             ( Set1.hs, interpreted )
    Ok, two modules loaded.
    *Set1> quadruple 1
    2

完成一组练习后，你可以将其交到课程页面上的[Submit page](https://haskell.mooc.fi/submit.php)上。之后你可以在[Results page](https://haskell.mooc.fi/results.php)上看到你提交的结果以及在[My status page](https://haskell.mooc.fi/status.php)上看到你的总分。

**注意！** 你可以根据需要多次上交练习集。

**注意！** 如果你不想使用 Stack 或无法使其工作，你还应该能够使用[Cabal](https://www.haskell.org/cabal/download.html)运行测试，如下所示：

``` sh
$ cabal v2-build
$ cabal v2-exec runhaskell Set1Test.hs
```

### 1.13.1 模型解

成功完成一组练习的所有练习后，你可以在[My status page](https://haskell.mooc.fi/status)上查看模型解决方案。浏览一下模型解决方案很有用，它们可能会向你展示你错过的技术！

## 1.14 练习

- [Set1](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set1.hs)

# 2 第 2 讲：要么作为英雄而死……

- 有关递归的更多信息
- 守卫
- 更多类型：列表、 `Maybe`、`Either`
- 多态性

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

## 2.6`Maybe` 类型

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

## 2.7 附注：构造函数

正如你在上面所看到的，我们可以在 `Maybe` 的构造函数上进行模式匹配：`Just` 和 `Nothing`。稍后我们会回到构造函数的含义。现在只要注意到构造函数是以大写字母开头的特殊值就足够了，你可以对其进行模式匹配。

我们已经见过的其他构造函数包括 `Bool` 的构造函数 -`True` 和 `False`。我们将在下一讲介绍列表类型的构造函数。

构造函数可以像 Haskell 值一样使用。像 `Nothing` 和 `False` 这样不带参数的构造函数只是常量。像 `Just` 这样带有参数的构造函数的行为类似于函数。它们甚至还有函数类型！

    Prelude> :t Just
    Just :: a -> Maybe a

## 2.8`Either` 类型

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

## 2.12 练习

- [Set2a](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set2a.hs)
- [Set2b](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set2b.hs)

# 3 第 3 讲：Catamorphic

- 列表，列表，列表
- 函数式编程
- 关于类型的一些知识

## 3.1 最后的函数式编程

现在有了我们工具箱中的列表和多态性，我们终于可以开始研究函数式编程了。

在 Haskell 中，函数是一个值，就像数字或列表一样。函数可以作为参数传递给其他函数。这是一个玩具示例。函数 `applyTo1` 接受类型为 `Int->Int` 的函数，将其应用于数字 `1`，并返回结果。

``` haskell
applyTo1 :: (Int -> Int) -> Int
applyTo1 f = f 1
```

让我们定义一个 `Int->Int` 类型的简单函数，并看看 `applyTo1` 的实际效果。

``` haskell
addThree :: Int -> Int
addThree x = x + 3
```

``` haskell
applyTo1 addThree
  ==> addThree 1
  ==> 1 + 3
  ==> 4
```

让我们回到 `applyTo1` 的类型标注。

``` haskell
applyTo1 :: (Int -> Int) -> Int
```

需要括号是因为类型 `Int -> Int -> Int` 将是采用两个 `Int` 参数的函数的类型。稍后会详细介绍这一点。

让我们看一个稍微有趣的例子。这次我们将实现一个多态函数 `doTwice`。请注意我们如何将它与各种类型的值和函数一起使用。

``` haskell
doTwice :: (a -> a) -> a -> a
doTwice f x = f (f x)
```

``` haskell
doTwice addThree 1
  ==> addThree (addThree 1)
  ==> 7
doTwice tail "abcd"
  ==> tail (tail "abcd")
  ==> "cd"
```

``` haskell
makeCool :: String -> String
makeCool str = "WOW " ++ str ++ "!"
```

``` haskell
doTwice makeCool "Haskell"
  ==> "WOW WOW Haskell!!"
```

### 3.1.1 列表上的函数式编程

那有点无聊。幸运的是，有许多有用的列表函数以函数作为参数。顺便说一句，以函数作为参数（或返回函数）的函数通常称为“高阶函数”。

这些处理列表的高阶函数里，最有名的是 `map`。它会把给定函数应用到列表的每个元素上，并返回一个新列表。

``` haskell
map :: (a -> b) -> [a] -> [b]
```

``` haskell
map addThree [1,2,3]
  ==> [4,5,6]
```

`filter` 常常和 `map` 搭配使用。`filter` 不是转换列表的所有元素，而是删除列表的一些元素并保留其他元素。换句话说， `filter` 从列表中选择满足条件的元素。

``` haskell
filter :: (a -> Bool) -> [a] -> [a]
```

这是一个例子：从列表中选择正数元素

``` haskell
positive :: Int -> Bool
positive x = x>0
```

``` haskell
filter positive [0,1,-1,3,-3]
  ==> [1,3]
```

请注意 `map` 和 `filter` 的类型签名如何使用多态性。它们适用于各种列表。`map` 的类型甚至使用了两个类型参数！以下是使用 `map` 和 `filter` 进行类型推断的一些示例。

``` haskell
onlyPositive xs = filter positive xs
mapBooleans f = map f [False,True]
```

``` haskell
Prelude> :t onlyPositive
onlyPositive :: [Int] -> [Int]
Prelude> :t mapBooleans
mapBooleans :: (Bool -> b) -> [b]
Prelude> :t mapBooleans not
mapBooleans not :: [Bool]
```

还有一件事：还记得构造函数只是函数吗？这表示你可以将它们作为参数传递给其他函数！

``` haskell
wrapJust xs = map Just xs
```

``` haskell
Prelude> :t wrapJust
wrapJust :: [a] -> [Maybe a]
Prelude> wrapJust [1,2,3]
[Just 1,Just 2,Just 3]
```

### 3.1.2 列表函数式编程示例

`1` 和 `n` 之间有多少个“回文数”？

``` haskell
-- a predicate that checks if a string is a palindrome
palindrome :: String -> Bool
palindrome str = str == reverse str

-- palindromes n takes all numbers from 1 to n, converts them to strings using show, and keeps only palindromes
palindromes :: Int -> [String]
palindromes n = filter palindrome (map show [1..n])
```

``` haskell
palindrome "1331" ==> True
palindromes 150 ==>
  ["1","2","3","4","5","6","7","8","9",
   "11","22","33","44","55","66","77","88","99",
   "101","111","121","131","141"]
length (palindromes 9999) ==> 198
```

字符串中有多少个以“a”开头的单词？这使用模块 `Data.List` 中的函数 `words` 将字符串拆分为单词。

``` haskell
countAWords :: String -> Int
countAWords string = length (filter startsWithA (words string))
  where startsWithA s = head s == 'a'
```

``` haskell
countAWords "does anyone want an apple?"
  ==> 3
```

`Data.List` 中的函数 `tails` 返回列表的所有后缀（“尾部”）的列表。我们可以使用 `tails` 来完成许多字符串处理任务。`tails` 的工作原理如下：

``` haskell
tails "echo"
  ==> ["echo","cho","ho","o",""]
```

这是一个示例，我们可以查找字符串中给定字符之后的字符。首先，我们使用 `tails`、`map` 和 `take` 来获取一定长度的所有子串：

``` haskell
substringsOfLength :: Int -> String -> [String]
substringsOfLength n string = map shorten (tails string)
  where shorten s = take n s
```

``` haskell
substringsOfLength 3 "hello"
  ==> ["hel","ell","llo","lo","o",""]
```

最后还剩下一些较短的子字符串（你能明白为什么吗？），但它们现在就可以满足我们的目的。现在我们有了 `substringsOfLength`，我们可以实现函数 `whatFollows c k s` 来查找字符串 `s` 中出现的所有字符 `c`，并输出这些出现之后的 `k` 字母。

``` haskell
whatFollows :: Char -> Int -> String -> [String]
whatFollows c k string = map tail (filter match (substringsOfLength (k+1) string))
  where match sub = take 1 sub == [c]
```

``` haskell
whatFollows 'a' 2 "abracadabra"
  ==> ["br","ca","da","br",""]
```

## 3.2 偏应用

使用高阶函数时，你会发现自己定义了许多小辅助函数，例如前面示例中的 `addThree` 或 `shorten`。从长远来看，这有点麻烦，但幸运的是 Haskell 的函数表现得有点奇怪……

让我们从 GHCi 开始：

``` haskell
Prelude> add a b = a+b
Prelude> add 1 5
6
Prelude> addThree = add 3
Prelude> addThree 2
5
```

因此，我们定义了 `add`，一个有两个参数的函数，并且只给它一个参数。结果不是类型错误而是新函数。新函数只是存储（或记住）给定的参数，等待另一个参数，然后将两者都提供给 `add`。

    Prelude> map addThree [1,2,3]
    [4,5,6]
    Prelude> map (add 3) [1,2,3]
    [4,5,6]

在这里我们可以看到，我们甚至不需要为 `add 3` 返回的函数命名。我们可以在任何需要一个参数的函数的地方使用它。

这称为“偏应用”。 Haskell 中的所有函数都是这样的。让我们仔细看看。这是一个带有许多参数的函数。

``` haskell
between :: Integer -> Integer -> Integer -> Bool
between lo high x = x < high && x > lo
```

``` haskell
Prelude> between 3 7 5
True
Prelude> between 3 6 8
False
```

我们可以给 `between` 更少的参数并返回新函数，就像我们在 `add` 中看到的那样：

``` haskell
Prelude> (between 1 5) 2
True
Prelude> let f = between 1 5 in f 2
True
Prelude> map (between 1 3) [1,2,3]
[False,True,False]
```

查看偏应用 `between` 的类型。它们的行为很整齐，随着值被添加到表达式中，参数从类型中一一消失。

``` haskell
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

``` haskell
map (drop 1) ["Hello","World!"]
  ==> ["ello","orld!"]
```

除了普通函数外，运算符也可以偏应用。使用运算符时，你可以选择固定左参数还是右参数。（偏应用的运算符也称为 *section* 或*运算符 section*。）一些例子：

``` haskell
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

``` haskell
(+) 1 2 ==> 1 + 2 ==> 3
```

这在需要将运算符作为参数传递给另一个函数时尤其有用。

例如，函数 `zipWith` 接受一个二元函数和两个列表，并用这个函数把两个列表逐个元素组合起来。我们可以使用 `zipWith (+)` 对两个列表逐个元素求和：

``` haskell
Prelude> :t zipWith
zipWith :: (a -> b -> c) -> [a] -> [b] -> [c]
Prelude> zipWith (+) [0,2,5] [1,3,3]
[1,5,8]
```

如果无法将运算符转换为函数，我们就必须使用辅助函数——例如上面的 `add`。

请注意，省略括号会导致类型错误：

``` haskell
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

``` haskell
6 `div` 2 ==> div 6 2 ==> 3
(+1) `map` [1,2,3] ==> map (+1) [1,2,3] ==> [2,3,4]
```

## 3.4 lambda 表达式

函数式编程工具箱里最后还需要一个工具：λ（lambda）。lambda 表达式是*匿名函数*。考虑这样一种情况：你只需要临时使用一个函数，例如下面这个表达式：

``` haskell
let big x = x>7 in filter big [1,10,100]
```

lambda 表达式允许我们直接编写它，而无需为辅助函数定义名称 (`big`)：

``` haskell
filter (\x -> x>7) [1,10,100]
```

以下是 GHCi 中的更多示例：

``` haskell
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

``` haskell
(.) :: (b -> c) -> (a -> b) -> a -> c
```

这就是它的作用

    (f.g) x ==> f (g x)

你可以使用函数组合从其他函数构建函数，而无需提及任何参数。例如：

``` haskell
double x = 2*x
quadruple = double . double  -- computes 2*(2*x) == 4*x
f = quadruple . (+1)         -- computes 4*(x+1)
g = (+1) . quadruple         -- computes 4*x+1
third = head . tail . tail   -- fetches the third element of a list
```

我们还可以使用 `(.)` 重新实现 `doTwice`。请注意我们如何使用 `doTwice` 既可以仅应用于函数，也可以应用于函数和值。

``` haskell
doTwice :: (a -> a) -> a -> a
doTwice f = f . f
```

``` haskell
let ttail = doTwice tail
in ttail [1,2,3,4]
  ==> [3,4]

(doTwice tail) [1,2,3,4] ==> [3,4]

doTwice tail [1,2,3,4] ==> [3,4]
```

定义新函数时通常不使用函数组合，而是为了避免定义辅助函数。例如，考虑这两个表达式之间的差异：

``` haskell
let notEmpty x = not (null x)
in filter notEmpty [[1,2,3],[],[4]]
  ==> [[1,2,3],[4]]
```

``` haskell
filter (not . null) [[1,2,3],[],[4]]
  ==> [[1,2,3],[4]]
```

另一个运算符 `$` 更加微妙。我们来看看它的类型。

``` haskell
($) :: (a -> b) -> a -> b
```

它采用 `a -> b` 类型的函数和 `a` 类型的值，并返回 `b` 类型的值。换句话说，它是一个函数应用运算符。表达式 `f $ x` 与 `f x` 相同。这看起来没什么用，但这表示 `$` 运算符可以用来消除括号！这些表达式是相同的：

``` haskell
head (reverse "abcd")
head $ reverse "abcd"
```

当它用于消除一对括号时，这并不令人印象深刻，但 `.` 和 `$` 一起可以消除很多括号！例如我们可以重写

``` haskell
reverse (map head (map reverse (["Haskell","pro"] ++ ["dodo","lyric"])))
```

作为

``` haskell
(reverse . map head . map reverse) (["Haskell","pro"] ++ ["dodo","lyric"])
```

进而

``` haskell
reverse . map head . map reverse $ ["Haskell","pro"] ++ ["dodo","lyric"]
```

有时，运算符 `.` 和 `$` 本身作为函数很有用。例如，可以使用 map 和 `$` 的一部分将函数列表应用到参数：

``` haskell
map ($"string") [reverse, take 2, drop 2]
  ==> [reverse $ "string", take 2 $ "string", drop 2 $ "string"]
  ==> [reverse "string", take 2 "string", drop 2 "string"]
  ==> ["gnirts", "st", "ring"]
```

如果这看起来很复杂，请不要担心。在你熟悉 `.` 和 `$` 之前，你不需要在自己的代码中使用它们。然而，当你在互联网上阅读 Haskell 示例和代码时，你会遇到 `.` 和 `$`，因此了解它们是有好处的。[This article](https://typeclasses.com/featured/dollar)也可能有帮助。

## 3.6 示例：重写 `whatFollows`

现在，让我们使用刚刚看到的工具重写之前的 `whatFollows` 示例。这是原始版本：

``` haskell
substringsOfLength :: Int -> String -> [String]
substringsOfLength n string = map shorten (tails string)
  where shorten s = take n s

whatFollows :: Char -> Int -> String -> [String]
whatFollows c k string = map tail (filter match (substringsOfLength (k+1) string))
  where match sub = take 1 sub == [c]
```

首先，让我们摆脱辅助函数 `substringsOfLength` 并将所有代码移至 `whatFollows`：

``` haskell
whatFollows c k string = map tail (filter match (map shorten (tails string)))
  where shorten s = take (k+1) s
        match sub = take 1 sub == [c]
```

现在让我们使用偏应用而不是定义 `shorten`：

``` haskell
whatFollows c k string = map tail (filter match (map (take (k+1)) (tails string)))
  where match sub = take 1 sub == [c]
```

让我们使用 `.` 和 `$` 来消除其中一些括号：

``` haskell
whatFollows c k string = map tail . filter match . map (take (k+1)) $ tails string
  where match sub = take 1 sub == [c]
```

我们还可以用 lambda 替换 `match`：

``` haskell
whatFollows c k string = map tail . filter (\sub -> take 1 sub == [c]) . map (take (k+1)) $ tails string
```

最后，我们根本不需要提及 `string` 参数，因为我们可以将 `whatFollows` 表示为 `map`、`filter`、`map` 和 `tails` 的组合：

``` haskell
whatFollows c k = map tail . filter (\sub -> take 1 sub == [c]) . map (take (k+1)) . tails
```

我们甚至可以更进一步，使用运算符部分重写 lambda

``` haskell
    \sub -> take 1 sub == [c]
=== \sub -> (==[c]) (take 1 sub)
=== \sub -> (==[c]) ((take 1) sub)
=== \sub -> ((==[c]) . (take 1)) sub
=== ((==[c]) . (take 1))
=== ((==[c]) . take 1)
```

现在我们剩下的是：

``` haskell
whatFollows c k = map tail . filter ((==[c]) . take 1) . map (take (k+1)) . tails
```

这是该函数的一个有点极端的版本，但是当适度使用时，此处显示的技术可以使代码更易于阅读。

## 3.7 更多函数式列表处理示例

以下是一些使用列表进行函数式编程的更多示例。让我们首先介绍几个新的列表函数：

``` haskell
takeWhile :: (a -> Bool) -> [a] -> [a]   -- take elements from a list as long as they satisfy a predicate
dropWhile :: (a -> Bool) -> [a] -> [a]   -- drop elements from a list as long as they satisfy a predicate
```

``` haskell
takeWhile even [2,4,1,2,3]   ==> [2,4]
dropWhile even [2,4,1,2,3]   ==> [1,2,3]
```

还有函数 `elem`，可用于检查列表是否包含元素：

``` haskell
elem 3 [1,2,3]   ==> True
elem 4 [1,2,3]   ==> False
```

使用这些，我们可以实现一个函数 `findSubstring`，该函数查找仅由给定字符组成的字符串中最早和最长的子字符串。

``` haskell
findSubstring :: String -> String -> String
findSubstring chars = takeWhile (\x -> elem x chars)
                      . dropWhile (\x -> not $ elem x chars)
```

``` haskell
findSubstring "a" "bbaabaaaab"              ==> "aa"
findSubstring "abcd" "xxxyyyzabaaxxabcd"    ==> "abaa"
```

函数 `zipWith` 允许你逐个元素地组合两个列表：

``` haskell
zipWith :: (a -> b -> c) -> [a] -> [b] -> [c]
```

``` haskell
zipWith (++) ["John","Mary"] ["Smith","Cooper"]
  ==> ["JohnSmith","MaryCooper"]
zipWith take [4,3] ["Hello","Warden"]
  ==> ["Hell","War"]
```

有时，对于高阶函数，拥有一个不执行任何操作的函数会很有用。函数 `id :: a -> a` 是恒等函数，仅返回其参数。

``` haskell
id 3 ==> 3
map id [1,2,3] ==> [1,2,3]
```

这似乎有点无用，但你可以将其与 `filter` 或 `dropWhile` 一起使用：

``` haskell
filter id [True,False,True,True]  ==>  [True,True,True]
dropWhile id [True,True,False,True,False]  ==>  [False,True,False]
```

另一个非常简单但有时至关重要的函数是常量函数 `const :: a -> b -> a`。它总是返回它的第一个参数：

``` haskell
const 3 True ==> 3
const 3 0    ==> 3
```

当偏应用时，当你需要一个始终返回相同值的函数时，可以使用它：

``` haskell
map (const 5) [1,2,3,4] ==> [5,5,5,5]
filter (const True) [1,2,3,4] ==> [1,2,3,4]
```

## 3.8 列表和递归

这是一个新的运算符， `:`

``` haskell
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

### 3.8.1 建立列表

使用 `:` 我们可以定义构建列表的递归函数。例如，这是一个构建类似 `[3,2,1]` 的列表的函数：

``` haskell
descend 0 = []
descend n = n : descend (n-1)
```

``` haskell
descend 4 ==> [4,3,2,1]
```

这是一个通过迭代函数 `n` 次来构建列表的函数：

``` haskell
iterate f 0 x = [x]
iterate f n x = x : iterate f (n-1) (f x)
```

``` haskell
iterate (*2) 4 3 ==> [3,6,12,24,48]

let xs = "terve"
in iterate tail (length xs) xs
  ==> ["terve","erve","rve","ve","e",""]
```

这是一个更复杂的示例：在给定字符处将字符串拆分为多个片段：

``` haskell
split :: Char -> String -> [String]
split c [] = []
split c xs = start : split c (drop 1 rest)
  where start = takeWhile (/=c) xs
        rest = dropWhile (/=c) xs
```

``` haskell
split 'x' "fooxxbarxquux"   ==>   ["foo","","bar","quu"]
```

### 3.8.2 列表的模式匹配

上一讲，有人说构造函数是可以进行模式匹配的东西。上面透露了列表类型的构造函数是 `:` 和 `[]`。我们可以将一加一放在一起，猜测我们可以在 `:` 和 `[]` 上进行模式匹配。这是真实的！以下是如何使用模式匹配定义自己的 `head` 和 `tail` 版本：

``` haskell
myhead :: [Int] -> Int
myhead [] = -1
myhead (first:rest) = first

mytail :: [Int] -> [Int]
mytail [] = []
mytail (first:rest) = rest
```

你可以*嵌套*模式。也就是说，你可以从列表开头开始对多个元素进行模式匹配。在此示例中，我们使用与 `(a:(b:_))` 相同的模式 `(a:b:_)`：

``` haskell
sumFirstTwo :: [Integer] -> Integer
-- this equation gets used for lists of length at least two
sumFirstTwo (a:b:_) = a+b
-- this equation gets used for all other lists (i.e. lists of length 0 or 1)
sumFirstTwo _       = 0
```

``` haskell
sumFirstTwo [1]      ==> 0
sumFirstTwo [1,2]    ==> 3
sumFirstTwo [1,2,4]  ==> 3
```

这是一个使用许多不同列表模式的示例：

``` haskell
describeList :: [Int] -> String
describeList []         = "an empty list"
describeList (x:[])     = "a list with one element"
describeList (x:y:[])   = "a list with two elements"
describeList (x:y:z:xs) = "a list with at least three elements"
```

``` haskell
describeList [1,3]        ==> "a list with two elements"
describeList [1,2,3,4,5]  ==> "a list with at least three elements"
```

以 `:[]` 结尾的列表模式可以作为列表文字键入。也就是说，就像 `[1,2,3]` 与 `1:2:3:[]` 的值相同一样，模式 `[x,y]` 与模式 `x:y:[]` 相同。让我们重写前面的例子。

``` haskell
describeList :: [Int] -> String
describeList []         = "an empty list"
describeList [x]        = "a list with exactly one element"
describeList [x,y]      = "a list with exactly two elements"
describeList (x:y:z:xs) = "a list with at least three elements"
```

嵌套模式的另一种方法是在头部进行模式匹配，而在列表上进行模式匹配。例如，此函数检查列表是否以 `0` 开头：

``` haskell
startsWithZero :: [Integer] -> Bool
startsWithZero (0:xs) = True
startsWithZero (x:xs) = False
startsWithZero []     = False
```

### 3.8.3 使用列表

使用模式匹配和递归，我们可以递归地处理整个列表。以下是对列表中所有数字求和的方法：

``` haskell
sumNumbers :: [Int] -> Int
sumNumbers [] = 0
sumNumbers (x:xs) = x + sumNumbers xs
```

以下是计算列表中最大数字的方法，这次使用辅助函数。

``` haskell
myMaximum :: [Int] -> Int
myMaximum [] = 0       -- actually this should be some sort of error...
myMaximum (x:xs) = go x xs
  where go biggest [] = biggest
        go biggest (x:xs) = go (max biggest x) xs
```

**注意！**，“`go`”只是这里辅助函数的一个可爱的名字。这不是特殊的语法。

在使用列表时使用嵌套模式通常很方便。下面是一个计算 `Maybe` 列表中出现了多少个 `Nothing` 值的示例：

``` haskell
countNothings :: [Maybe a] -> Int
countNothings [] = 0
countNothings (Nothing : xs) = 1 + countNothings xs
countNothings (Just _  : xs) = countNothings xs
```

``` haskell
countNothings [Nothing,Just 1,Nothing]  ==>  2
```

### 3.8.4 构建和使用列表

现在我们可以构建和使用列表了，让我们同时进行这两个操作。此函数将列表中的所有元素加倍。

``` haskell
doubleList :: [Int] -> [Int]
doubleList [] = []
doubleList (x:xs) = 2*x : doubleList xs
```

它的求值如下：

``` haskell
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

``` haskell
map :: (a -> b) -> [a] -> [b]
map _ []     = []
map f (x:xs) = f x : map f xs
```

和[here’s filter](https://hackage.haskell.org/package/base-4.16.4.0/docs/src/GHC.List.html#filter)：

``` haskell
filter :: (a -> Bool) -> [a] -> [a]
filter _pred []    = []
filter pred (x:xs)
  | pred x         = x : filter pred xs
  | otherwise      = filter pred xs
```

（**注意！** 将参数命名为 `_pred` 是一种告诉代码读者该参数未使用的方法。也可以只是 `_`。）

### 3.8.5 尾递归和列表

当递归函数的计算结果是对具有不同参数的同一函数的新调用时，它被称为“尾递归”。 （递归调用被称为位于*尾部位置*。）这是与命令式循环相对应的递归类型。我们已经看到了许多尾递归函数的示例，但我们还没有真正比较编写同一函数的两种方法。这是本讲座前面的 `sumNumbers`：

``` haskell
-- Not tail recursive!
sumNumbers :: [Int] -> Int
sumNumbers [] = 0
sumNumbers (x:xs) = x + sumNumbers xs
```

在第二个方程中，函数 `+` 位于顶层，即位于尾部位置。对 `sumNumbers` 的递归调用是 `+` 的参数。这是使用尾递归辅助函数编写的 `sumNumbers`：

``` haskell
-- Tail recursive version
sumNumbers :: [Int] -> Int
sumNumbers xs = go 0 xs
  where go sum [] = sum
        go sum (x:xs) = go (sum+x) xs
```

请注意 `go` 的第二个方程：它在顶层（即尾部位置）递归调用 `go`。`+` 现在处于 `go` 的争论中。

对于像 `sumNumbers` 这样产生单个值（数字）的函数，选择哪种形式的递归并不重要。非尾递归函数更容易阅读，而尾递归函数更容易想出。你可以尝试用两种方式编写函数。尾递归形式可能更有效，但这取决于许多细节。我们将在本课程的第二部分中更多地讨论 Haskell 性能。

但是，当你返回列表时，这两种形式之间存在很大差异。考虑之前的函数 `doubleList`。又是这样，首先直接实现，然后通过尾递归辅助函数实现。

``` haskell
-- Not tail recursive!
doubleList :: [Int] -> [Int]
doubleList [] = []
doubleList (x:xs) = 2*x : doubleList xs
```

``` haskell
-- Tail recursive version
doubleList :: [Int] -> [Int]
doubleList xs = go [] xs
    where go result [] = result
          go result (x:xs) = go (result++[2*x]) xs
```

在这里，直接版本效率更高。`(:)` 运算符以恒定时间工作，而 `(++)` 运算符需要遍历整个列表，需要线性时间。因此，直接版本使用相对于列表长度的线性时间 (*O(n)*)，而尾递归版本是二次的 (*O(n²)*)！

人们可能会想通过在尾递归版本中使用 `(:)` 来解决此问题，但随后列表将以相反的顺序生成。这可以通过应用 `reverse` 来解决，但这会使生成的函数变得相当复杂。

更喜欢直接版本还有另一个原因：懒惰。我们将在课程的第 2 部分中回到惰性，但现在你知道**生成列表的直接方法更简单、更高效且更惯用**就足够了。你应该尝试在练习中练习它。查看上面 `map` 和 `filter` 的标准库实现，即使它们直接生成列表而没有尾递归！

## 3.9 有趣的事情：列表推导式

Haskell 有*列表推导式*，这是一种很好的定义列表的语法，结合了 `map` 和 `filter` 的强大功能。你可能已经熟悉 Python 的列表推导式。 Haskell 的工作方式几乎相同，但它们的语法有点不同。

映射：

``` haskell
[2*i | i<-[1,2,3]]
  ==> [2,4,6]
```

过滤：

``` haskell
[i | i <- [1..7], even i]
  ==> [2,4,6]
```

一般来说，这两种形式是等价的：

``` haskell
[f x | x <- lis, p x]
map f (filter p lis)
```

列表推导式可以做更多的事情。你可以迭代多个列表：

``` haskell
[ first ++ " " ++ last | first <- ["John", "Mary"], last <- ["Smith","Cooper"] ]
  ==> ["John Smith","John Cooper","Mary Smith","Mary Cooper"]
```

你可以进行局部定义：

``` haskell
[ reversed | word <- ["this","is","a","string"], let reversed = reverse word ]
  ==> ["siht","si","a","gnirts"]
```

你甚至可以在列表推导中进行模式匹配！

``` haskell
firstLetters string = [ char | (char:_) <- words string ]
```

``` haskell
firstLetters "Hello World!"
  ==> "HW"
```

## 3.10 有趣的事情：自定义运算符

在 Haskell 中，*operator* 是由字符 `!#$%&*+./<=>?@\^|-~` 构建的任何东西。运算符可以像函数一样定义（注意类型标注略有不同）：

``` haskell
(<+>) :: [Int] -> [Int] -> [Int]
xs <+> ys = zipWith (+) xs ys
```

``` haskell
(+++) :: String -> String -> String
a +++ b = a ++ " " ++ b
```

## 3.11 有用的东西：类型孔

有时，在编写 Haskell 时，找到具有正确类型的表达式可能很棘手。幸运的是，编译器可以在这里帮助你！名为“Typed Holes”的功能允许你在代码中保留空白，编译器会告诉你空白中的表达式应具有什么类型。

空白可能看起来像 `_` 或 `_name`。它们可能与“一切皆有可能”模式 `_` 相混淆，但不同之处在于，孔发生在 `=` 的“右侧”，而“一切皆有可能”模式发生在 `=` 的“左侧”。

让我们从 GHCi 中的一个简单示例开始：

``` haskell
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

    <interactive>: error:
        • Found hole: _hole :: Bool -> Bool

错误消息的其余部分提供了有关 `_hole` 值的一些建议，例如 `id` 和 `not`。

让我们看一个更长的示例，其中我们尝试实现一个使用布尔值列表过滤列表的函数：

``` haskell
keepElements [5,6,7,8] [True,False,True,False] ==> [5,7]
```

我们将从 `zip` 开始，因为我们知道这可以很好地配对两个列表的元素。我们添加一个类型化孔 `_doIt` 并用 `zip` 的结果调用它，看看接下来需要做什么。

``` haskell
keepElements :: [a] -> [Bool] -> [a]
keepElements xs bs = _doIt (zip xs bs)
```

``` haskell
<interactive>: error:
    • Found hole: _doIt :: [(a, Bool)] -> [a]
    ...
```

这看起来可以用 `map` 来完成。让我们看看会发生什么：

``` haskell
keepElements :: [a] -> [Bool] -> [a]
keepElements xs bs = map _f (zip xs bs)
```

``` haskell
<interactive>: error:
    • Found hole: _f :: (a, Bool) -> a
    ...
      Valid hole fits include
        fst :: forall a b. (a, b) -> a
```

伟大的！ GHC 让我们想起了函数 `fst`，它可以从一对中取出第一个。我们现在完成了吗？

``` haskell
keepElements :: [a] -> [Bool] -> [a]
keepElements xs bs = map fst (zip xs bs)
```

``` haskell
Prelude> keepElements [5,6,7,8] [True,False,True,False]
[5,6,7,8]
```

哦，对了，我们忘记做过滤部分了。让我们再次尝试输入孔：

``` haskell
keepElements :: [a] -> [Bool] -> [a]
keepElements xs bs = map fst (filter _predicate (zip xs bs))
```

``` haskell
<interactive>: error:
    • Found hole: _predicate :: (a, Bool) -> Bool
    ...
      Valid hole fits include
        snd :: forall a b. (a, b) -> b
        ...
        ... lots of other suggestions
```

GHC 再次提醒我们一个似乎做正确事情的函数：只需从元组中获取第二个元素。现在我们的函数已经完成并按预期工作。

``` haskell
keepElements :: [a] -> [Bool] -> [a]
keepElements xs bs = map fst (filter snd (zip xs bs))
```

``` haskell
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

- [Set3a](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set3a.hs)：正常列表练习
- [Set3b](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set3b.hs)：列出递归练习

### 3.13.1 常见错误

``` haskell
No instance for (Eq a) arising from a use of ‘==’
```

你可能尝试过使用 `x==Nothing` 来检查值是否为 `Nothing`。请改用模式匹配。出现此错误的原因是无法比较 `Maybe a` 类型的值，因为 Haskell 不知道如何比较多态类型 `a` 的值。你将在下一讲中找到更多相关内容。现在使用模式匹配而不是 `==`。

# 4 第 4 讲：真正的类型类

- 元组
- 类型类
- 数据结构：Map、Array

## 4.1 附注：元组

在我们深入了解类型类之前，让我们先介绍一下 Haskell 中最后一个内置数据类型：元组。 *元组*或*对*（或三元组、四元组等）是将几个不同类型的值捆绑在一起的一种方式。你可以将元组视为固定长度的列表（就像 Python 的元组一样）。与列表不同，元组中的每个元素可以有不同的类型。元素的类型反映在元组的类型中。以下是元组类型和值的一些示例：

| 类型 | 示例值 |
|:------------------|:---------------------|
| `(String,String)` | `("Hello","World!")` |
| `(Int,Bool)` | `(1,True)` |
| `(Int,Int,Int)` | `(4,0,3)` |

要从元组中获取值，你可以使用函数 `fst` 和 `snd`：

``` haskell
fst :: (a, b) -> a
snd :: (a, b) -> b
```

你还可以对元组进行模式匹配。这通常是最方便的方法，并且也适用于较大尺寸的元组。`fst` 和 `snd` 功能仅对成对起作用。

元组与列表结合起来非常有用。以下是使用 `Data.List` 模块中的 `zip`、`unzip` 和 `partition` 函数的一些示例。

``` haskell
zip :: [a] -> [b] -> [(a, b)]    -- two lists to list of pairs
unzip :: [(a, b)] -> ([a], [b])  -- list of pairs to pair of lists
partition :: (a -> Bool) -> [a] -> ([a], [a])    -- elements that satisfy and don't satisfy a predicate
```

``` haskell
zip [1,2,3] [True,False,True]
  ==> [(1,True),(2,False),(3,True)]
unzip [("Fred",1), ("Jack",10), ("Helen",13)]
  ==> (["Fred","Jack","Helen"],[1,10,13])
partition (>0) [-1,1,-4,3,2,0]
  ==> ([1,3,2],[-1,-4,0])
```

这是元组模式匹配的示例：

``` haskell
swap :: (a,b) -> (b,a)
swap (x,y) = (y,x)
```

这是同时对元组和列表进行模式匹配的示例：

``` haskell
-- sum all numbers that are paired with True
sumIf :: [(Bool,Int)] -> Int
sumIf [] = 0
sumIf ((True,x):xs) = x + sumIf xs
sumIf ((False,_):xs) = sumIf xs
```

``` haskell
sumIf [(True,1),(False,10),(True,100)]
  ==> 101
```

## 4.2 插曲：折叠

再次考虑函数 `sumNumbers :: [Int] -> Int`、`myMaximum :: [Int] -> Int` 和 `countNothings :: [Maybe a] -> Int`。

``` haskell
sumNumbers :: [Int] -> Int
sumNumbers [] = 0
sumNumbers (x:xs) = x + sumNumbers xs

myMaximum :: [Int] -> Int
myMaximum [] = 0
myMaximum (x:xs) = go x xs
  where go biggest [] = biggest
        go biggest (x:xs) = go (max biggest x) xs

countNothings :: [Maybe a] -> Int
countNothings [] = 0
countNothings (Nothing : xs) = 1 + countNothings xs
countNothings (Just _  : xs) = countNothings xs
```

它们有一个共同点：接收一个列表，并生成一个依赖于列表元素的值。也就是说，它们把包含许多值的列表“压缩”或“折叠”成一个值。

Prelude 有一个名为 `foldr` 的函数，它会对 `Foldable` 数据类型执行“右结合折叠”。我们很快就会了解更多关于 `Foldable` 的内容。现在只需要把它想成列表上的函数即可，所以我们先看这个定义：

``` haskell
foldr :: (a -> b -> b) -> b -> [a] -> b
foldr f y []     = y
foldr f y (x:xs) = f x (foldr f y xs)
```

这个定义表示：对于空列表 `[] :: [a]`，`foldr` 返回默认值 `y :: b`。对于任何非空列表 `x : xs`，`foldr` 会把 `f` 应用到 `x` 和 `foldr f y xs` 的结果上，也就是折叠列表其余部分后的结果。这是一个简单的递归定义。

换句话说，`foldr` 会反复调用它的参数函数 `f`，每次传入两个参数：

- 第一个参数是列表中的当前元素。
- 第二个参数是 `f` 为列表的其余部分返回的内容。

考虑列表 `[1,2,3]`：

![](img/list123.svg)

表达式 `foldr (+) 0 [1,2,3]` 的计算结果如下：

``` haskell
foldr (+) 0 [1,2,3] ==> foldr (+) 0 (1:2:3:[])
                    ==> 1 + (foldr (+) 0 (2:3:[]))
                    ==> 1 + (2 + (foldr (+) 0 (3:[])))
                    ==> 1 + (2 + (3 + (foldr (+) 0 [])))
                    ==> 1 + (2 + (3 + 0))
```

结果可以被认为是一棵树：

![](img/sum123.svg)

理解 `foldr f y xs` 的一种方法是：它把 `(:)` 操作替换为 `f`，把 `[]` 替换为 `y`。在这个例子里，`f` 是 `(+)`，`y` 是 `0`。如果你写出 `sumNumbers [1,2,3]` 的求值过程，会发现它和 `foldr (+) 0 [1,2,3]` 执行的是同样的计算。更一般地说：

``` haskell
sumNumbers xs == foldr (+) 0 xs
```

那些更有数学经验的人可能会注意到，我们可以通过*归纳*来证明这个说法：首先， `sumNumbers [] ==> 0` 和 `foldr (+) 0 [] ==> 0`，所以在基本情况下 `sumNumbers [] == foldr (+) 0 []`。接下来，我们可以假设 `sumNumbers xs == foldr (+) 0 xs` 对于任何列表 `xs` 作为我们的归纳假设。然后，对于列表 `x:xs`，我们有 `sumNumbers (x:xs) ==> x + sumNumbers xs`。因此，通过归纳假设，得到 `foldr (+) 0 (x:xs) ==> x + foldr (+) 0 xs ==> x + sumNumbers xs`。因此，通过归纳法，方程成立。

在本课程中，你不需要阅读、编写或理解归纳证明，但知道 Haskell 中函数的属性和等式可以（原则上）进行数学分析也许会令人放心，因为 Haskell 是一门很好的语言。 （任何编程语言都可以分析等式和属性，但对于 Haskell 来说，这种分析特别方便，因为 Haskell 是纯的。）

另一个折叠示例是 `map` 函数：

``` haskell
map g xs = foldr helper [] xs
  where helper y ys = g y : ys
```

要了解其原理，请考虑 `foldr helper [] [x1,x2,..,xn]` 的作用：

![](img/foldr1.svg)

现在，由于 `helper x xs ==> g x : xs` 对于每个 `x` 和 `xs`，我们得到：

![](img/foldr2.svg)

结果列表 `[ g x1, g x2, g x3, ..., g xn ]` 正是我们使用 `map g xs` 得到的列表。 （这也可以通过归纳来证明，就像我们对 `sumNumbers` 所做的那样。）要吸取的教训是，折叠是一种特殊但相当普遍的方法，可以将某些变换递归地应用到某些结构（例如列表）中。

## 4.3 类型类

Haskell 的 `+` 如何在 `Int` 和 `Double` 上工作？为什么我可以将各种东西与 `==` 进行比较？我们之前简要提到过约束类型。让我们看看它们的真正含义是什么。我们来看看 `==` 和 `+` 的类型。

``` haskell
(==) :: (Eq a) => a -> a -> Bool
```

类型 `(Eq a) => a -> a -> Bool` 的含义是：*对于所有属于 `Eq` 类的类型 `a`，这是一个 `a -> a -> Bool` 类型的函数*。也就是说，如果类型 `a` 是 `Eq` 类的成员，就可以把两个 `a` 类型的值传给 `==`，并得到一个 `Bool` 结果。

``` haskell
(+) :: (Num a) => a -> a -> a
```

类似地，类型 `(Num a) => a -> a -> a` 意味着： *对于属于类 `Num` 的所有类型 `a`，这是类型 `a -> a -> a`*的函数。也就是说，只要 `a` 是 `Num` 的成员，你就可以将两个相同类型 `a` 的值赋予 `+` 并得到第三个类型 `a` 的值。

`Num` 和 `Eq` 是类型类。 *类型类*是一种将支持类似操作的类型分组在一起的方法。

**注意！** 类型类是类型的集合。它与面向对象编程的类没有太大关系！在某些情况下，类型类可以像面向对象编程中的“接口”一样。不幸的是，类型类中的函数通常被称为“方法”，这增加了混乱。

附言。还记得使用类型变量实现多态性如何称为“参数多态性”吗？描述类型类实现的功能的花哨词是“特设多态”。不同之处在于，对于参数多态性，函数（例如 `head`）对所有类型都有相同的实现，而对于特设多态，则有多种实现（考虑数字和字符串上的 `==`）。

## 4.4 类型约束

当你使用具体类型（不是类型变量）时，你可以只使用类型类函数（在本例中为 `(==)`）：

``` haskell
f :: (Int -> Int) -> Int -> Bool
f g x = x == g x
```

当然，如果相关类型不是正确类的成员，则会出现错误。例如：

``` haskell
addTrue :: Bool -> Bool
addTrue b = b + True
```

    error:
        • No instance for (Num Bool) arising from a use of ‘+’
        • In the expression: b + True
          In an equation for ‘addTrue’: addTrue b = b + True

然而，在*多态*函数中，你需要添加*类型约束*。这不起作用：

``` haskell
f :: (a -> a) -> a -> Bool
f g x = x == g x
```

幸运的是，这个错误很好：

    error:
        • No instance for (Eq a) arising from a use of ‘==’
          Possible fix:
            add (Eq a) to the context of
              the type signature for:
                f :: (a -> a) -> a -> Bool
        • In the expression: x == g x
          In an equation for ‘f’: f g x = x == g x

为了表明 `f` 仅适用于 `Eq` 类成员的类型，我们向类型标注添加类型约束 `(Eq a) =>`。

``` haskell
f :: (Eq a) => (a -> a) -> a -> Bool
f g x = x == g x
```

如果你没有类型标注，*类型推断*可以提供约束！

``` haskell
Prelude> f g x = x == g x
Prelude> :type f
f :: (Eq a) => (a -> a) -> a -> Bool
```

你还可以有多个约束：

``` haskell
bothPairsEqual :: (Eq a, Eq b) => a -> a -> b -> b -> Bool
bothPairsEqual left1 left2 right1 right2 = left1 == left2 && right1 == right2
```

## 4.5 标准类型类

以下是你应该了解的一些标准 Haskell 类型类。

### 4.5.1 `Eq`

我们已经看到了用于相等比较的 `Eq` 类。以下是 `Eq` 类的基本操作及其使用示例。正如你所看到的，到目前为止我们见过的几乎所有类型（除了函数）都是 `Eq` 的成员。

``` haskell
(==) :: Eq a => a -> a -> Bool
(/=) :: Eq a => a -> a -> Bool
```

``` haskell
Prelude> 1 == 2
False
Prelude> 1 /= 2
True
Prelude> "Foo" == "Bar"
False
Prelude> [[1,2],[3,4]] == [[1,2],[3,4]]
True
Prelude> (\x -> x+1) == (\x -> x+2)

<interactive>:5:1: error:
    • No instance for (Eq (Integer -> Integer))
        arising from a use of ‘==’
        (maybe you haven't applied a function to enough arguments?)
    • In the expression: (\ x -> x + 1) == (\ x -> x + 2)
      In an equation for ‘it’: it = (\ x -> x + 1) == (\ x -> x + 2)
```

还有一些其他有用的函数使用 `Eq` 类，例如模块 `Data.List` 中的 `nub`。

``` haskell
Prelude> import Data.List
Prelude Data.List> :t nub
nub :: Eq a => [a] -> [a]
Prelude Data.List> nub [3,5,3,1,1]      -- eliminates duplicates
[3,5,1]
```

### 4.5.2 `Ord`

`Ord` 类用于排序（小于、大于）。同样，这里是基本操作及其使用的一些示例。请注意新的 `Ordering` 类型。它的值 `LT` 表示“小于”， `EQ` 表示“等于”， `GT` 表示“大于”。

``` haskell
compare :: Ord a => a -> a -> Ordering
(<) :: Ord a => a -> a -> Bool
(>) :: Ord a => a -> a -> Bool
(>=) :: Ord a => a -> a -> Bool
(<=) :: Ord a => a -> a -> Bool
max :: Ord a => a -> a -> a
min :: Ord a => a -> a -> a
```

``` haskell
Prelude> compare 1 1                -- 1 is EQual to 1
EQ
Prelude> compare 1 3                -- 1 is Less Than 3
LT
Prelude> compare 1 0                -- 1 is Greater Than 0
GT
Prelude> min 5 3
3
Prelude> max 5 3
5
Prelude> "aardvark" < "banana"      -- strings are compared alphabetically
True
Prelude> [1,2,3] > [2,5]            -- lists are compared like strings
False
Prelude> [1,2,3] > [1,1]
True
```

当我们可以比较值时，我们还可以对它们的列表进行排序。`Data.List` 中的函数 `sort` 适用于属于 `Ord` 类的所有类型。

``` haskell
Prelude> import Data.List
Prelude Data.List> :t sort
sort :: Ord a => [a] -> [a]
Prelude Data.List> sort [6,1,4,8,2]
[1,2,4,6,8]
Prelude Data.List> sort "black sphinx of quartz, judge my vow!"     -- remember, strings are lists!
"      !,aabcdefghijklmnoopqrstuuvwxyz"
```

作为最后一个例子，让我们根据长度对列表列表进行排序。我们需要两个辅助函数：

``` haskell
-- from the module Data.Ord
-- compares two values "through" the function f
comparing :: (Ord a) => (b -> a) -> b -> b -> Ordering
comparing f x y = compare (f x) (f y)

-- from the module Data.List
-- sorts a list using the given comparison function
sortBy :: (a -> a -> Ordering) -> [a] -> [a]
```

现在 `sortByLength` 的实现很简单：

``` haskell
-- sorts lists by their length
sortByLength :: [[a]] -> [[a]]
sortByLength = sortBy (comparing length)
```

``` haskell
sortByLength [[1,2,3],[4,5],[4,5,6,7]]   ==>  [[4,5],[1,2,3],[4,5,6,7]]
```

### 4.5.3`Num`, `Integral`, `Fractional`, `Floating`

`Num` 类包含整数算术：

``` haskell
(+) :: Num a => a -> a -> a
(-) :: Num a => a -> a -> a
(*) :: Num a => a -> a -> a
negate :: Num a => a -> a    -- 0-x
abs :: Num a => a -> a       -- absolute value
signum :: Num a => a -> a    -- -1 for negative values, 0 for 0, +1 for positive values
fromInteger :: Num a => Integer -> a
```

`Num` 也出现在整数文字类型中：

``` haskell
Prelude> :t 12
12 :: Num p => p
```

这表示像 `12` 这样的文字可以解释为实现 `Num` 的任何类型的成员。当 GHC 读取 `12` 之类的数字文字时，它会生成对应于 `fromIntegral 12` 的代码。

``` haskell
Prelude> 1 :: Int
1
Prelude> 1 :: Double
1.0
Prelude> fromIntegral 1 :: Double
1.0
```

`Integral` 是表示整数的类型类，例如 `Int` 和 `Integer`。最有趣的函数是用于整数除法和求余的 `div` 和 `mod`。属于 `Integral` 的所有类型也属于 `Num`。

``` haskell
div :: Integral a => a -> a -> a
mod :: Integral a => a -> a -> a
```

`Fractional` 是具有除法类型的类。属于 `Fractional` 的所有类型也属于 `Num`。

``` haskell
(/) :: Fractional a => a -> a -> a
```

`Floating` 包含一些仅对浮点数有意义的附加操作。属于 `Floating` 的所有类型也属于 `Fractional`（以及 `Num`）。

``` haskell
sqrt :: Floating a => a -> a
sin :: Floating a => a -> a
```

### 4.5.4`Read` 和 `Show`

`Show` 和 `Read` 类用于函数 `show` 和 `read`，它们将值与字符串相互转换。

``` haskell
show :: Show a => a -> String
read :: Read a => String -> a
```

``` haskell
Prelude> show 3
"3"
Prelude> read "3" :: Int
3
Prelude> read "3" :: Double
3.0
```

正如你在上面所看到的，你经常需要将类型标注与 `read` 一起使用，以便编译器可以选择正确的实现。

### 4.5.5 附注：`Foldable`

还有一件事！你可能还记得前面提到过 `length` 的类型不是 `[a] -> Int` 而是更通用的类型。我们来看看：

``` haskell
Prelude> :t length
length :: Foldable t => t a -> Int
```

这种类型看起来与我们之前见过的有点不同。类型变量 `t` 有一个参数 `a`。我们将在第 2 部分中更详细地讨论这样的类型类，但这里有一个速成课程。

`Foldable` 代表的是可以折叠的类型。`foldr` 的真实类型是：

``` haskell
foldr :: Foldable t => (a -> b -> b) -> b -> t a -> b
```

我们已经成功地利用了列表是 `Foldable` 的事实，因为我们已经成功地在列表上使用了 `length` 和 `foldr`。然而， `Maybe` 也是 `Foldable`！`Maybe` 的 `Foldable` 实例只是假装 `Maybe a` 的值类似于长度为 0 或 1 的列表：

``` haskell
foldr (+) 1 Nothing   ==> 1
foldr (+) 1 (Just 3)  ==> 4
length Nothing        ==> 0
length (Just 'a')     ==> 1
```

接下来我们将遇到更多可折叠类型。

## 4.6 更多数据结构

现在我们已经熟悉了标准类型类，我们可以看看它们的应用之一：`Map` 和 `Array` 数据结构。

### 4.6.1 `Data.Map`

`Data.Map` 模块定义了 `Map` 类型。映射是键值对的搜索树。看待这个问题的一种方法是， `Map k v` 类型的值与 `[(k,v)]` 类型的值（一个对的列表）大致相同。然而，映射上的操作比列表上的操作更有效。

由于 `Data.Map` 包含一些与 `Prelude` 函数同名的函数，因此需要导入命名空间*限定*：

``` haskell
import qualified Data.Map as Map
```

现在我们可以将地图类型称为 `Map.Map`，以及各种地图函数，例如 `Map.insert`。以下是地图最重要的功能：

``` haskell
-- Create a Map from a list of key-value pairs
Map.fromList :: Ord k => [(k, a)] -> Map.Map k a

-- Insert a value into a map. Overrides any previous value with the same key.
-- Returns a new map. Does not mutate the given map.
Map.insert :: Ord k => k -> a -> Map.Map k a -> Map.Map k a

-- Get a value from a map using a key. Returns Nothing if the key was not present in the map.
Map.lookup :: Ord k => k -> Map.Map k a -> Maybe a

-- An empty map
Map.empty :: Map.Map k a
```

需要映射键类型的 `Ord` 约束，因为映射被实现为*有序二叉搜索树*。

请注意，与所有 Haskell 值一样，映射是“不可变的”，这表示一旦定义映射就无法更改它。然而，像 `insert` 这样的映射操作会生成一个“新”映射。要执行多个映射操作，你需要重用返回值。这是在地图上运行的 GHCi 会话。

``` haskell
Prelude> import qualified Data.Map as Map
Prelude Map> values = Map.fromList [("z",3),("w",4)]
Prelude Map> Map.lookup "z" values
Just 3
Prelude Map> Map.lookup "banana" values
Nothing
Prelude Map> Map.insert "x" 7 values
fromList [("w",4),("x",7),("z",3)]
Prelude Map> values                                       -- note immutability!
fromList [("w",4),("z",3)]
Prelude Map> Map.insert "x" 1 (Map.insert "y" 2 values)   -- two insertions
fromList [("w",4),("x",1),("y",2),("z",3)]
Prelude Map>
```

以下是将银行表示为 `Map String Int`（从帐户名称映射到帐户余额）并从帐户中提取一些钱的示例：

``` haskell
withdraw :: String -> Int -> Map.Map String Int -> Map.Map String Int
withdraw account amount bank =
  case Map.lookup account bank of
    Nothing  -> bank                                   -- account not found, no change
    Just sum -> Map.insert account (sum-amount) bank   -- set new balance
```

以下是如何在 `GHCi` 中使用 `withdraw` 函数。请注意地图如何打印为 `fromList` 调用。另请注意，调用 `withdraw ... bank` 如何返回*新*存储体，并且不会更改现有存储体。

``` haskell
GHCi> bank = Map.fromList [("Bob",100),("Mike",50)]
GHCi> withdraw "Bob" 80 bank
fromList [("Bob",20),("Mike",50)]
GHCi> bank                         -- note immutability
fromList [("Bob",100),("Mike",50)]
GHCi> withdraw "Bozo" 1000 bank
fromList [("Bob",100),("Mike",50)]
```

`Data.Map` 定义了各种有用的高阶函数来更新地图。我们可以使用 `Data.Map.adjust` 重写 `withdraw` 函数：

``` haskell
withdraw :: String -> Int -> Map.Map String Int -> Map.Map String Int
withdraw account amount bank = Map.adjust (\x -> x-amount) account bank
```

**注意！** 有单独的 `Data.Map.Strict` 和 `Data.Map.Lazy` 实现。当你导入 `Data.Map` 时，你会得到 `Data.Map.Lazy`。你可以在[the docs for `Data.Map.Lazy`](https://hackage.haskell.org/package/containers-0.6.5.1/docs/Data-Map-Lazy.html)中找到所有 `Data.Map` 函数的文档。我们不会在这里讨论它们的差异，但大多数情况下你应该在实际代码中使用 `Data.Map.Strict`。

### 4.6.2 `Data.Array`

另一种工作方式类似于列表但对于某些操作更有效的类型是数组。数组在许多其他编程语言中都很常见，但 Haskell 数组有点不同。

与 `Data.Map` 模块不同， `Data.Array` 可以正常导入：

``` haskell
import Data.Array
```

现在我们可以看看构造数组的 `array` 函数的类型。

``` haskell
array :: Ix i => (i, i) -> [(i, e)] -> Array i e
```

这里有几件事需要注意。首先， `Array` 类型由“两种”类型参数化：索引类型和元素类型。大多数其他编程语言仅使用元素类型参数化数组，但索引类型始终为 `int`。例如，在 Haskell 中，我们可以有一个 `Array Char Int`：一个由字符索引的数组，或者 `Array Bool String`，一个由布尔索引的数组，甚至 `Array (Int,Int) Int`，一个二维整数数组。

并非所有类型都可以是索引类型。只有类似于整数的类型才适合。这就是 `Ix i` 类约束的原因。`Ix` 类收集所有可用作数组索引的类型。

其次， `array` 函数需要一个额外的 `(i,i)` 参数。这些是数组的最小和最大索引。与其他一些语言不同，数组总是从索引 0 或 1 开始，在 Haskell 中，你可以定义一个从 7 开始到 11 的数组。所以这个数组是这样的：

``` haskell
myArray :: Array Int String
myArray = array (7,11) [(7,"seven"), (8,"eight"), (9,"nine"), (10,"ten"), (11,"ELEVEN")]
```

按顺序列出所有索引和元素可能有点麻烦，因此还有 `listArray` 构造函数，它只按顺序获取元素列表：

``` haskell
listArray :: Ix i => (i, i) -> [e] -> Array i e
```

``` haskell
myArray :: Array Int String
myArray = listArray (7,11) ["seven", "eight", "nine", "ten", "ELEVEN"]
```

数组与两个新运算符一起使用：

``` haskell
-- Array lookup
(!) :: Ix i => Array i e -> i -> e
-- Array update
(//) :: Ix i => Array i e -> [(i, e)] -> Array i e
```

以下是 `GHCi` 会话示例：

``` haskell
Prelude> import Data.Array
Prelude Data.Array> myArray = listArray (7,11) ["seven", "eight", "nine", "ten", "ELEVEN"]
Prelude Data.Array> myArray
array (7,11) [(7,"seven"),(8,"eight"),(9,"nine"),(10,"ten"),(11,"ELEVEN")]
Prelude Data.Array> myArray ! 8
"eight"
Prelude Data.Array> myArray // [(8,"ocho"),(9,"nueve")]
array (7,11) [(7,"seven"),(8,"ocho"),(9,"nueve"),(10,"ten"),(11,"ELEVEN")]
```

你可能想知道为什么 `(//)` 运算符一次执行多个更新。原因是 Haskell 数组的主要弱点：不变性。由于数组无法就地更改，因此 `(//)` 必须复制整个数组。这就是为什么在 Haskell 中通常最好使用列表或映射来存储需要更新的数据。然而，当构造一次然后用于大量查找时，数组可能仍然有用。我们将在下一讲中回顾 Haskell 数据结构的工作原理。

**注意！** 在本课程中，我们将仅使用 `Array`，这是 Haskell 标准中指定的简单数组类型。还有许多其他数组类型，例如可变的 `IOArray` 和有些晦涩的 `DiffArray`。还有用于数组的类型类，如 `IArray` 和 `MArray`。除了数组之外，还有一个[wide family of `Vector` types](https://hackage.haskell.org/package/vector)对于实际程序来说比 `Array` 更实用。

### 4.6.3 附注：折叠映射和数组

`Map` 和 `Array` 类型是 `Foldable` 的实例，就像列表一样！这表示你可以在它们上使用 `length` 和 `foldr` 等函数：

``` haskell
length (array (7,11) [(7,"seven"),(8,"eight"),(9,"nine"),(10,"ten"),(11,"ELEVEN")])
  ==> 5
foldr (+) 0 (Map.fromList [("banana",3),("egg",7)])
  ==> 10
```

## 4.7 阅读文档

Haskell 库往往有非常好的文档。我们之前已经通过 Hackage (<https://hackage.haskell.org>) 链接到了文档，但知道如何自己查找文档也很重要。用于生成 Haskell 文档的工具称为 *Haddock*，因此有时 Haskell 文档被称为 *haddocks*。

Hackage 是 Haskell 包存储库（就像 Python 的[PyPI](https://pypi.org/)、Java 的 Maven Central 或 JavaScript 的[NPM](https://npmjs.com)）。除了实际的包之外，它还托管它们的文档。我们在本课程中使用的大多数模块都位于名为 `base` 的包中。你可以在<https://hackage.haskell.org/package/base-4.16.4.0/>浏览基础包的文档。

当你不太确定要查找的功能在哪里时，Hoogle (<https://hoogle.haskell.org/>) 可以提供帮助。 Hoogle 是 Haskell 文档的搜索引擎。当你需要检查 `foldr` 的类型或哪些包包含名为 `reverse` 的函数时，这是一个很好的资源。

最后，由于本课程使用 `stack` 工具，你还可以使用以下命令浏览已为你安装的库堆栈的文档

    stack haddock --open
    stack haddock --open <package>

这样做的另一个好处是可以获得正确版本的文档。

综上所述，阅读Haskell库文档的主要方式如下：

- 如果你知道包的名称，你可以通过<https://hackage.haskell.org/>浏览文档。
- 如果你知道函数的名称，则可以使用<https://hoogle.haskell.org/>找到它。
- 如果你使用的是 `stack`，则可以使用 `stack haddock --open` 或 `stack haddock --open <package>` 在浏览器中打开文档。

## 4.8 测验

`swap . swap` 是什么类型？

1.  `(a, b) -> (a, b)`
2.  `(a, b) -> (b, a)`
3.  `a -> a`

`\f g x -> (f x, g x)` 是什么类型？

1.  `(a -> b) -> (c -> d) -> (a,c) -> (b, d)`
2.  `(a -> b) -> (a -> c) -> a -> (b, c)`
3.  `(a -> b) -> (b -> a) -> a -> (b, a)`

`\t -> (fst . fst $ t, (snd . fst $ t, snd t))` 是什么类型？

1.  `(a, (b, c)) -> (a, (b, c))`
2.  `(a, (b, c)) -> ((a, b), c)`
3.  `((a, b), c) -> (a, (b, c))`

函数 `foldr (\x xs -> xs ++ [x]) []` 有什么作用？

1.  它根本不改变它的输入列表
2.  它从左到右更改列表的关联性
3.  它反转其输入列表

函数 `foldr (\(x, y) zs -> x : y : zs) []` 有什么作用？

1.  它将一对列表变成一对列表
2.  它将一对列表变成一个对的列表
3.  它将对列表转换为元素列表

`foldr (\n b -> n == 3 && b)` 是什么类型？

1.  `(Foldable t, Eq a, Num a) => Bool -> t a -> Bool`
2.  `(Foldable t, Eq a, Num a, Bool b) => b -> t a -> b`
3.  `(Foldable t, Eq a, Num a) => Bool -> [ a ] -> Bool`

`\x -> case x of (True, "Foo") -> show True ++ "Foo"` 是什么类型？

1.  `Either Bool String -> String`
2.  `(Bool, String) -> String`
3.  `Show a => (Bool, String) -> a`

## 4.9 练习

- [Set4a](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set4a.hs)：类型类
- [Set4b](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set4b.hs): 折叠
