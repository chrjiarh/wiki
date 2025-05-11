---
title: Docker 搭建 Django 开发环境
authors:
  - Chris
date: 2025-03-24
categories:
  - Python
tags:
  - Python
  - Django
---

# Docker 搭建 Django 开发环境

<!-- more -->

## 安装 Python

拉取镜像

```bash
docker pull python:3.6
```

启动 MySQL 容器

```bash
docker run -p 8010:8000 \
 --name django1.11 \
 -v django_projects:/app \
 -w /app \
 -id python:3.6
```

进入容器

```bash
docker exec -it django1.11 /bin/bash
```

## 安装 Django 和创建工程

安装 Django

```bash
pip install django==1.11.11
```

创建工程

```bash
django-admin startproject demo
```

运行服务器
```bash
python manage.py runserver 0.0.0.0:8000
```

## Vscode 开发

使用 Vscode 需要安装 Docker 和 Dev Containers 插件。

注意：使用 Vscodo 进行 Debug 时，最新的 Python 插件不支持 Python3.6 的，所以需要安装旧的插件并且关闭插件更新。

- Python v2022.8.1
- Pylance v2022.6.30
- Python Debugger v2023.1.XXX

??? abstract "创建 Debug 启动文件 `launch.json` "
    ```json
    {
        // Use IntelliSense to learn about possible attributes.
        // Hover to view descriptions of existing attributes.
        // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
        "version": "0.2.0",
        "configurations": [
            {
                "name": "Python: Django",
                "type": "python",
                "request": "launch",
                "program": "${workspaceFolder}/demo/manage.py",
                "args": [
                    "runserver",
                    "--noreload",
                    "0.0.0.0:8000"
                ],
                "django": true,
                "justMyCode": true
            }
        ]
    }
    ```

