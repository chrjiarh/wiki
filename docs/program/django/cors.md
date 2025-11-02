---
tags:
  - Django
---

# Django 跨域请求

Django 项目中解决 跨域请求（CORS） 问题，推荐使用第三方库 `django-cors-headers`。这是处理 CORS 最常用也最简单的方法。

- 项目地址：[adamchainz/django-cors-headers](https://github.com/adamchainz/django-cors-headers)
- 官方文档：[django-cors-headers](https://pypi.org/project/django-cors-headers/)


## 简介

### 什么是跨域（Cross-Origin）？

“跨域”是指：浏览器在执行前端 JavaScript 时，尝试访问一个与当前页面不同源（域名、端口、协议）的资源或接口。
出于安全考虑，浏览器默认会阻止这种操作，这被称为“同源策略”（Same-Origin Policy）。

### 什么是 CORS？

CORS（Cross-Origin Resource Sharing），即“跨域资源共享”，是 一种标准的 HTTP 机制，允许服务器声明哪些前端源可以访问它的资源。


## 安装 `django-cors-headers`

```bash
pip install django-cors-headers
```

## 配置 `settiong.py` 文件

### 添加到 INSTALLED_APPS

```python
INSTALLED_APPS = [
    ...
    'corsheaders',
    ...
]
```

### 添加中间件到 MIDDLEWARE

注意顺序：

```python
MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',  # 必须放在最前或靠前
    ...
]
```

### 设置允许的跨域来源（白名单）

```python
# 允许所有域名跨域（开发时可用，生产慎用）
CORS_ALLOW_ALL_ORIGINS = True

# 更安全的方式：仅允许特定前端地址跨域
CORS_ALLOWED_ORIGINS = [
    "http://localhost:3000",
    "https://your-frontend-domain.com",
]

# 使用正则表达式匹配允许访问的 域名/IP
CORS_ALLOWED_ORIGIN_REGEXES = [
    r"^https://\w+\.example\.com$",   # 允许所有 *.example.com 子域
    r"^http://localhost:\d+$",        # 允许任意端口的 localhost
]
```

> `CORS_ORIGIN_WHITELIST` 是 `django-cors-headers` 中 早期版本（如 3.x）用来设置允许跨域请求来源的配置项，但在 较新的版本（4.x 及以后）中已被弃用。


## 可选配置项

### 允许携带 Cookie

```python
CORS_ALLOW_CREDENTIALS = True

# 这里有一个需要注意的点
# chrome升级到80版本之后，cookie的SameSite属性默认值由None变为Lax
# 也就是说允许同站点跨域 不同站点需要修改配置为 None（需要将Secure设置为True）
# 需要前端与后端部署在统一服务器下才可进行跨域cookie设置

# 总结：需要设置 samesite = none、secure = True（代表安全环境 需要 localhost 或 HTTPS）才可跨站点设置cookie
```

### 允许特定的 HTTP 方法

```python
CORS_ALLOW_METHODS = [
    "GET",
    "POST",
    "PUT",
    "PATCH",
    "DELETE",
    "OPTIONS"
]
```


### 允许特定头部

```python
CORS_ALLOW_HEADERS = [
    "authorization",
    "content-type",
    ...
]
```

## 检查调试

配置完成后，重启 Django 开发服务，前端发起跨域请求时浏览器应不会再报如下错误：

```bash
Access to fetch at 'http://localhost:8000/api' from origin 'http://localhost:3000' has been blocked by CORS policy
```

## 参考文章

- [【Django跨域】一篇文章彻底解决Django跨域问题！](https://developer.aliyun.com/article/1095338)
