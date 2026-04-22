# 翻译工作指南

这份文档记录了把 Haskell MOOC 的 Markdown 文件翻译成中文时使用的工作流。目标是在翻译正文的同时，保留代码块、链接、行内代码、标题以及按章节拆分的结构。

## 整体流程

1. 将原始 HTML 转换为干净的 Markdown。
   - 输入示例：`part1.html`、`part2.html`
   - 输出示例：`part1.clean.md`、`part2.clean.md`
   - 保留代码围栏、标题、链接、列表、引用块和图片等结构。

2. 选择需要翻译的章节范围。
   - 第 1-4 章输出到 `part1-translate.md`。
   - 第 5-8 章输出到 `part1-translate-5-8.md`。

3. 只对正文做机器翻译。
   - 代码块必须原样保留。
   - 像 `` `map` `` 这样的行内代码需要在翻译前保护起来。
   - Markdown 链接中的 URL 应保持不变。
   - 即使标题和列表项的可见文本被翻译，Markdown 结构也要保留。

4. 手动润色中文。
   - 修正 Haskell 专有术语。
   - 修正机器翻译中关于 `class`、`instance`、`Monad`、`Monoid`、`IO`、`pure`、`case` 和 `constructor` 的误译。
   - 将过于正式的 `您` 改为 `你`。
   - 让章节标题和目录中的译名保持一致。

5. 将翻译后的章节拆分到 `translate-cn/`。
   - 每个文件都保留：
     - `# Haskell 慕课，第 1 部分`
     - 当前章节自己的局部目录
     - 当前章节的译文正文

6. 运行结构检查。
   - 代码围栏数量应与源码一致。
   - 不应残留占位符。
   - 不应残留明显的 HTML 片段。
   - 代码块外的行内反引号应成对出现。

## 机器翻译脚本模式

脚本使用公开的 Google Translate endpoint 生成第一版译文。它会先用占位符保护对 Markdown 或代码敏感的片段，翻译普通正文，然后再恢复这些占位符。

注意：这个脚本只用于生成草稿。输出内容仍然需要人工 review。

```python
from pathlib import Path
import html
import json
import re
import urllib.parse
import urllib.request

SOURCE = Path("part1.clean.md")
OUT = Path("part1-translate-draft.md")


def google_translate(text: str) -> str:
    query = urllib.parse.urlencode({
        "client": "gtx",
        "sl": "en",
        "tl": "zh-CN",
        "dt": "t",
        "q": text,
    })
    url = "https://translate.googleapis.com/translate_a/single?" + query
    with urllib.request.urlopen(url, timeout=30) as response:
        data = json.loads(response.read().decode("utf-8"))
    return "".join(part[0] for part in data[0])


def protect_inline_markdown(text: str):
    placeholders = {}

    def put(value: str) -> str:
        key = f"ZXQPH{len(placeholders)}ZXQ"
        placeholders[key] = value
        return key

    # 先保护行内代码。
    text = re.sub(r"`[^`]*`", lambda m: put(m.group(0)), text)

    # 保护 Markdown 链接中的 URL，但允许链接文本被翻译。
    def protect_link(m):
        label, url = m.group(1), m.group(2)
        url_key = put(url)
        return f"[{label}]({url_key})"

    text = re.sub(r"\[([^\]]+)\]\(([^)]+)\)", protect_link, text)
    return text, placeholders


def restore_placeholders(text: str, placeholders: dict[str, str]) -> str:
    for key, value in placeholders.items():
        text = text.replace(key, value)
    return text


def translate_markdown(md: str) -> str:
    result = []
    in_code = False
    paragraph = []

    def flush_paragraph():
        if not paragraph:
            return
        block = "\n".join(paragraph)
        protected, placeholders = protect_inline_markdown(block)
        translated = google_translate(protected)
        translated = restore_placeholders(translated, placeholders)
        result.append(html.unescape(translated))
        paragraph.clear()

    for line in md.splitlines():
        if line.startswith("```"):
            flush_paragraph()
            in_code = not in_code
            result.append(line)
            continue

        if in_code:
            result.append(line)
            continue

        if not line.strip():
            flush_paragraph()
            result.append("")
            continue

        # 保持 Markdown 块结构，只翻译可见正文。
        paragraph.append(line)

    flush_paragraph()
    return "\n".join(result).rstrip() + "\n"


text = SOURCE.read_text(encoding="utf-8")

# 示例：从第 5 章开始翻译。
match = re.search(r"^# 5 ", text, flags=re.MULTILINE)
if not match:
    raise SystemExit("chapter start not found")

draft = translate_markdown(text[match.start():])
OUT.write_text(draft, encoding="utf-8")
```

## 手动润色规则

下面这些替换和检查只能作为起点，不能盲目当作最终结果。有些词需要结合上下文判断。

常见术语：

- `type class`：类型类
- `class`：类。指 Haskell class 时不要译成“课程”
- `instance`：实例
- `constructor`：构造函数
- `field`：字段
- `record syntax`：记录语法
- `algebraic datatype`：代数数据类型
- `type parameter`：类型参数
- `recursive type`：递归类型
- `deriving`：推导
- `default implementation`：默认实现
- `minimal complete definition`：最小完整定义
- `subclass`：子类
- `Monoid`：幺半群，首次出现时也可写作 `Monoid（幺半群）`
- `Semigroup`：半群
- `Monad`：通常保留为 `Monad`，首次出现时也可写作 `Monad（单子）`
- `IO action`：IO 操作
- `side effect`：副作用
- `pure`：纯的
- `purity`：纯性
- `case`：情况。在代数数据类型建模的上下文里，不要译成“案例”

风格偏好：

- 使用 `你`，不要使用 `您`。
- Haskell 标识符、类型名、构造函数、运算符和 GHCi 命令都保留代码格式。
- URL 保持不变。
- 代码块里的注释默认保持不变，除非有明确理由需要翻译。
- 标题和目录里的译名要保持一致。
- 优先使用自然可读的中文，不要机械保留英文语序。

典型机器翻译修正：

```python
from pathlib import Path

path = Path("part1-translate-draft.md")
text = path.read_text(encoding="utf-8")

replacements = {
    "您": "你",
    "旁注": "附注",
    "课程的信息": "类的信息",
    "课程文档": "类文档",
    "自动使你的类成为某些基本类型类的成员": "自动让你的类型成为某些基本类型类的实例",
    "访问器功能": "访问器函数",
    "最小的完整定义": "最小完整定义",
    "通过网络交谈": "通过网络通信",
    "杂质": "非纯性",
    "纯度怎么样？": "纯性怎么办？",
    "数学规划难题": "数学编程谜题",
}

for old, new in replacements.items():
    text = text.replace(old, new)

path.write_text(text, encoding="utf-8")
```

## 拆分章节

这个脚本会把合并后的译文拆分成每章一个文件，并写入 `translate-cn/`。它假设合并文件在 `# Haskell 慕课，第 1 部分` 之前有一份目录。

```python
from pathlib import Path
import re

src = Path("part1-translate-5-8.md")
outdir = Path("translate-cn")
outdir.mkdir(exist_ok=True)

lines = src.read_text(encoding="utf-8").splitlines()
doc_title_idx = next(i for i, line in enumerate(lines) if line.startswith("# Haskell"))
toc_lines = lines[:doc_title_idx]
body_lines = lines[doc_title_idx:]

chapter_starts = []
for i, line in enumerate(body_lines):
    match = re.match(r"^# ([5-8]) ", line)
    if match:
        chapter_starts.append((int(match.group(1)), i))
chapter_starts.append((9, len(body_lines)))

names = {
    5: "05-you-need-string-for-a-knot.md",
    6: "06-working-class-hero.md",
    7: "07-new-constellations.md",
    8: "08-the-aftertaste.md",
}

for idx in range(4):
    chapter, start = chapter_starts[idx]
    _, end = chapter_starts[idx + 1]
    chapter_body = body_lines[start:end]

    chapter_toc = []
    in_chapter = False
    for line in toc_lines:
        if re.match(rf"- \[{chapter} ", line):
            in_chapter = True
            chapter_toc.append(line)
            continue
        if re.match(r"- \[[5-8] ", line):
            in_chapter = False
        if in_chapter and re.match(rf"  - \[{chapter}\.\d+ ", line):
            chapter_toc.append(line)

    content = [
        "# Haskell 慕课，第 1 部分",
        "",
        *chapter_toc,
        "",
        *chapter_body,
    ]
    (outdir / names[chapter]).write_text(
        "\n".join(content).rstrip() + "\n",
        encoding="utf-8",
    )
```

## 结构检查

比较源码和译文的代码围栏数量：

```sh
rg -c '^```' /tmp/part1-source-5-8.md
rg -c '^```' part1-translate-5-8.md
```

检查拆分后的文件：

```sh
python3 - <<'PY'
from pathlib import Path

files = sorted(Path("translate-cn").glob("0[5-8]-*.md"))
for path in files:
    text = path.read_text(encoding="utf-8")
    fences = text.count("\n```")
    odd_inline = []
    in_code = False
    for line_no, line in enumerate(text.splitlines(), 1):
        if line.startswith("```"):
            in_code = not in_code
            continue
        if not in_code and line.count("`") % 2:
            odd_inline.append(line_no)
    print(path, "fences=", fences, "even=", fences % 2 == 0,
          "odd inline backticks=", odd_inline[:10])
PY
```

搜索常见残留问题：

```sh
rg -n 'ZXQPH|<span|<div|\u00a0|您|旁注|工人阶级|杂质|纯度|通过网络交谈|数学规划|精美的变化|寻求有关材料|Part 2 of the course' \
  part1-translate-5-8.md translate-cn/
```

如果出现不间断空格，统一替换成普通空格：

```python
from pathlib import Path

files = [Path("part1-translate-5-8.md"), *Path("translate-cn").glob("0[5-8]-*.md")]
for path in files:
    text = path.read_text(encoding="utf-8")
    path.write_text(text.replace("\u00a0", " "), encoding="utf-8")
```

## 备注

翻译流程有意分成两个阶段：

1. 先用机器翻译提高速度和覆盖范围。
2. 再人工做技术润色，保证正确性和可读性。

不要信任机器翻译对编程术语的处理。只要某个技术段落听起来不自然，就应该对照源码重新检查。
