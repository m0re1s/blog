# m0re1s 个人主页

> 纯前端静态博客站点，支持 Markdown / LaTeX / PDF 三种文章类型，可通过 GitHub Pages 部署。
>
> 在线访问：**https://m0re1s.github.io/blog/**

## 项目简介

这是一个轻量级的个人博客网站，无需任何后端服务或构建工具。所有文章以 Markdown 文件形式存放，通过 `marked.js` 渲染为 HTML，`KaTeX` 渲染数学公式。页面结构极简：一个主页（文章列表）+ 一个文章页（动态加载内容）。

## 页面路由

| URL | 页面 | 说明 |
|---|---|---|
| `/` 或 `/index.html` | 主页 | 静态 HTML，展示文章列表 |
| `/post.html?file=xxx.md` | 文章页 | 通过 URL 参数指定文章文件名 |
| `/xxxxxx`（6 位短码） | 文章页 | 短链接，由 `404.html` 解析后跳转到对应文章页 |

## 短链接

每篇文章可通过 6 位短码访问，如 `https://m0re1s.github.io/blog/fqkqyo`：

- 短码 = 文件名的 djb2 哈希（模 36⁶）转 6 位 base36，**由文件名确定性派生**，新增文章无需任何注册步骤
- **文章页底部自动显示本文短链**并提供一键复制按钮，读者无需查询；短码由 `post.html` 在浏览器本地用同一算法计算
- GitHub Pages 对不存在的路径会返回仓库根的 `404.html`，其内嵌脚本从 `index.html` 解析出全部文章文件名、逐个计算短码并匹配，命中后跳转 `post.html?file=...`
- **不要重命名文章文件**，否则短码随之改变，旧短链接失效
- 该机制依赖 GitHub Pages 的 404 回退，本地 `python -m http.server` 下不可用（本地预览仍走 `post.html?file=...`）；`404.html` 与 `post.html` 各有一份 `shortCode` 实现，**改动算法须同步两处**

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

## 本地开发

直接用浏览器打开 `index.html` 会因 `file://` 协议的 CORS 限制导致文章加载失败。必须启动本地服务器：

```bash
python -m http.server 8000
```

访问 http://localhost:8000 即可正常浏览。

## 部署

推送到 GitHub，在仓库 Settings → Pages 中选择分支和根目录 `/`，站点将发布在 `https://m0re1s.github.io/仓库名/`。所有链接使用相对路径，子路径部署兼容。