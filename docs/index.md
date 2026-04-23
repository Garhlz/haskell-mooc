<section class="course-hero" markdown>
# Haskell MOOC 中文版

从表达式、类型和递归开始，用 Haskell 系统学习函数式编程。本站是 Haskell MOOC 课程材料的中文整理版，保留原课程的章节顺序、代码示例和练习入口。

翻译版仓库：[Garhlz/haskell-mooc](https://github.com/Garhlz/haskell-mooc)
</section>

## 从这里开始

如果你是第一次接触 Haskell，建议直接从第 1 讲开始，边读边在 GHCi 中运行示例。每讲结束后完成对应练习，再进入下一讲。

已经有函数式编程经验的读者，也建议快速浏览第 1 部分。课程后半部分会默认你熟悉前面出现的类型、模式匹配、递归写法和类型类。

## 学习路径

=== "第 1 部分"

    先建立 Haskell 的基本读写能力：表达式、类型、列表、递归、高阶函数、代数数据类型和类型类。第 1 部分坚持纯函数式编程，暂不引入 IO。

    - [第 1 讲：故事就这样开始](01-and-so-it-begins.md)
    - [第 2 讲：要么成为英雄，要么……](02-either-you-die-a-hero.md)
    - [第 3 讲：折叠与高阶函数](03-catamorphic.md)
    - [第 4 讲：类型类进阶](04-real-classy.md)
    - [第 5 讲：用类型打结](05-you-need-string-for-a-knot.md)
    - [第 6 讲：类型类英雄](06-working-class-hero.md)
    - [第 7 讲：新的星座](07-new-constellations.md)
    - [第 8 讲：余韵](08-the-aftertaste.md)

=== "第 2 部分"

    在第 1 部分的基础上进入更接近真实程序的主题：惰性求值、IO、Functor、Monad、常用库、测试、并行与并发。

    - [第 9 讲：第 1 部分回顾](09-recap-of-part-1.md)
    - [第 10 讲：还原主义](10-reductionism.md)
    - [第 11 讲：`RealWorld -> (a,RealWorld)`](11-realworld-to-a-realworld.md)
    - [第 12 讲：fmap fmap fmap](12-fmap-fmap-fmap.md)
    - [第 13 讲：问题范畴中的幺半群](13-a-monoid-in-the-category-of-problems.md)
    - [第 14 讲：开始使用库](14-lets-use-some-libraries.md)
    - [第 15 讲：没有 Monad 也能做验证](15-youre-valid-even-without-monads.md)
    - [第 16 讲：零碎内容](16-odds-and-ends.md)

## 你会学到什么

<div class="grid cards" markdown>

- **用类型思考**

    通过类型签名理解函数行为，让编译器在早期暴露错误。

- **用递归和组合写程序**

    从列表处理开始，逐步掌握高阶函数、模式匹配和函数组合。

- **理解纯函数式编程**

    学习如何把副作用隔离出来，并利用纯函数提升可推理性。

- **完成真实练习**

    每讲都配有 Haskell 源码练习，通过测试反馈巩固材料。

</div>

## 学习建议

1. 先进入 `exercises/` 目录运行 `stack build`，确认练习环境可用。
2. 不要只阅读代码块；把示例复制到 GHCi，观察类型和求值结果。
3. 遇到类型错误时，先读函数类型，再读错误信息中提到的实际类型与期望类型。
4. 每讲的练习比正文更重要。卡住时可以回到相关小节重新运行示例。

!!! note "关于这个翻译"
    本站不是官方中文版。译文尽量保留原课程结构和代码，只针对中文表达、术语一致性与网站阅读体验做整理。若中英文含义存在差异，请以原课程为准。

## 原始资源

- [官方课程网站](https://haskell.mooc.fi)
- [官方 GitHub 仓库](https://github.com/moocfi/haskell-mooc)
- [课程 Telegram 频道](https://t.me/haskell_mooc_fi)

## 延伸阅读

- [A Gentle Introduction to Haskell](https://www.haskell.org/tutorial/)
- [Learn You a Haskell for Great Good!](http://learnyouahaskell.com/chapters)
- [The Haskell School of Expression](https://www.cs.yale.edu/homes/hudak/SOE/index.htm)
- [Haskell Programming from First Principles](https://haskellbook.com/)
- [`#haskell` on libera.chat](https://libera.chat/)
