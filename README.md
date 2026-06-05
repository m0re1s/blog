# m0re1s 个人主页

> 纯前端静态博客站点，支持 Markdown / LaTeX / PDF 三种文章类型，可通过 GitHub Pages 部署。

## 项目简介

这是一个轻量级的个人博客网站，无需任何后端服务或构建工具。所有文章以 Markdown 文件形式存放，通过 `marked.js` 渲染为 HTML，`KaTeX` 渲染数学公式。页面结构极简：一个主页（文章列表）+ 一个文章页（动态加载内容）。

## 文件结构

```
├── index.html          # 主页：个人介绍 + 文章列表
├── post.html           # 文章页：动态加载并渲染 Markdown
├── style.css           # 全局样式（主页 + 文章页共用）
├── .gitignore          # 忽略 local_docs/ 等目录
├── assets/
│   └── example.pdf     # PDF 资源文件
├── posts/
│   ├── example-md.md   # Markdown 格式文章
│   ├── example-tex.md  # 含 LaTeX 数学公式的文章
│   ├── example-pdf.md  # 嵌入 PDF 的文章
│   └── geometry-perpendicular.md  # 平面几何题目（含 GeoGebra 交互图形）
├── local_docs/         # ⛔ 本地文档目录（不纳入 git）
│   ├── DEPLOY_GUIDE.md # 部署指南（SSH、Git 配置等）
│   ├── USAGE_GUIDE.md  # 使用指南（本地开发、新增文章等）
│   └── PROBLEM_LOG.md  # 问题与解决方案记录
└── README.md           # 项目说明（本文件）
```

> ⚠️ `local_docs/` 目录已被 `.gitignore` 排除，不会推送到远程仓库。该目录包含个人部署配置等敏感信息，仅供本地参考。

## 页面路由

| URL | 页面 | 说明 |
|---|---|---|
| `/` 或 `/index.html` | 主页 | 静态 HTML，展示文章列表 |
| `/post.html?file=xxx.md` | 文章页 | 通过 URL 参数指定文章文件名 |

文章列表在 `index.html` 中**硬编码**，新增文章需手动在 `<ul class="post-list">` 中添加 `<li>` 条目。

## 文章加载流程

```
用户点击链接 → post.html?file=xxx.md
    → 读取 URL 参数 file
    → fetch('posts/xxx.md') 获取 Markdown 文本
    → marked.parse() 转为 HTML 并写入 #article
    → renderMathInElement() 渲染 LaTeX 公式
    → 更新页面标题为 h1 内容
```

## 外部依赖（CDN）

| 库 | 版本 | 用途 | 加载位置 |
|---|---|---|---|
| marked.js | 12.0.2 | Markdown → HTML | post.html |
| KaTeX | 0.16.10 | LaTeX 数学公式渲染 | post.html |
| KaTeX auto-render | 0.16.10 | 自动识别 `$...$` / `$$...$$` | post.html |

主页 `index.html` 无外部 JS 依赖，纯静态 HTML + CSS。

## 文章类型

所有文章为 `.md` 文件，置于 `posts/` 目录，按内容特征分三类：

- **MD**（绿色标签 `tag-md`）：纯 Markdown，代码/文字内容
- **TeX**（红色标签 `tag-tex`）：含 `$...$` / `$$...$$` LaTeX 数学公式
- **PDF**（蓝色标签 `tag-pdf`）：通过 `<iframe src="assets/xxx.pdf">` 嵌入 PDF

还支持嵌入外部交互图形（如 GeoGebra），使用 `<iframe>` 标签即可。

## 新增文章

1. 在 `posts/` 目录创建 `.md` 文件
2. 如需嵌入 PDF，将 PDF 放入 `assets/`，在 `.md` 中写 `<iframe src="assets/xxx.pdf"></iframe>`
3. 在 `index.html` 文章列表添加条目：

```html
<li><span class="tag tag-md">MD</span> <a href="post.html?file=新文件名.md">文章标题</a></li>
```

⚠️ **两步缺一不可**：仅创建 `.md` 文件不会自动出现在主页上，必须在 `index.html` 中注册链接。

## 本地开发

直接用浏览器打开 `index.html` 会因 `file://` 协议的 CORS 限制导致文章加载失败。必须启动本地服务器：

```bash
python -m http.server 8000
```

访问 http://localhost:8000 即可正常浏览。

## 部署

推送到 GitHub，在仓库 Settings → Pages 中选择分支和根目录 `/`，站点将发布在 `https://m0re1s.github.io/仓库名/`。所有链接使用相对路径，子路径部署兼容。

详细部署步骤见 `local_docs/DEPLOY_GUIDE.md`（本地文档，不纳入 git）。