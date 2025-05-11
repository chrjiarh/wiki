---
title: pip 包管理工具
authors:
  - Chris
date: 2025-03-24
categories:
  - Python
tags:
  - Python
---

# pip 包管理工具

<!-- more -->

pip 是 Python 包管理工具，允许用户从 Python 包索引（PyPI）下载和安装软件包。它的全称是 “Pip Installs Packages”。

## 换源

- 清华大学： [https://pypi.tuna.tsinghua.edu.cn/simple/](https://pypi.tuna.tsinghua.edu.cn/simple/)
- 阿里云： [http://mirrors.aliyun.com/pypi/simple/](http://mirrors.aliyun.com/pypi/simple/)
- 中国科技大学： [https://pypi.mirrors.ustc.edu.cn/simple/](https://pypi.mirrors.ustc.edu.cn/simple/)
- 豆瓣： [http://pypi.douban.com/simple/](http://pypi.douban.com/simple/)
- 中国科学技术大学： [http://pypi.mirrors.ustc.edu.cn/simple/](http://pypi.mirrors.ustc.edu.cn/simple/)

**临时使用：**

```bash
pip install <package_name> -i https://pypi.tuna.tsinghua.edu.cn/simple/
```

**永久修改：**

修改 ~/.pip/pip.conf 文件，配置 pip 清华源。

```bash
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host=pypi.tuna.tsinghua.edu.cn
```

查看 pip 配置信息

```bash
pip config list
```

## 参数解释

```bash
pip --help
```

## 包管理

### 列出已安装的包

```bash
pip list
```

### 查看包信息

```bash
pip show <package_name>
```

### 安装

```bash
pip install <package_name>

# 安装指定包版本
pip install django==1.11.11
```

### 卸载

```bash
pip uninstall <package_name>
```

### 检查哪些包需要更新

```bash
pip list --outdated
```

### 升级

```bash
pip install --upgrade <package_name>
```

## 升级 pip 本身

查看当前 pip 版本

```bash
pip show pip
```

升级

```bash
python -m pip install --upgrade pip
```

## 使用 requirements.txt 文件

在大型项目中，管理依赖项可能会变得复杂。为了简化这一过程，可以使用 requirements.txt 文件。该文件列出了项目所需的所有包及其版本。

### 创建

可以手动创建一个 requirements.txt 文件，也可以使用以下命令自动生成：

```bash
pip freeze > requirements.txt
```

### 安装 requirements.txt 中的包

```bash
pip install -r requirements.txt
```


## 缓存

查看缓存信息

```bash
pip cache info
```

还有指令可以查看 cache 列表和路径

```bash
pip cache list
pip cache dir
```

清除缓存

```bash
pip cache purge
```
