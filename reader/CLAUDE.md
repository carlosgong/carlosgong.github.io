# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 沟通礼仪
* 使用中文进行回复
* 语气言简意赅，中立专业

## 项目概述
这是一个静态文章收藏系统：
- `md/` 目录存放原始 Markdown 文章（带 frontmatter: source, clipped）
- `web/posts/` 目录存放转换后的 HTML 文章页面
- `index.html` 是文章目录首页

## 文件结构
```
├── index.html                    # 文章目录首页
├── md/                           # Markdown 源文件
│   └── full-metal-jacket.md
└── web/
    ├── assets/
    │   ├── style.css             # 共享样式
    │   └── images/               # 文章图片
    └── posts/                    # HTML 文章页面
        └── full-metal-jacket.html
```

## 添加新文章完整流程

### 第一阶段：翻译（如原文非中文）

1. **检查翻译提示**：阅读 `docs/translationPrompt.md` 了解翻译要求

2. **创建中文翻译文件**：
   - 源文件：`md/{article-name}.md`
   - 输出：`md/{article-name}-cn.md`
   - 按照翻译提示要求：
     - 完整保留原文信息点
     - 行文通顺，符合中文习惯，避免机械翻译
     - 保留原文情感、立场、措辞风格
     - 保留所有链接和图片引用
     - 不添加额外解释
     - 提供全文翻译
   - frontmatter 保持不变（source, clipped）

### 第二阶段：处理图片（如文章包含图片）

1. **下载图片到本地**：
   - 创建目录：`web/assets/images/`（如不存在）
   - 下载文章中引用的图片，保存为 `web/assets/images/{image-name}.jpg`
   - 使用 curl 命令下载：
     ```bash
     curl -L -o "web/assets/images/{image-name}.jpg" "{image-url}"
     ```

2. **确保 CSS 图片样式存在**（在 `web/assets/style.css` 中）：
   ```css
   .content img {
     max-width: 100%;
     height: auto;
     border-radius: 6px;
     margin: 20px 0;
   }

   .content figure {
     margin: 20px 0;
   }

   .content figcaption {
     font-size: 14px;
     color: #666;
     text-align: center;
     margin-top: 8px;
     font-style: italic;
   }
   ```

3. **HTML 中引用本地图片**：
   - 使用 `<figure>` 和 `<figcaption>` 包裹图片
   - 图片路径：`../assets/images/{image-name}.jpg`
   - 示例：
     ```html
     <figure>
       <a href="../assets/images/example.jpg">
         <img src="../assets/images/example.jpg" alt="图片描述">
       </a>
       <figcaption>图片说明文字</figcaption>
     </figure>
     ```

### 第三阶段：创建 HTML 页面

采用双文件方案（源语版 + 中文版）：

1. **创建源语版 HTML**：
   - 复制 `web/posts/full-metal-jacket.html` 作为模板
   - 重命名为 `web/posts/{article-name}.html`
   - 替换内容：
     - 标题（第5行 `# ` 后的内容）
     - 来源链接（frontmatter 中的 `source`）
     - 收藏时间（frontmatter 中的 `clipped`）
     - 正文（Markdown → HTML）
   - 语言切换器：`<span>源语</span> / <a href="{article-name}-cn.html">中文</a>`

2. **创建中文版 HTML**：
   - 复制刚创建的源语版
   - 重命名为 `web/posts/{article-name}-cn.html`
   - 内容替换为中文翻译（来自 `md/{article-name}-cn.md`）
   - 语言切换器改为：`<a href="{article-name}.html">源语</a> / <span>中文</span>`
   - 页面标题可添加 `(中文)` 后缀

### 第四阶段：更新首页

**重要：文章按倒序排列，最新收藏的文章在最上方。**

在 `index.html` 的 `.article-list` 中，在 `list-header` 之后**第一个位置**插入新行（链接指向源语版）：
```html
<div class="article-row">
  <a href="web/posts/{article-name}.html" class="title">{标题}</a>
  <span class="source">{来源域名}</span>
  <span class="clipped">{收藏日期}</span>
</div>
```

即：新文章行应该紧跟在 `</div>`（list-header 结束标签）之后，位于所有现有文章行之前。

### 文件结构示例
```
├── md/
│   ├── article.md              # 源语 Markdown
│   └── article-cn.md           # 中文翻译（同一份原文）
├── web/
│   ├── assets/
│   │   ├── style.css           # 共享样式
│   │   └── images/             # 文章图片
│   │       └── article-image.jpg
│   └── posts/
│       ├── article.html        # 源语 HTML（含切换到中文的链接）
│       └── article-cn.html     # 中文 HTML（含切换到源语的链接）
```

### 关键注意事项
- 首页只链接源语版 HTML
- 中英文页面通过 `.lang-switch` 互相切换
- 翻译时保留原文 frontmatter，标题行保留原文不翻译
- 所有页面共享 `web/assets/style.css` 样式
- **首页文章按倒序排列：最新收藏的文章插入到列表最顶部（list-header 之后第一个位置）**
