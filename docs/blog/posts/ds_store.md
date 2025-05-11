---
title: Git 忽略 .DS_Store 文件
authors:
  - Chris
date: 2025-03-24
categories:
  - Mac
tags:
  - Mac
---

# Mac 系统中 Git 忽略 .DS_Store 文件

<!-- more -->

.DS_Store (英文全称 Desktop Services Store) 是一种由苹果公司的 Mac OS X 操作系统所创造的隐藏文件，目的在于存贮目录的自定义属性，例如文件的图标位置或者是背景色的选择，存在于每一个用「访达」打开过的文件夹下面。

方法一：在 Git 目录中添加 `.gitignore` 文件配置，再输入 `*/.DS_Store` 。

方法二：全局配置，不管在哪个目录都可以忽略。


```
touch ~/.gitignore_global

echo "**/.DS_Store" >> ~/.gitignore_global

git config --global core.excludesfile ~/.gitignore_global
```