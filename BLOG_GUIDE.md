# 博客使用指南

## 博客信息
- 📁 本地路径：`C:\Users\k1_adm\.openclaw\workspace\blog`
- 🌐 访问地址：https://lesliechueng4-ctrl.github.io/
- 💾 GitHub 仓库：https://github.com/lesliechueng4-ctrl/lesliechueng4-ctrl.github.io

## 发布新文章

只需告诉我内容，格式如下：

```
帮我发布文章：
标题：XXX
标签：标签1, 标签2
分类：分类名称
内容：
（你的文章内容）
```

我会自动：
1. 创建文章文件
2. 本地构建
3. 推送到 GitHub
4. 博客自动更新

## 本地预览

```powershell
cd C:\Users\k1_adm\.openclaw\workspace\blog
hugo server -D
```

访问 http://localhost:1313

## 手动发布（如果需要）

```powershell
cd C:\Users\k1_adm\.openclaw\workspace\blog
hugo
git add .
git commit -m "Update blog"
git subtree push --prefix public origin gh-pages
```

## 文章格式

文章文件位于 `content/posts/`，格式：

```yaml
---
title: "文章标题"
date: 2026-02-14T12:00:00+08:00
draft: false
tags: ["标签1", "标签2"]
categories: ["分类"]
---

文章正文内容...
```

## 配置文件

- 主配置：`hugo.yaml`
- 主题：`themes/PaperMod`（Git 子模块）
