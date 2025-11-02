---
title: Redis 连接配置
authors:
  - Chris
date: 2025-03-30
categories:
  - Linux
tags:
  - Linux
---

# Redis 连接配置

<!-- more -->

## 注释 bind，默认只允许本机连接

```bash
#bind 127.0.0.1 -::1
```

## protected-mode 设置为 no

```bash
protected-mode no
```

## 设置 Redis 连接密码

```bash
requirepass 123456
```
