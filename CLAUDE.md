# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

Hexo 7 博客（中文技术博客，Java 方向），站点名 "Pairs' Blog"。GitHub 上共两个仓库：

1. **源码仓库**（本仓库，`pairs-vip/source-pairs-vip.github.io`）：Hexo 源码、文章、图片，附件也放本仓库的 Release 中（tag 固定 `assets`）
2. **展示仓库**（`pairs-vip/pairs-vip.github.io`）：`hexo deploy` 推送生成的静态页面到这里，由 GitHub Pages 托管

旧附件仓库 `pairs-vip/blog-downloads` 已弃用，附件迁移至本仓库 Release。

## 从 0 搭建环境

新机器 clone 后的初始化步骤：

```bash
git clone git@github.com:pairs-vip/source-pairs-vip.github.io.git
cd source-pairs-vip.github.io
npm install      # 依据 package-lock.json 还原依赖（node_modules 不进 Git）
npx hexo server  # 预览 http://localhost:4000
```

前置条件：Node.js（LTS 版本）；`hexo deploy` 需要 GitHub SSH key（deploy 配置为 SSH 地址）。主题 `hexo-theme-inside` 在仓库 `themes/` 内，无需单独安装。

## 常用命令

```bash
npx hexo server      # 本地预览，http://localhost:4000
npx hexo clean       # 清除 db.json 缓存与 public/
npx hexo generate    # 生成静态文件到 public/
npx hexo deploy      # 部署 public/ 到展示仓库
npx hexo new post "标题"   # 新建文章，生成 source/_posts/标题.md
```

## 新增一篇博客的完整流程

顺序很重要：**图片必须先 push 到 GitHub，jsDelivr 链接才生效**。

1. 写文章：`source/_posts/` 新建 `.md`，front-matter 按约定填写
2. 图片入位：放 `static_resource/<主题目录>/`，文章内用 jsDelivr CDN 链接引用（push 前链接 404 属正常）
3. 上传附件（如有）：GitHub 网页操作，传到本仓库 Release（tag `assets`），把下载链接写进文章
4. 推送源码仓库：`git add` 文章与图片 → `git commit` → `git push origin main`，之后 jsDelivr 开始分发（首次有几分钟延迟）
5. 本地预览：`npx hexo clean && npx hexo server`，检查排版、图片、附件链接
6. 部署站点：`npx hexo clean && npx hexo generate && npx hexo deploy`（生成产物已纳入 .gitignore，无需提交）

两个顺序约束：图片先 push 再预览（否则预览时图片 404）；改配置或主题后必须先 `hexo clean` 再 generate，否则缓存可能导致改动不生效。

## 架构与关键约定

### 图片引用（重要）

文章图片**不能**用本地相对路径，必须走 jsDelivr CDN 引用本仓库文件：

```markdown
![描述](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/<主题目录>/<图片名>.png)
```

图片文件放在 `static_resource/<主题目录>/` 下并提交到本仓库，CDN 会从 GitHub 拉取。新文章配图时按同样规则组织。

### 附件引用

文章附件（软件包、demo 源码等）放在**本仓库**（source）的 GitHub Release 中：tag 固定为 `assets`，一个 Release 承载全部附件，只增不改。文章里通过 Releases 下载链接引用：

```html
<a href="https://github.com/pairs-vip/source-pairs-vip.github.io/releases/download/assets/<文件名>" download="<文件名>">
```

注意：Release 附件不进 Git 历史、不受 Git 的 100MB 单文件限制（上限 2GB）；同一 Release 内附件文件名必须全局唯一（重名会被 GitHub 自动改名导致链接错位），按 `<主题>-<描述>.<后缀>` 命名。

### 文章规范

- 文章在 `source/_posts/*.md`，文件名即 URL 中的 `:title`（permalink 格式 `:year/:month/:day/:title/`，URL 使用文章文件名）
- front-matter 约定字段：`title`、`date`、`tags`、`categories`（如 `dev`/`ops`）、`author: pairs`、`top`、`cover`、`toc: true`
- 正文必须使用中文；技术类文章常用标签：JVM、GC、docker、dubbo、nacos、rabbitmq、kibana、性能优化、tips

### 主题

- 当前主题为 `themes/hexo-theme-inside`（本地拷贝，非 npm 依赖）；`themes/hexo-theme-cactus` 为未启用的旧主题
- 主题配置在 `themes/hexo-theme-inside/_config.yml`；根 `_config.yml` 的 `theme_config:` 段可覆盖（当前仅覆盖 favicon）
- 页面 `source/about/`、`source/links/` 为独立页面

### Git 说明

`.gitignore` 忽略四类可重建文件：`node_modules/`（依赖）、`public/`、`db.json`、`.deploy_git/`（生成产物），clone 后需 `npm install` 才能运行，生成产物不再提交。提交信息遵循全局规范（Conventional Commits 中文格式）。
