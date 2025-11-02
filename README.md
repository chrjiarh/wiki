# Wiki - 个人知识库

[![MkDocs](https://img.shields.io/badge/MkDocs-1.6.1-2B2B2B?logo=markdown&logoColor=white)](https://www.mkdocs.org)
[![Material Theme](https://img.shields.io/badge/Material%20Theme-9.6.9-3f51b5?logo=material-design)](https://squidfunk.github.io/mkdocs-material/)

基于 MkDocs + Material 构建的个人文档笔记系统，自动部署到 GitHub Pages。

## ✨ 核心功能

- 📚 使用 Material for MkDocs 主题的现代化文档界面
- 🚀 基于 GitHub Actions 的自动化部署流程
- 🌓 支持深色/浅色主题一键切换
- 📝 Markdown 增强语法支持
- 🔍 全文搜索与标签分类功能

## 🛠️ 本地开发

```bash
# 安装依赖
pip install -r requirements.txt

# 启动本地服务器
mkdocs serve

# 访问 http://localhost:8000 查看效果
```

## ⚙️ 配置文件

- 主配置：[mkdocs.yml](./mkdocs.yml)
- 部署配置：[PublishMySite.yml](.github/workflows/PublishMySite.yml)

## 🔄 部署机制

使用 GitHub Actions，自动部署项目文档。当新的提交推送到 `master` 或 `main` 分支时，静态站点将自动构建并部署。

> 如果几分钟后 GitHub 页面仍未显示，请转到仓库的设置并确保 GitHub 页面的[发布源分支](https://docs.github.com/zh/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site)设置为 `gh-pages` 。

网站网址： `<username>.github.io/<repository>`