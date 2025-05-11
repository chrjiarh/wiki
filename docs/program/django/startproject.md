---
tags:
  - Python
  - Django
---

# Django 工程搭建

## 环境安装

方法一：使用 Conda 创建虚拟环境并安装 Django

```bash
conda create -n django python=3.8
conda activate django
pip install django==3.2.9
```

方法二：使用 Docker 搭建 Django 开发环境，[参考博客](../../blog/posts/python/docker_django.md)。


## 创建工程

### 创建

在 Django 中，项目工程目录可以借助 Django 管理工具 `django-admin` 进行创建。

```bash
django-admin startproject mysite
```

### 工程目录说明

```bash
$ tree
.
├── manage.py             命令行工具文件
└── mysite                项目名称
    ├── __init__.py       标识当前所在的项目目录是一个 Python 包
    ├── settings.py       项目配置文件
    ├── urls.py           Url 路径文件
    ├── asgi.py           ASGI 服务器程序的入口文件
    └── wsgi.py           WSGI 服务器程序的入口文件
```

- manage.py：一个让你用各种方式管理 Django 项目的命令行工具。
- mysite/：一个目录，它是你项目的实际 Python 包。
- mysite/\_\_init__.py：一个空文件，告诉 Python 这个目录应该被认为是一个 Python 包。
- mysite/settings.py：Django 项目的配置文件。
- mysite/urls.py：Django 项目的 URL 声明，就像你网站的“目录”。
- mysite/asgi.py：作为你的项目的运行在 ASGI 兼容的 Web 服务器上的入口。
- mysite/wsgi.py：作为你的项目的运行在 WSGI 兼容的Web服务器上的入口。


### 运行

启动 Django 自带的用于开发的简易服务器，它是一个用纯 Python 写的轻量级的 Web 服务器。

```bash
python manage.py runserver 0.0.0.0:8000
```

服务器运行后，浏览器访问 [https://127.0.0.1:8000/](https://127.0.0.1:8000/)。

> 默认 IP 是 127.0.0.1，默认端口为 8000。0.0.0.0 让其它电脑可连接到开发服务器。

Django 默认工作在调试 Debug 模式下，如果增加、修改、删除文件，服务器会自动重启。


## 创建应用

Django 项目就是基于 Django 框架开发的 Web 应用，它包含了一组配置和多个应用，我们把应用称之为 App。一个 App 就是一个 Python 包，通常一个 App 可以包含模型、视图、模板和 URL 配置文件，可以被应用到多个 Django 项目中，因为它们的本质就是可被重用的 Python 软件包。

### 创建

创建子应用模块目录仍然可以通过命令来操作

```bash
python manage.py startapp myapp
```

### 应用目录说明

```bash
$ tree
.
├── manage.py             命令行工具文件
├── myapp                 应用名称
│   ├── admin.py          Django Admin 应用的配置文件
│   ├── apps.py           应用程序本身的属性配置文件
│   ├── __init__.py       标识当前所在的应用目录是一个 Python 包
│   ├── migrations        数据模型迁移记录目录
│   │   └── __init__.py   标识当前所在的数据模型迁移记录目录是一个 Python 包
│   ├── models.py         用于定义应用中所需要的数据表的配置文件
│   ├── tests.py          用于编写当前应用程序的单元测试的测试文件
│   └── views.py          用来定义视图处理函数的文件
└── mysite                项目名称
    ├── __init__.py       标识当前所在的项目目录是一个 Python 包
    ├── settings.py       项目配置文件
    ├── urls.py           Url 路径文件
    ├── asgi.py           ASGI 服务器程序的入口文件
    └── wsgi.py           WSGI 服务器程序的入口文件
```


### 注册应用

创建出来的子应用目录文件虽然被放到了工程项目目录中，但是 Django 工程并不能立即直接使用该子应用，需要注册安装后才能使用。

注册一个子应用的方法，将子应用的配置信息文件 `apps.py` 中的 `Config` 类添加到工程配置文件 `settings.py` 的 `INSTALLED_APPS` 列表中。

例如，将刚创建的 `myapp` 子应用添加到工程中，可在 `INSTALLED_APPS` 列表中添加 `'users.apps.MyappConfig'`。

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    'myapp.apps.MyappConfig',
]
```

