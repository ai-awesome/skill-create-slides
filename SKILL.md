---
name: create-slides
description: >
  Create presentations using the slides.johnsonlee.io framework (Reveal.js + Markdown).
  Trigger when user mentions "create slides", "presentation", "make a deck", "slide deck",
  "做个 slides", "做个演讲", "写个 PPT", "做个分享".
  Also applies when the user provides a topic or outline and wants a complete slide deck.
---

# Slides Creator

基于 [slides.johnsonlee.io](https://slides.johnsonlee.io) 框架创建演示文稿。该框架是一个轻量级的 Reveal.js 封装，用 Markdown 编写幻灯片内容。

## 工作流程

1. **明确主题和大纲** — 跟用户对齐演讲主题、目标受众、语言（中/英/双语）、章节结构
2. **创建项目** — 初始化 GitHub repo，生成 `index.html` 和目录结构
3. **编写内容** — 按章节生成 Markdown 幻灯片
4. **用户审阅** — 让用户审阅，根据反馈修改
5. **部署** — 推送到 GitHub，通过 GitHub Pages 发布

## 框架概述

### 引用方式

演示文稿是一个静态 HTML 站点，通过 CDN 引用框架：

```html
<head>
  <link rel="stylesheet" href="https://slides.johnsonlee.io/slides.css">
</head>
<body>
  <script src="https://slides.johnsonlee.io/slides.js"></script>
  <script>
    Slides.init({
      title: 'Presentation Title',
      chapters: ['cover', 'chapter-1', 'chapter-2'],
      langs: { en: 'EN', zh: '中文' }
    });
  </script>
</body>
```

### 配置参数

| 属性 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `title` | `string` | 是 | 页面标题，用于 `<title>` 标签 |
| `chapters` | `string[]` | 是 | Markdown 文件名（不含 `.md` 后缀） |
| `langs` | `{ code: label }` | 否 | 语言映射，如 `{ en: 'EN', zh: '中文' }` |
| `reveal` | `object` | 否 | 覆盖 Reveal.js 初始化选项 |

### 目录结构

```
project-root/
  index.html              # 主页面，包含 Slides.init() 配置
  src/
    en/                    # 英文内容
      cover.md
      chapter-1.md
      chapter-2.md
    zh/                    # 中文内容（如果双语）
      cover.md
      chapter-1.md
      chapter-2.md
  assets/                  # 语言无关的图片资源
    diagram.svg
```

## Markdown 编写规范

### 幻灯片分隔符

- `---`（前后各一个空行）：章节内的水平分隔，创建新的幻灯片
- `----`（前后各一个空行）：垂直分隔，创建子幻灯片

```markdown
## Slide 1 Title

Content here

----

## Slide 1.1 (vertical sub-slide)

More content

----

## Slide 1.2

Even more content
```

### 幻灯片指令

在幻灯片开头使用 HTML 注释控制布局：

```markdown
<!-- .slide: class="center" -->

## Centered Title

*Subtitle text*
```

### 自动布局规则

框架会自动检测幻灯片内容并应用最佳布局，**不需要手动配置**：

1. **居中布局** — 标题 + 最多一行文字（无列表/表格/图片）→ 自动居中。也可通过 `<!-- .slide: class="center" -->` 手动触发
2. **拉伸列表** — 标题 + 单个列表（≤ 8 项）→ 列表拉伸填满空间，各项均匀分布
3. **自动分列** — 标题 + 单个列表（> 8 项）→ 自动分成 `ceil(N/8)` 列
4. **表格** — 标题 + 表格 → 表格填满标题下方可用宽度
5. **图片** — 标题 + 图片 → 图片在剩余空间居中
6. **多内容块** — 标题 + 多个内容块 → 第二个块起自动添加间距

### 封面幻灯片模板

封面通常是居中的，包含标题、副标题和作者信息：

```markdown
<!-- .slide: class="center" -->

# Presentation Title

### Subtitle or Description

&nbsp;

Author Name | Year
```

### 章节标题模板

每个章节的第一页通常是居中的章节标题：

```markdown
<!-- .slide: class="center" -->

## Chapter N: Chapter Title

*Brief description or question*
```

### 内容幻灯片最佳实践

- **每页聚焦一个要点**，不要塞太多内容
- **列表项控制在 8 项以内**，超过会自动分列（如果不想分列，拆成多页）
- **善用子幻灯片**（`----` 分隔符）将相关内容组织在一起
- **善用表格**展示对比或结构化数据
- **善用引用块**（`>`）突出关键观点
- **代码块**会自动高亮和添加行号（`.text` 类除外）
- **图片**使用标准 Markdown 语法：`![alt](path/to/image.svg)`

### LaTeX 公式

支持行内公式和块级公式：

```markdown
行内公式：$E = mc^2$

块级公式：

$$
L = -\sum_{i} p(x_i) \log q(x_i)
$$
```

### 图片处理

- **语言无关的图片**放在 `assets/` 目录：`![Diagram](assets/diagram.svg)`
- **语言特定的图片**放在 `src/{lang}/images/`，各语言的 markdown 引用各自路径
- 优先使用 **SVG** 格式，清晰且可缩放

## 创建项目

### 步骤 1：创建 GitHub 仓库

```bash
# 创建仓库（公开，用于 GitHub Pages）
gh repo create {owner}/{repo-name} --public --clone
cd {repo-name}
```

### 步骤 2：生成 index.html

根据用户需求生成 `index.html`，包含：
- 正确的 `<meta>` 标签（charset、viewport、description、author）
- Open Graph 标签（og:title、og:description、og:image、og:url）
- `<link>` 引用 `slides.css`
- `<script>` 引用 `slides.js` 并调用 `Slides.init()`

**参考模板**（双语演示文稿）：

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Presentation Title</title>
    <meta name="description" content="A brief description of the presentation.">
    <meta name="author" content="Johnson Lee">
    <meta property="og:type" content="website">
    <meta property="og:title" content="Presentation Title">
    <meta property="og:description" content="A brief description.">
    <meta property="og:url" content="https://{subdomain}.johnsonlee.io/">
    <link rel="stylesheet" href="https://slides.johnsonlee.io/slides.css">
  </head>
  <body>
    <script src="https://slides.johnsonlee.io/slides.js"></script>
    <script>
      Slides.init({
        title: 'Presentation Title',
        chapters: ['cover', 'chapter-1', 'chapter-2', 'references'],
        langs: { en: 'EN', zh: '中文' }
      });
    </script>
  </body>
</html>
```

### 步骤 3：创建目录和内容文件

```bash
mkdir -p src/en src/zh assets
```

按章节编写 Markdown 文件。

### 步骤 4：配置 GitHub Pages

```bash
# 推送代码
git add -A && git commit -m "init: slides project" && git push -u origin main

# 启用 GitHub Pages（main 分支根目录）
gh api repos/{owner}/{repo-name}/pages --method POST --field source='{"branch":"main","path":"/"}'
```

如果需要自定义域名（如 `{name}.johnsonlee.io`），创建 `CNAME` 文件：

```bash
echo "{name}.johnsonlee.io" > CNAME
```

## 内容编写指南

### 演讲风格

- **深入浅出**：用简单的类比解释复杂概念
- **循序渐进**：每页只引入一个新概念
- **互动感**：善用反问句引导思考
- **具体化**：用真实的例子而非抽象描述
- **节奏感**：复杂内容之后穿插简单的总结页或过渡页

### 章节结构建议

一个完整的演示文稿通常包含：

1. **cover** — 封面（标题、副标题、作者）
2. **preface/outline** — 前言或大纲（可选）
3. **chapter-1 ~ chapter-N** — 正文章节
4. **references** — 参考资料（可选）

每个章节建议 5-15 页幻灯片，整体控制在 30-60 分钟的演讲时长。

### 章节总结页

每章末尾建议有一个总结页，用表格或要点回顾本章关键内容：

```markdown
## Chapter N Summary

| Topic | Key Idea |
|-------|----------|
| Concept A | One-sentence explanation |
| Concept B | One-sentence explanation |
| Concept C | One-sentence explanation |
```

### 思考题

每章末尾可选加一页"Think About It"，提出 3-4 个开放性问题：

```markdown
## Think About It

- Question 1 that connects to the content?
- Question 2 that encourages deeper thinking?
- Question 3 that relates to real life?
```

## 推送和部署

### 推送确认

推送前**必须**跟用户确认：

1. 展示章节结构和关键内容
2. 确认仓库名和部署地址
3. 明确告知将要 push 到哪个 repo 的哪个分支

只有用户明确确认后才执行推送。

### 推送命令

```bash
git add -A && git commit -m "content: add slides" && git push
```

### 更新内容

修改内容后：

```bash
git add -A && git commit -m "content: update {chapter}" && git push
```

GitHub Pages 会自动部署更新。

## 从对话生成演示文稿

如果用户在讨论完一个话题后说"把这个做成 slides"，流程是：

1. 回顾对话内容，提炼核心要点和逻辑结构
2. 设计章节划分和每章的幻灯片页数
3. 用演讲风格重新组织内容（不是简单搬运对话）
4. 补充过渡页和总结页，保证演讲的流畅性
5. 按正常流程让用户确认和推送
