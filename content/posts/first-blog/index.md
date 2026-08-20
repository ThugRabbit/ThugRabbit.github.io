+++
date = '2026-07-16T16:50:09-03:00'
draft = false
title = 'Hugo Leaf Bundle 使用指南 / Hugo Leaf Bundle Guide'
+++

## 简介 / Introduction

Hugo 的 page bundle（页面包）把一篇内容和它使用的图片、音频、附件等资源放在同一个目录中。对于博客文章，leaf bundle 通常是最直观的组织方式：每篇文章拥有独立文件夹，正文固定为 `index.md`，相关资源与正文一起保存。

A Hugo page bundle keeps a page and its related resources—such as images, audio, and attachments—in the same directory. For blog posts, a leaf bundle is often the clearest structure: every post has its own folder, its content is stored in `index.md`, and its resources live beside it.

本文本身就是一个 leaf bundle：正文位于 `content/posts/first-blog/index.md`，示例图片 `bluerobot.png` 位于同一个目录。

This article is itself a leaf bundle: the content is stored in `content/posts/first-blog/index.md`, with the example image `bluerobot.png` in the same directory.

## Leaf Bundle 的目录结构 / Leaf Bundle Structure

一个简单的博客目录可以这样组织：

A simple blog can be organized like this:

```text
content/
└── posts/
    ├── first-blog/
    │   ├── index.md
    │   └── bluerobot.png
    └── uv/
        └── index.md
```

只要目录根部包含 `index.md`，Hugo 就会把该目录识别为 leaf bundle。图片等文件会成为这篇文章的 page resources，并且只与当前 bundle 关联。

When a directory contains `index.md` at its root, Hugo treats it as a leaf bundle. Files such as images become page resources associated with that bundle.

Leaf bundle 是内容树的末端，不能包含子页面。目录中的其他 Markdown 文件会被视为 page resources，而不会生成独立页面。

A leaf bundle is an endpoint in the content tree and cannot contain descendant pages. Other Markdown files inside it are treated as page resources instead of being rendered as independent pages.

## Leaf Bundle 与 Branch Bundle / Leaf Bundle vs. Branch Bundle

Hugo 支持两种 page bundle。最容易辨认它们的方法是查看入口文件名。

Hugo supports two types of page bundles. The easiest way to distinguish them is by their index filename.

| 特性 / Feature | Leaf bundle | Branch bundle |
| --- | --- | --- |
| 入口文件 / Index file | `index.md` | `_index.md` |
| 常见用途 / Typical use | 单篇文章、项目页 / A post or standalone page | 栏目、分类入口 / A section or category page |
| 子页面 / Descendant pages | 不支持 / Not supported | 支持 / Supported |
| 常见路径 / Example | `content/posts/my-post/index.md` | `content/posts/_index.md` |

博客文章通常使用 leaf bundle；需要容纳多篇子内容的栏目使用 branch bundle。例如，`posts` 可以是一个 branch，而 `posts/first-blog` 是这个 branch 末端的一片 leaf。

Blog posts normally use leaf bundles, while a section containing multiple pages uses a branch bundle. For example, `posts` can be a branch, and `posts/first-blog` can be a leaf at the end of that branch.

## 创建 Leaf Bundle / Creating a Leaf Bundle

可以直接创建目录和 `index.md`，也可以使用 Hugo 命令：

You can create the directory and `index.md` manually, or use the Hugo command:

```bash
hugo new content posts/my-post/index.md
```

最小的 `index.md` 使用 front matter 描述文章信息，正文紧随其后：

A minimal `index.md` uses front matter for page metadata, followed by the article body:

```toml
+++
date = '2026-08-20T10:00:00-03:00'
draft = true
title = 'My Post'
+++
```

```markdown
## Hello

This is my new post.
```

写作时可以保持 `draft = true`。准备发布后改为 `draft = false`，再执行正式构建。

Keep `draft = true` while writing. Change it to `draft = false` when the article is ready for a production build.

## 使用同目录资源 / Using Co-Located Resources

把图片放进文章目录后，可以直接使用相对于 `index.md` 的路径：

After placing an image in the article directory, reference it with a path relative to `index.md`:

```markdown
![Blue robot](bluerobot.png)
```

渲染效果如下：

The rendered result appears below:

![Blue robot](bluerobot.png)

资源也可以放进子目录，让文章目录保持整洁：

Resources can also be placed in subdirectories to keep the bundle organized:

```text
my-post/
├── index.md
├── images/
│   ├── cover.jpg
│   └── diagram.png
└── downloads/
    └── example.zip
```

对应的 Markdown 引用为：

The corresponding Markdown references are:

```markdown
![Cover](images/cover.jpg)
[Download the example](downloads/example.zip)
```

## 为什么使用 Leaf Bundle / Why Use Leaf Bundles

- 文章与资源一起移动、复制或删除，不容易留下孤立文件。 / The article and its resources can be moved, copied, or deleted together without leaving orphaned files.
- 不同文章可以使用相同的资源文件名，例如各自拥有 `cover.jpg`。 / Different posts can reuse filenames such as `cover.jpg` without collisions.
- Markdown 中的相对路径简短直观。 / Relative paths in Markdown stay short and readable.
- 主题和模板可以通过 Hugo 的 `.Resources` API 查找和处理图片等资源。 / Themes and templates can find and process resources through Hugo's `.Resources` API.
- 文章目录可以作为一个完整单元进行版本管理。 / Each article directory acts as a self-contained unit in version control.

## 图片处理说明 / Notes on Image Processing

普通 Markdown 图片语法适合直接显示原图。如果需要调整尺寸、裁剪、转换格式或生成响应式图片，Hugo 也可以在构建时处理 page resources，但通常需要主题模板、render hook 或 shortcode 配合。

Standard Markdown image syntax is suitable for displaying the original image. To resize, crop, convert, or generate responsive images, Hugo can process page resources during the build, usually through a theme template, render hook, or shortcode.

因此，leaf bundle 并不会限制图片定制；它反而让 Hugo 能够把图片识别为与页面关联的资源。具体显示效果仍取决于当前主题及其 CSS。

Leaf bundles do not prevent image customization. Instead, they allow Hugo to recognize images as resources associated with a page. The final presentation still depends on the active theme and its CSS.

## 选择建议 / Practical Guidance

对于独立博客文章、教程或项目记录，优先使用 `文章目录/index.md`。当一个目录需要作为栏目首页，并继续包含多篇子文章时，再使用 `_index.md` 创建 branch bundle。

For an individual blog post, tutorial, or project note, prefer `post-directory/index.md`. Use `_index.md` for a branch bundle when the directory needs its own section page and must contain descendant content.

简单记忆：`index.md` 表示一篇最终内容，`_index.md` 表示一个还可以继续向下组织内容的入口。

A simple rule of thumb: `index.md` represents an endpoint page, while `_index.md` represents a section that can continue branching into more content.

## 官方文档 / Official Documentation

- [Hugo Page Bundles](https://gohugo.io/content-management/page-bundles/)
- [Hugo Page Resources](https://gohugo.io/content-management/page-resources/)
- [Hugo Image Processing](https://gohugo.io/content-management/image-processing/)
