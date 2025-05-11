---
title: venv 虚拟环境管理工具
authors:
  - Chris
date: 2025-04-09
categories:
  - Python
tags:
  - Python
---

# venv 虚拟环境管理工具

<!-- more -->

venv 是 Python 自带的虚拟环境管理工具。

> 注：venv 工具没法创建不同版本的 Python 环境。

## 安装 venv

Python 3.6 及以上已经默认安装，Python 3.5 需要通过系统的包管理工具安装，例如在 Ubuntu上，可以这么安装:

```bash
sudo apt install python3-venv
```


## 创建虚拟环境

假设我们要在当前目录的 test_env 目录下创建虚拟环境，那么执行下面的命令就可以了：

```bash
python3 -m venv test_env
```

## 启用虚拟环境

在 Linux 和 Mac 环境下，打开终端，执行下面的命令：

```bash
source ./test_env/bin/activate
```

## 安装包

虚拟环境启用后，就可以使用 `pip` 命令来安装需要的包：

```bash
pip install <package_name>
```

## 退出虚拟环境

执行命令：

```bash
deactivate
```

