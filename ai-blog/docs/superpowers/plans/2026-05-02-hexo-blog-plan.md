# Hexo 个人博客实施计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 搭建基于 Hexo + Butterfly 主题的个人博客，自动化部署到自有云服务器

**Architecture:** Hexo 静态博客通过 GitHub Actions 自动构建，rsync 部署到云服务器 Nginx

**Tech Stack:** Hexo 7.x, Butterfly 主题, GitHub Actions, rsync, Nginx

---

## 文件结构

```
ai-blog/
├── hexo/                          # Hexo 项目 (新建)
│   ├── package.json
│   ├── _config.yml
│   ├── source/_posts/             # 文章源文件
│   └── themes/butterfly/           # 主题 (通过 npm 安装)
├── .github/workflows/deploy.yml    # CI/CD 配置 (新建)
└── docs/
    ├── specs/                      # 设计文档
    └── plans/                      # 本计划
```

---

## Task 1: 初始化 Hexo 项目

**Files:**
- Create: `hexo/package.json`
- Create: `hexo/_config.yml`
- Create: `hexo/source/_posts/hello-world.md`

- [ ] **Step 1: 创建 hexo 目录并初始化**

```bash
mkdir -p hexo
cd hexo
npm init -y
```

- [ ] **Step 2: 安装 Hexo 和所需插件**

```bash
npm install hexo-cli hexo-generator-sitemap hexo-generator-feed
npm install hexo-renderer-marked
npm install butterfly-theme
```

- [ ] **Step 3: 创建 Hexo 配置文件 `_config.yml`**

```yaml
# Site
title: 我的博客
subtitle: ''
description: ''
keywords:
author: 你的名字
language: zh-CN
timezone: ''

# URL
url: https://blog.xxx.com
root: /
permalink: :year/:month/:day/:title/
permalink_defaults:

# Directory
source_dir: source
public_dir: public
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code
i18n_dir: :lang
skip_render:

# Writing
new_post_name: :title.md
default_layout: post
titlecase: false
external_link:
  enable: true
  field: site
  exclude: ''
filename_case: 0
render_drafts: false
post_asset_fallback: false
show_future: true
marked:
  enable: false

# Category & Tag
default_category: uncategorized
category_map:
tag_map:

# Date / Time format
date_format: YYYY-MM-DD
time_format: HH:mm:ss
updated_option: mtime

# Pagination
per_page: 10
pagination_dir: page

# Include / Exclude
exclude:
exclude:

# Output
public_dir: public

# Deploy
deploy:
  type: ''
```

- [ ] **Step 4: 创建示例文章 `source/_posts/hello-world.md`**

```markdown
---
title: Hello World
date: 2026-05-02 12:00:00
categories:
  - 日记
tags:
  - 随笔
---

欢迎来到我的博客！这是一篇示例文章。

## 标题

内容...
```

- [ ] **Step 5: 测试本地构建**

```bash
npx hexo server
```

访问 http://localhost:4000 确认博客可以正常显示

- [ ] **Step 6: 提交**

```bash
git add hexo/
git commit -m "feat: 初始化 Hexo 项目"
```

---

## Task 2: 配置 Butterfly 主题

**Files:**
- Modify: `hexo/_config.yml` (添加 theme 配置)
- Create: `hexo/themes/butterfly/_config.yml` (主题配置)

- [ ] **Step 1: 在 `_config.yml` 中设置主题**

```yaml
theme: butterfly
```

- [ ] **Step 2: 创建 Butterfly 主题配置 `themes/butterfly/_config.yml`**

```yaml
# 菜单
menu:
  Home: / || fa-solid fa-home
  Archives: /archives || fa-solid fa-archive
  Tags: /tags || fa-solid fa-tags
  About: /about || fa-solid fa-user

# 侧边排版
sidebar:
  enable: true
  position: left
  touchStart: 200

# 头像
avatar:
  url: /img/avatar.jpg
  effect: true

# 社交信息
social:
  GitHub: https://github.com/yourname || fa-brands fa-github
  Twitter: https://twitter.com/yourname || fa-brands fa-twitter

# 主页排版
index_page:
  top_grouping: all
  top: true

# 文章页配置
article:
  highlight:
    enable: true
    line_number: true
  copy:
    enable: true

# 评论
comments:
  enable: true
  type: waline
  waline:
    serverURL: https://your-waline-server.com
```

- [ ] **Step 3: 本地预览确认主题生效**

```bash
cd hexo
npx hexo clean
npx hexo server
```

- [ ] **Step 4: 提交**

```bash
git add hexo/
git commit -m "feat: 配置 Butterfly 主题"
```

---

## Task 3: 配置 Nginx

**Files:**
- Create: `docs/nginx-blog.conf` (Nginx 配置参考)

- [ ] **Step 1: 创建 Nginx 配置文件参考 `docs/nginx-blog.conf`**

```nginx
server {
    listen 80;
    server_name blog.xxx.com;

    # 强制跳转 HTTPS
    return 301 https://blog.xxx.com$request_uri;
}

server {
    listen 443 ssl http2;
    server_name blog.xxx.com;

    # SSL 配置 (使用你已有的证书)
    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/cert.key;
    ssl_session_timeout 1d;
    ssl_session_cache shared:SSL:50m;
    ssl_session_tickets off;

    # 博客静态文件目录
    root /var/www/blog;

    # 缓存配置
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|eot)$ {
        expires 30d;
        add_header Cache-Control "public, immutable";
    }

    # 默认请求处理
    location / {
        try_files $uri $uri/ /index.html;
    }

    # Gzip 压缩
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml;
}
```

- [ ] **Step 2: 在服务器上创建博客目录**

```bash
sudo mkdir -p /var/www/blog
sudo chown -R $USER:$USER /var/www/blog
```

- [ ] **Step 3: 上传 Nginx 配置并重启**

将 `docs/nginx-blog.conf` 内容复制到服务器，替换证书路径，然后:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

- [ ] **Step 4: 提交配置文档**

```bash
git add docs/nginx-blog.conf
git commit -m "docs: 添加 Nginx 配置参考"
```

---

## Task 4: 配置 GitHub Actions 自动部署

**Files:**
- Create: `.github/workflows/deploy.yml`

- [ ] **Step 1: 创建 GitHub Actions 工作流 `.github/workflows/deploy.yml`**

```yaml
name: Deploy Blog

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: true
          recursive: true

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
          cache-dependency-path: hexo/package-lock.json

      - name: Install Dependencies
        run: |
          cd hexo
          npm install

      - name: Build
        run: |
          cd hexo
          hexo clean
          hexo generate

      - name: Deploy to Server
        env:
          HOST: ${{ secrets.DEPLOY_HOST }}
          USER: ${{ secrets.DEPLOY_USER }}
          KEY: ${{ secrets.DEPLOY_KEY }}
          TARGET: ${{ secrets.DEPLOY_TARGET }}
        run: |
          eval "$(ssh-agent -s)"
          echo "$KEY" | tr -d '\r' | ssh-add -
          mkdir -p ~/.ssh
          ssh-keyscan -H $HOST >> ~/.ssh/known_hosts

          rsync -avz --delete \
            -e "ssh -o StrictHostKeyChecking=no" \
            ./hexo/public/ \
            $USER@$HOST:$TARGET

      - name: Cleanup SSH agent
        run: |
          ssh-agent -k
```

- [ ] **Step 2: 在 GitHub 仓库添加 Secrets**

在 GitHub 仓库 Settings → Secrets and variables → Actions 中添加:

| Secret 名称 | 说明 |
|-------------|------|
| DEPLOY_HOST | 服务器 IP 或域名 |
| DEPLOY_USER | SSH 用户名 |
| DEPLOY_KEY | 私钥内容 (-----BEGIN OPENSSH PRIVATE KEY-----) |
| DEPLOY_TARGET | 目标路径，如 /var/www/blog |

- [ ] **Step 3: 提交**

```bash
git add .github/workflows/deploy.yml
git commit -m "feat: 添加 GitHub Actions 部署配置"
git push
```

- [ ] **Step 4: 验证部署**

推送后检查 Actions 日志，确认构建和部署成功。访问 blog.xxx.com 验证。

---

## Task 5: 后续优化 (可选)

**以下任务可根据需要选择性执行**

- [ ] **添加评论系统 (Waline/Gitalk)**
- [ ] **配置文章加密**
- [ ] **添加网站统计**
- [ ] **配置 SEO**

---

## 验证清单

部署完成后验证以下内容:

- [ ] blog.xxx.com 可正常访问
- [ ] HTTPS 证书有效
- [ ] 主页、文章页面正常显示
- [ ] 提交新文章后自动部署成功