# Hexo 个人博客设计方案

## 概述

基于 Hexo 框架搭建个人博客，用于个人随笔/生活记录。采用 GitHub Actions 自动化部署到自有云服务器。

## 技术选型

- **框架**: Hexo 7.x
- **主题**: Butterfly (基于主题定制)
- **托管**: 自有云服务器 + Nginx
- **域名**: 子域名 blog.xxx.com
- **CI/CD**: GitHub Actions 自动化部署
- **SSL**: 已有证书，Nginx 配置

## 架构

```
写作环境 (本地 Markdown) → GitHub 仓库 → GitHub Actions → 云服务器 (Nginx)
```

用户通过 blog.xxx.com 访问博客，HTTP 自动跳转 HTTPS。

## 部署流程

1. 本地编写 Markdown 文章
2. 推送到 GitHub 仓库
3. GitHub Actions 自动触发构建
4. 构建产物通过 rsync 部署到云服务器
5. Nginx 托管静态文件

## 主题配置

- **风格**: 简约干净
- **配色**: 默认主题色，可根据喜好调整
- **功能**: 文章分类、标签、归档、搜索（本地搜索）
- **评论**: 可选 Gitalk 或 Waline

## 目录结构

```
ai-blog/
├── hexo/                    # Hexo 项目
│   ├── source/_posts/       # 文章源文件
│   ├── themes/              # 主题
│   ├── _config.yml          # Hexo 配置
│   └── package.json
├── .github/
│   └── workflows/
│       └── deploy.yml       # CI/CD 配置
└── docs/                    # 设计文档
```

## 配置清单

- [ ] 云服务器 SSH 访问配置
- [ ] GitHub Actions Secrets (服务器 SSH 密钥)
- [ ] Nginx 站点配置
- [ ] 域名 DNS 解析
- [ ] Hexo 主题配置
- [ ] 评论系统配置 (可选)

## 实施步骤

1. 初始化 Hexo 项目
2. 安装配置 Butterfly 主题
3. 配置 Nginx
4. 配置 GitHub Actions 部署
5. 测试验证