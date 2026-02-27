---
title: Markdown 示例
published: 2023-10-01
description: 一个简单的 Markdown 博客文章示例。
tags: [Markdown, 博客, 示例]
category: 示例
draft: false
---

# 一级标题示例

段落之间使用空行分隔。

第二段。_斜体_、**加粗** 和 `等宽字体`。无序列表如下：

- 第一项
- 第二项
- 第三项

注意：如果不算列表符号，正文内容从左侧缩进 4 个空格开始。

> 引用块
> 可以这样写。
>
> 也可以跨越多个段落，
> 按需使用即可。

三个短横线可表示长破折号。两个短横线可表示区间（例如 12--14 章）。
三个点 ... 会被转换为省略号。也支持 Unicode。☺

## 二级标题示例

下面是有序列表：

1. 第一项
2. 第二项
3. 第三项

再次注意正文会从左侧缩进 4 个字符开始。下面是代码示例：

    # 再重复一遍...
    for i in 1 .. 10 { do-something(i) }

你应该已经发现了：这里是 4 个空格缩进。除此之外，也可以用围栏代码块：

```
define foobar() {
    print "Welcome to flavor country!";
}
```

（这样复制粘贴更方便）。你还可以给围栏代码块指定语言，让 Pandoc 做语法高亮：

```python
import time
# 快，数到十！
for i in range(10):
    # (but not *too* quick)
    time.sleep(0.5)
    print i
```

### 三级标题示例

下面是嵌套列表示例：

1. 先准备这些食材：

    - 胡萝卜
    - 芹菜
    - 扁豆

2. 烧一锅水。

3. 把所有食材放进锅里，并按下面步骤操作：

        找到木勺
        打开锅盖
        搅拌
        盖上锅盖
        把木勺勉强架在锅柄上
        等待 10 分钟
        回到第一步（或者煮好后关火）

    不要碰到木勺，不然会掉下来。

再注意一次：文本始终按 4 空格缩进对齐（包括上面第 3 项的续行）。

这里有一个 [网站链接](http://foo.bar)，一个 [本地文档链接](local-doc.html)，以及一个 [当前文档内标题链接](#an-h2-header)。另外还有脚注 [^1]。

[^1]: 脚注内容写在这里。

表格可以这样写：

size material color

---

9 leather brown
10 hemp canvas natural
11 glass transparent

表：鞋子、尺码与材质

（上面这一行是表格标题。）Pandoc 也支持多行表格：

---

keyword text

---

red Sunsets, apples, and
other red or reddish
things.

green Leaves, grass, frogs
and other things it's
not easy being.

---

下面是一条分割线。

---

定义列表可以这样写：

apples
: 适合做苹果酱。
oranges
: 柑橘类水果！
tomatoes
: tomato 拼写里没有多余的 "e"。

同样，文本依然是 4 空格缩进。（如果想更清晰，每组术语/定义之间可加空行。）

这是一个“行块（line block）”：

| 第一行
| 第二行
| 第三行

图片也可以这样写：

[//]: # (![example image]&#40;./demo-banner.png "An exemplary image"&#41;)

行内公式可以这样写：$\omega = d\phi / dt$。行间公式建议独占一行，并使用双美元符：

$$I = \int \rho R^{2} dV$$

$$
\begin{equation*}
\pi
=3.1415926535
 \;8979323846\;2643383279\;5028841971\;6939937510\;5820974944
 \;5923078164\;0628620899\;8628034825\;3421170679\;\ldots
\end{equation*}
$$

最后，任何你希望按字面显示的标点都可以用反斜杠转义，例如：\`foo\`、\*bar\* 等。
