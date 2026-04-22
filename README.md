# Haskell MOOC 中文翻译

<p align="center">
  <img alt="Haskell MOOC course logo" src="img/haskell-mooc-logo.svg" width="360">
</p>

本仓库整理了 University of Helsinki 的 [Haskell MOOC](https://haskell.mooc.fi) 课程材料，并提供中文翻译版本。课程面向想用 Haskell 学习函数式编程的读者，内容从基础语法、递归、高阶函数和类型类开始，逐步进入 IO、Monad、库、测试、并行与并发。

中文译文位于 [`docs/`](./docs/) 目录，可从 [`docs/index.md`](./docs/index.md) 或 [`docs/01-and-so-it-begins.md`](./docs/01-and-so-it-begins.md) 开始阅读。翻译过程、术语选择和一些约定记录在 [`other/translation-guidance.md`](./other/translation-guidance.md)。

## 在线站点

本仓库已配置 MkDocs + Material 主题，可构建为中文课程网站：

```sh
mkdocs serve
```

构建静态站点：

```sh
mkdocs build
```

构建结果会输出到 `site/` 目录。

## 课程结构

- 第 1 部分：Haskell 基础、递归、高阶函数、列表、代数数据类型、类型类和纯函数式编程。
- 第 2 部分：IO、Monad、惰性求值、Functor/Foldable、常用库、测试以及并发相关内容。

建议按章节顺序学习，并在每讲结束后完成对应练习。后续章节会默认你已经熟悉前面的语法和抽象。

## 练习

练习文件在 [`exercises/`](./exercises/) 目录下。首次运行前先安装依赖：

```sh
cd exercises
stack build
```

练习文件通常命名为 `Set1.hs`、`Set2a.hs`、`Set14b.hs` 等。按文件内说明补全代码后，可运行对应测试：

```sh
stack runhaskell SetXTest.hs
```

请把 `SetXTest.hs` 替换成当前练习对应的测试文件名，例如 `Set3aTest.hs`。

## 原始课程

本翻译基于 Joel Kaasinen 和 John Lång 编写的 Haskell MOOC 原始材料整理。官方资源如下：

- [课程网站](https://haskell.mooc.fi)
- [官方 GitHub 仓库](https://github.com/moocfi/haskell-mooc)
- [课程 Telegram 频道](https://t.me/haskell_mooc_fi)

仓库根目录中的 [`part1.html`](./part1.html) 和 [`part2.html`](./part2.html) 是原课程 HTML 材料的离线版本。

## 常见问题

- 如果 `stack build` 在构建 `zlib` 时失败，通常需要安装 zlib 头文件。Ubuntu 可运行 `sudo apt install zlib1g-dev`。
- 如果出现 `Downloading lts-18.18 build plan ... RedownloadInvalidResponse`，通常是 Stack 版本过旧。可运行 `stack upgrade` 后重试。
- 本翻译不是官方中文版。如中文表述与原文含义不一致，请以原课程材料为准。

## 授权

原课程材料与本中文翻译均遵循 [Creative Commons Attribution-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-sa/4.0/)。

[![License: CC BY-SA 4.0](https://i.creativecommons.org/l/by-sa/4.0/88x31.png)](https://creativecommons.org/licenses/by-sa/4.0/)

如果你发现翻译、代码或排版问题，欢迎通过 issue 或 pull request 反馈。贡献前可先阅读 [`CONTRIBUTING.md`](./CONTRIBUTING.md)。
