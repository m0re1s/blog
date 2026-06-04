# m0re1s 个人主页

> 纯前端静态博客站点，支持 Markdown / LaTeX / PDF 三种文章类型，可通过 GitHub Pages 部署。

## 文件结构

```
├── index.html          # 主页：个人介绍 + 文章列表
├── post.html           # 文章页：动态加载并渲染 Markdown
├── style.css           # 全局样式（主页 + 文章页共用）
├── assets/
│   └── example.pdf     # PDF 资源文件
├── posts/
│   ├── example-md.md   # Markdown 格式文章
│   ├── example-tex.md  # 含 LaTeX 数学公式的文章
│   └── example-pdf.md  # 嵌入 PDF 的文章
└── README.md           # 项目文档
```

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

## 新增文章

1. 在 `posts/` 目录创建 `.md` 文件
2. 如需嵌入 PDF，将 PDF 放入 `assets/`，在 `.md` 中写 `<iframe src="assets/xxx.pdf"></iframe>`
3. 在 `index.html` 文章列表添加条目：

```html
<li><span class="tag tag-md">MD</span> <a href="post.html?file=新文件名.md">文章标题</a></li>
```

## 本地开发


```bash
python -m http.server 8000
# 或
npx serve .
```

访问 `http://localhost:8000` 即可正常浏览。

## 部署

推送到 GitHub，在仓库 Settings → Pages 中选择分支和根目录 `/`，站点将发布在 `https://m0re1s.github.io/仓库名/`。所有链接使用相对路径，子路径部署兼容。