---
title: Markdown 扩展功能
published: 2024-05-01
updated: 2024-11-29
description: '在 Fuwari 中了解更多 Markdown 扩展能力'
image: ''
tags: [示例, Markdown, Fuwari]
category: '示例'
draft: false 
---

## GitHub 仓库卡片
你可以添加链接到 GitHub 仓库的动态卡片。页面加载时会从 GitHub API 拉取仓库信息。

::github{repo="Fabrizz/MMM-OnSpotify"}

使用 `::github{repo="<owner>/<repo>"}` 即可创建 GitHub 仓库卡片。

```markdown
::github{repo="saicaca/fuwari"}
```

## 提示块（Admonitions）

支持以下提示块类型：`note` `tip` `important` `warning` `caution`

:::note
强调即使快速浏览也应注意的信息。
:::

:::tip
帮助用户更顺利完成任务的可选信息。
:::

:::important
用户成功完成任务所必需的关键信息。
:::

:::warning
由于存在潜在风险，需要用户立即关注的重要内容。
:::

:::caution
某个操作可能带来的负面后果。
:::

### 基础语法

```markdown
:::note
Highlights information that users should take into account, even when skimming.
:::

:::tip
Optional information to help a user be more successful.
:::
```

### 自定义标题

提示块标题可以自定义。

:::note[MY CUSTOM TITLE]
这是一个带有自定义标题的提示块。
:::

```markdown
:::note[MY CUSTOM TITLE]
This is a note with a custom title.
:::
```

### GitHub 语法

> [!TIP]
> 同样支持 [GitHub 语法](https://github.com/orgs/community/discussions/16925)。

```
> [!NOTE]
> The GitHub syntax is also supported.

> [!TIP]
> The GitHub syntax is also supported.
```

### 剧透（Spoiler）

你可以在文本中添加剧透内容，剧透内部同样支持 **Markdown** 语法。

这段内容 :spoiler[被隐藏了 **ayyy**]！

```markdown
The content :spoiler[is hidden **ayyy**]!

```