---
title: Github 进行 SSH 连接出现 kex_exchange_identification 错误
authors:
  - Chris
date: 2025-05-11
categories:
  - Linux
tags:
  - Linux
---

# Github 通过 SSH 连接出现 kex_exchange_identification 错误

<!-- more -->


## 问题描述

使用 SSH 连接 Github 时，出现 `Connection closed by 127.0.0.1 port 7890` 错误。

可以使用命令 `ssh -vvv git@github.com` 以详细调试模式尝试通过 SSH 连接到 GitHub：

```bash
$ ssh -vvv git@github.com
...
debug1: Connecting to github.com port 22.
...
kex_exchange_identification: Connection closed by remote host
Connection closed by 127.0.0.1 port 7890
```

## 原因

默认的 SSH 端口（22）被阻断了，或者被系统代理（比如 Clash）劫持了，基本上是代理的问题。

## 解决方法

1. 不使用代理（不推荐：因为不用代理 Github 访问会不稳定）
2. 通过 443 端口连接 SSH：[官方文档](https://docs.github.com/zh/authentication/troubleshooting-ssh/using-ssh-over-the-https-port)

**具体方法：修改 `~/.ssh/config`**

```bash
vim ~/.ssh/config
```

加入以下内容

```bash
Host github.com
  HostName ssh.github.com
  User git
  Port 443
```

验证

```bash
$ ssh -T git@github.com
```