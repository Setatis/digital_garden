---
publish: true
title: Quartz公开笔记站部署指南
created: 2026-10-28
modified: 2026-10-31
tags:
  - 主题/学业
---

# Quartz公开笔记站部署指南

> 用 Quartz 把 Markdown 笔记编译成静态网站，用 GitHub Pages 免费托管。一次配置，之后推送即为发布。

## 一、适用场景

想让别人打开一个链接就能看你的笔记，不买服务器，只公开其中一部分。

需要三样东西：GitHub 账号、一个 public 仓库、Quartz 模板（默认分支 `v5`）。

- Quartz：把 Markdown 编译成 HTML 页面，开源免费。
- GitHub Pages：托管这些页面，地址形如 `https://用户名.github.io/仓库名/`。

## 二、部署步骤

### 建仓库

打开 `https://github.com/jackyzha0/quartz`，点 **Use this template → Create a new repository**，可见性选 **Public**，分支保持 `v5`。

### 启用 Actions

`Settings → Actions → General`，选 **Allow all actions and reusable workflows**，保存。关着的话工作流不会执行，界面上也没有运行记录。

### 写站点配置

把 `quartz.config.default.yaml` 复制为 `quartz.config.yaml`，改三行：

```yaml
configuration:
  pageTitle: Digital Garden
  locale: zh-CN
  baseUrl: 用户名.github.io/仓库名
```

`pageTitle` 是站点名，`locale` 是界面语言，`baseUrl` 是站点根网址。`baseUrl` 去掉 `https://`、末尾不加斜杠。写错了会出现「网页能打开、样式全丢」。

### 写部署工作流

模板自带的工作流带一行 `if: ${{ github.repository == 'jackyzha0/quartz' }}`，只在官方仓库运行，复制出来的仓库每次都显示 skipped。

把 `.github/workflows/deploy.yml` 整份替换为：

```yaml
name: Deploy Quartz to GitHub Pages

on:
  push:
    branches: [v5]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - uses: actions/setup-node@v6
        with:
          node-version: 24
          cache: npm
      - run: npm ci
      - run: npx quartz build -d content
      - uses: actions/configure-pages@v5
      - uses: actions/upload-pages-artifact@v4
        with:
          path: public

  deploy:
    environment:
      name: github-pages
    runs-on: ubuntu-latest
    needs: build
    steps:
      - uses: actions/deploy-pages@v4
```

| 段落 | 作用 |
| --- | --- |
| `on` | 推送 `v5` 自动跑；`workflow_dispatch` 在 Actions 页面留一个手动运行按钮 |
| `permissions` | `pages: write` 才能发布到 Pages；`id-token: write` 换一张短期身份凭证，仓库里不用存密码 |
| `concurrency` | 同一时间只跑一个部署，后到的排队，避免两次部署互相覆盖 |
| `npm ci` | 按 `package-lock.json` 安装依赖 |
| `npx quartz build -d content` | 把 `content/` 编译到 `public/` |
| `upload-pages-artifact` | 把 `public/` 打包交给部署环节 |
| `deploy` | `needs: build`，构建成功才上线 |

### 推送

网页上 **Add file → Create new file**，文件名填 `.github/workflows/deploy.yml`，粘贴内容，提交到 `v5`；或者在本地 `git push origin v5`。

### Pages 来源选 GitHub Actions

`Settings → Pages → Source` 选 **GitHub Actions**。少了这步，工作流跑成功，网址仍然 404。

### 手动跑一次

**Actions** → 左侧 **Deploy Quartz to GitHub Pages** → **Run workflow** → 分支 `v5`。3 到 5 分钟，build 与 deploy 两个作业变绿即完成，deploy 里能拿到访问地址。

### 补首页

没有 `content/index.md` 时，根地址拿不到 `index.html`，返回的是订阅源 XML，屏幕上是一串代码。新建这个文件：

```markdown
---
title: 首页
---

# Digital Garden

欢迎来到我的数字花园。
```

推送后自动重建，首页正常显示。

## 三、日常更新

- Obsidian 装 **Quartz Syncer** 插件，填仓库名、分支 `v5`、GitHub token，勾选要公开的笔记，点发布。两三分钟后网站更新。
- 不装插件也行：GitHub 网页把 `.md` 拖进 `content/` 目录提交。
- 发出去的链接手机能直接打开，站内自带搜索、双链跳转、RSS 订阅。
