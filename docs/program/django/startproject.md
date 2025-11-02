---
tags:
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


## 创建视图

在 Django 中，视图是处理请求并返回响应的函数。视图函数接收一个 HttpRequest 对象作为参数，并返回一个 HttpResponse 对象。视图函数通常定义在应用的 `views.py` 文件中。

### 创建视图函数

在 `myapp/views.py` 文件中，添加以下代码：

```python
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world. You're at the myapp index.")
```

### 配置 URL

在 `myapp` 目录下创建一个名为 `urls.py` 的文件，并添加以下代码：

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

### 配置项目 URL

在 `mysite/urls.py` 文件中，添加以下代码：

```python
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('admin/', admin.site.urls),
    path('myapp/', include('myapp.urls')),
]
```

### 运行服务器

启动 Django 开发服务器：

```bash
python manage.py runserver 0.0.0.0:8000
```

访问 [https://127.0.0.1:8000/myapp/](https://127.0.0.1:8000/myapp/)，能看到 "Hello, world. You're at the myapp index." 的响应。

## settings.py 常用参数

配置官方文档：[https://docs.djangoproject.com/en/stable/ref/settings/](https://docs.djangoproject.com/en/stable/ref/settings/)

```python
# Django settings.py 配置文件参数详解笔记

# 建议在实际项目中，将敏感信息（如 SECRET_KEY, 数据库密码等）通过环境变量加载，
# 而不是直接硬编码在 settings.py 文件中。例如:
# import os
# SECRET_KEY = os.environ.get('DJANGO_SECRET_KEY', 'your-default-secret-key-for-dev')

# --- 基础路径定义 ---
from pathlib import Path

# BASE_DIR 指向项目根目录 (通常是包含 manage.py 文件的上一级目录)
# 例如: project_root/
#             ├── manage.py
#             ├── project_name/  <- 如果 settings.py 在 project_name/project_name/ 下, BASE_DIR 指向这里
#             │   ├── settings.py
#             │   └── ...
#             └── app_name/
# 如果 settings.py 在 project_root/project_name/settings.py, 那么 BASE_DIR 是:
# BASE_DIR = Path(__file__).resolve().parent.parent
# 如果 settings.py 在 project_root/config/settings.py, 那么 BASE_DIR 可能是:
# BASE_DIR = Path(__file__).resolve().parent.parent
# 请根据你的项目结构调整 .parent 的数量
BASE_DIR = Path(__file__).resolve().parent.parent


# --- 核心设置 ---

# 安全密钥：用于加密签名，如会话数据、CSRF令牌等。
# 生产环境中必须保密且唯一，不要使用默认生成的或简单的值。
# Django 启动项目时会自动生成一个。
SECRET_KEY = 'django-insecure-your-unique-and-secret-key'  # 生产环境请务必替换为一个复杂且保密的密钥

# 调试模式：
# True: 开发模式。显示详细的错误页面，自动重载代码。
# False: 生产模式。不显示详细错误信息，提高安全性。
# 生产环境必须设为 False！
DEBUG = True  # 开发时设为 True，生产环境必须改为 False

# 允许的主机/域名：
# DEBUG = True 时，默认为 ['localhost', '127.0.0.1', '[::1]']
# DEBUG = False (生产环境) 时，必须明确指定允许访问本站的域名或IP。
# ['yourdomain.com', 'www.yourdomain.com']
# '*' 表示允许所有主机，但存在安全风险，不建议在生产环境中使用。
ALLOWED_HOSTS = [] # 例如: ['www.example.com', 'api.example.com']


# --- 应用定义 ---

# 项目中安装的应用列表。Django自带应用和自定义应用都需要在此注册。
# 应用的顺序有时很重要，特别是涉及模板覆盖或依赖关系时。
INSTALLED_APPS = [
    'django.contrib.admin',         # Django 管理后台
    'django.contrib.auth',          # Django 认证系统
    'django.contrib.contenttypes',  # 内容类型框架
    'django.contrib.sessions',      # Session 会话框架
    'django.contrib.messages',      # 消息框架
    'django.contrib.staticfiles',   # 静态文件管理
    # 'django.contrib.sites',       # 如果使用 Django Sites 框架
    # 'allauth',                    # 第三方认证库示例
    # 'allauth.account',
    # 'allauth.socialaccount',
    # 'rest_framework',             # Django REST framework 示例
    'your_app_name.apps.YourAppConfig', # 或直接 'your_app_name' (推荐前者)
    # ... 其他自定义应用
]

# 中间件：处理请求和响应的钩子框架。
# 顺序非常重要，请求时自上而下处理，响应时自下而上处理。
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',      # 安全相关的中间件
    'django.contrib.sessions.middleware.SessionMiddleware', # Session 支持
    'django.middleware.common.CommonMiddleware',          # 常用设置，如处理URL末尾斜杠
    'django.middleware.csrf.CsrfViewMiddleware',          # CSRF 保护
    'django.contrib.auth.middleware.AuthenticationMiddleware', # 用户认证
    'django.contrib.messages.middleware.MessageMiddleware',    # 消息提示
    'django.middleware.clickjacking.XFrameOptionsMiddleware',# 防止点击劫持
    # 'whitenoise.middleware.WhiteNoiseMiddleware', # 如果使用 WhiteNoise 服务静态文件 (生产)
    # ... 其他自定义或第三方中间件
]

# 根URL配置：指向项目的主 urls.py 文件。
ROOT_URLCONF = 'your_project_name.urls' # 替换 'your_project_name' 为你的项目名

# --- 模板配置 ---
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates', # 默认的Django模板引擎
        'DIRS': [
            BASE_DIR / 'templates', # 项目级模板目录
            # os.path.join(BASE_DIR, 'templates') # 另一种写法
        ],
        'APP_DIRS': True, # 允许Django在每个已安装应用的 'templates' 子目录中查找模板
        'OPTIONS': {
            'context_processors': [ # 上下文处理器，为模板提供全局变量
                'django.template.context_processors.debug',    # DEBUG 变量等
                'django.template.context_processors.request',  # request 对象
                'django.contrib.auth.context_processors.auth',     # user 对象，perms 对象
                'django.contrib.messages.context_processors.messages', # messages
                # 'django.template.context_processors.i18n', # 如果使用国际化
            ],
            # 'builtins': [], # 自定义模板标签/过滤器
            # 'loaders': [], # 自定义模板加载器
        },
    },
]

# --- WSGI/ASGI 应用 ---

# WSGI (Web Server Gateway Interface) 应用入口点，用于传统的同步Web服务器。
WSGI_APPLICATION = 'your_project_name.wsgi.application' # 替换 'your_project_name'

# ASGI (Asynchronous Server Gateway Interface) 应用入口点，用于异步Web服务器 (如 Daphne, Uvicorn)。
# 如果你使用 Django Channels 或其他 ASGI 功能，会需要配置这个。
# ASGI_APPLICATION = 'your_project_name.asgi.application' # 替换 'your_project_name'


# --- 数据库配置 ---
# https://docs.djangoproject.com/en/stable/ref/settings/#databases

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3', # 数据库引擎 (sqlite3, postgresql, mysql, oracle)
        'NAME': BASE_DIR / 'db.sqlite3',        # 数据库文件名 (对于 SQLite)
        # 对于其他数据库 (如 PostgreSQL, MySQL):
        # 'NAME': 'your_db_name',
        # 'USER': 'your_db_user',
        # 'PASSWORD': 'your_db_password',
        # 'HOST': 'localhost', # 或数据库服务器IP/域名
        # 'PORT': '5432',      # PostgreSQL 默认 5432, MySQL 默认 3306
        # 'OPTIONS': {
        #     'init_command': "SET sql_mode='STRICT_TRANS_TABLES'", # MySQL 示例
        # },
        # 'ATOMIC_REQUESTS': True, # 设为True，则每个HTTP请求都在一个事务中处理 (默认 False)
    }
    # 可以在这里配置多个数据库连接
    # 'another_db': {
    #     'ENGINE': 'django.db.backends.postgresql',
    #     'NAME': 'other_db_name',
    #     'USER': 'other_db_user',
    #     'PASSWORD': 'other_db_password',
    #     'HOST': 'localhost',
    #     'PORT': '5432',
    # }
}
# 如果使用多个数据库, 还需要配置 DATABASE_ROUTERS
# DATABASE_ROUTERS = ['path.to.your.RouterClass'] # 示例
# 生产环境建议使用 PostgreSQL 或 MySQL 等更健壮的数据库。


# --- 密码验证 ---
# https://docs.djangoproject.com/en/stable/ref/settings/#auth-password-validators

AUTH_PASSWORD_VALIDATORS = [
    {
        'NAME': 'django.contrib.auth.password_validation.UserAttributeSimilarityValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator',
        # 'OPTIONS': {'min_length': 9} # 可选：自定义最小长度
    },
    {
        'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator',
    },
]


# --- 国际化 (i18n) 与本地化 (l10n) ---
# https://docs.djangoproject.com/en/stable/topics/i18n/

# 默认语言代码。例如 "en-us" (美式英语), "zh-hans" (简体中文)。
LANGUAGE_CODE = 'zh-hans' # 或者 'en-us'

# 默认时区。例如 "UTC", "Asia/Shanghai"。
# Windows 用户可能需要使用 pytz 库支持所有时区名称。
TIME_ZONE = 'Asia/Shanghai' # 或者 'UTC'

# 是否启用Django的翻译系统。
USE_I18N = True

# 是否根据当前区域设置来格式化日期、数字等。
# USE_L10N 在 Django 5.0+ 中被废弃，使用 USE_FORMATTING 代替
# USE_L10N = True (Django < 5.0)
USE_FORMATTING = True # (Django >= 5.0)

# 是否使用时区感知的时间对象。
# 当为 True 时，Django 会在内部存储 UTC 时间，并在模板和表单中转换为 TIME_ZONE。
USE_TZ = True

# （可选）指定语言文件 (.po, .mo) 的搜索路径
# LOCALE_PATHS = [
#     BASE_DIR / 'locale',
# ]
# （可选）可用语言列表，如果定义了，则 Django 只会为这些语言提供翻译。
# from django.utils.translation import gettext_lazy as _
# LANGUAGES = [
#   ('de', _('German')),
#   ('en', _('English')),
#   ('zh-hans', _('Simplified Chinese')),
# ]


# --- 静态文件 (CSS, JavaScript, Images) ---
# https://docs.djangoproject.com/en/stable/howto/static-files/

# 静态文件在浏览器中访问的URL前缀。
# 例如: http://example.com/static/css/style.css
STATIC_URL = '/static/' # 必须以 / 结尾

# （可选）开发时，Django 在这些目录中查找静态文件 (除了每个 app 下的 'static/' 目录)。
# 例如: [BASE_DIR / 'project_static_files']
STATICFILES_DIRS = [
    BASE_DIR / 'static', # 项目级静态文件目录
]

# （生产环境）运行 `collectstatic` 命令时，所有静态文件会被收集到这个目录。
# 这个目录应该由你的Web服务器 (如 Nginx) 直接服务。
# 通常设置为项目根目录下的一个文件夹，例如 'staticfiles' 或 'collected_static'。
# 不要与 STATICFILES_DIRS 中的路径相同。
STATIC_ROOT = BASE_DIR / 'staticfiles_collected' # 示例路径，确保Web服务器配置指向这里

# （可选）指定静态文件查找器，默认已包含常用查找器。
# STATICFILES_FINDERS = [
#     'django.contrib.staticfiles.finders.FileSystemFinder',
#     'django.contrib.staticfiles.finders.AppDirectoriesFinder',
# ]

# （可选, 生产环境配合 WhiteNoise 等库）静态文件存储引擎。
# STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'


# --- 媒体文件 (用户上传的文件) ---
# https://docs.djangoproject.com/en/stable/topics/files/

# 用户上传文件在浏览器中访问的URL前缀。
# 例如: http://example.com/media/uploads/image.jpg
MEDIA_URL = '/media/' # 必须以 / 结尾

# 用户上传文件在服务器上存储的物理路径。
# 确保运行Django应用的用户对此目录有写权限。
MEDIA_ROOT = BASE_DIR / 'media' # 项目级媒体文件存储目录

# 注意：在开发环境中，你需要配置URLconf来服务MEDIA_URL。
# 在 urls.py 中添加:
# from django.conf import settings
# from django.conf.urls.static import static
# if settings.DEBUG:
#     urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)


# --- 默认主键字段类型 ---
# https://docs.djangoproject.com/en/stable/ref/settings/#default-auto-field

# Django 3.2+ 版本默认为 BigAutoField，之前的版本默认为 AutoField。
# BigAutoField 使用64位整数，AutoField 使用32位整数。
DEFAULT_AUTO_FIELD = 'django.db.models.BigAutoField'


# --- 用户认证相关 ---

# （可选）如果使用自定义用户模型，需要指定。
# AUTH_USER_MODEL = 'your_app_name.CustomUser' # 例如 'accounts.User'

# （可选）未登录用户访问需要登录的页面时，跳转到的登录URL。
LOGIN_URL = '/accounts/login/' # 通常是你的登录页面的URL名称或路径
# LOGIN_URL = 'login' # 也可以是 URL 名称

# （可选）用户成功登录后跳转到的URL。
LOGIN_REDIRECT_URL = '/' # 首页或用户仪表盘
# LOGIN_REDIRECT_URL = 'home' # 也可以是 URL 名称

# （可选）用户成功登出后跳转到的URL。
LOGOUT_REDIRECT_URL = '/' # 首页或登录页
# LOGOUT_REDIRECT_URL = 'login' # 也可以是 URL 名称


# --- Email 配置 ---
# https://docs.djangoproject.com/en/stable/topics/email/

# 开发时，可以将邮件输出到控制台：
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'

# 生产环境SMTP配置示例 (以Gmail为例，但建议使用专用邮件服务如 SendGrid, Mailgun):
# EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
# EMAIL_HOST = 'smtp.gmail.com'
# EMAIL_PORT = 587 # TLS
# EMAIL_USE_TLS = True
# EMAIL_USE_SSL = False # 如果端口是465，则设为True，USE_TLS设为False
# EMAIL_HOST_USER = 'your_email@gmail.com' # 你的邮箱地址
# EMAIL_HOST_PASSWORD = 'your_app_password'  # 你的邮箱应用专用密码 (不是登录密码)
# DEFAULT_FROM_EMAIL = EMAIL_HOST_USER # 默认发件人

# （可选）当 DEBUG=False 时，发生服务器错误时接收邮件的管理员列表。
# ADMINS = [('Your Name', 'your_email@example.com')]
# MANAGERS = ADMINS # 通常与ADMINS相同，用于接收特定类型的通知，如损坏链接


# --- Session 设置 ---
# https://docs.djangoproject.com/en/stable/ref/settings/#sessions

# SESSION_ENGINE = 'django.contrib.sessions.backends.db' # 默认，存储在数据库
# SESSION_ENGINE = 'django.contrib.sessions.backends.cache' # 存储在缓存
# SESSION_ENGINE = 'django.contrib.sessions.backends.cached_db' # 优先缓存，再数据库
# SESSION_ENGINE = 'django.contrib.sessions.backends.file_based' # 存储在文件系统
# SESSION_ENGINE = 'django.contrib.sessions.backends.signed_cookies' # 存储在加密cookie中 (有大小限制)

# SESSION_COOKIE_AGE = 1209600  # Session Cookie 的有效期，单位秒 (默认2周)
# SESSION_COOKIE_SECURE = False # 开发时False, 生产环境(HTTPS)应设为 True
# SESSION_COOKIE_HTTPONLY = True # 防止客户端JavaScript访问Session Cookie (推荐True)
# SESSION_SAVE_EVERY_REQUEST = False # 默认False, 每次请求都保存session数据会影响性能


# --- CSRF 设置 ---
# https://docs.djangoproject.com/en/stable/ref/settings/#csrf

# CSRF_COOKIE_SECURE = False # 开发时False, 生产环境(HTTPS)应设为 True
# CSRF_COOKIE_HTTPONLY = False # 默认False, 如果设为True，JS将无法访问CSRF token (通常不需要)
# CSRF_TRUSTED_ORIGINS = ['https://*.example.com'] # 对于HTTPS子域名，需要添加到信任来源


# --- 日志配置 (Logging) ---
# https://docs.djangoproject.com/en/stable/topics/logging/
# Django 的日志配置非常灵活，但也很复杂。以下是一个基础示例：
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False, # 是否禁用已存在的日志器
    'formatters': { # 日志格式
        'verbose': {
            'format': '{levelname} {asctime} {module} {process:d} {thread:d} {message}',
            'style': '{',
        },
        'simple': {
            'format': '{levelname} {message}',
            'style': '{',
        },
    },
    'handlers': { # 日志处理器
        'console': { # 输出到控制台
            'level': 'DEBUG', # 日志级别
            'class': 'logging.StreamHandler',
            'formatter': 'simple',
        },
        'file': { # 输出到文件
            'level': 'INFO',
            'class': 'logging.handlers.RotatingFileHandler',
            'filename': BASE_DIR / 'logs/django.log', # 日志文件路径
            'maxBytes': 1024 * 1024 * 5,  # 5 MB
            'backupCount': 5, # 保留5个备份文件
            'formatter': 'verbose',
        },
    },
    'loggers': { # 日志器
        'django': { # Django框架的日志
            'handlers': ['console', 'file'],
            'level': 'INFO', # Django框架日志级别
            'propagate': True,
        },
        'your_app_name': { # 自定义应用的日志
            'handlers': ['console', 'file'],
            'level': 'DEBUG', # 自定义应用日志级别
            'propagate': False,
        },
    },
}
# 注意: 使用文件日志前，请确保 `logs` 目录存在且有写入权限。


# --- 安全设置 (生产环境特别重要) ---
# https://docs.djangoproject.com/en/stable/ref/middleware/#module-django.middleware.security

# 以下设置通常在生产环境中（当 DEBUG=False 时）启用，并且网站使用 HTTPS

# SECURE_SSL_REDIRECT = True # 将所有HTTP请求重定向到HTTPS (需要Web服务器配合或Nginx/Apache处理)
# SECURE_HSTS_SECONDS = 31536000  # 1年. 告诉浏览器后续只通过HTTPS访问 (需谨慎开启)
# SECURE_HSTS_INCLUDE_SUBDOMAINS = True # HSTS 应用于所有子域名
# SECURE_HSTS_PRELOAD = True # 允许将域名提交到浏览器HSTS预加载列表

# SESSION_COOKIE_SECURE = True # 仅通过HTTPS发送Session Cookie (已在 Session 设置中提及)
# CSRF_COOKIE_SECURE = True    # 仅通过HTTPS发送CSRF Cookie (已在 CSRF 设置中提及)

# SECURE_BROWSER_XSS_FILTER = True # 启用浏览器内置的XSS过滤器 (现代浏览器可能已默认)
# SECURE_CONTENT_TYPE_NOSNIFF = True # 防止浏览器猜测MIME类型

# X_FRAME_OPTIONS = 'DENY' # 或 'SAMEORIGIN'. 防止点击劫持 (clickjacking)

# （可选）如果你的 Django 应用部署在反向代理 (如 Nginx) 后面，并且代理处理了 SSL/TLS，
# 那么需要告诉 Django 它是安全的。
# SECURE_PROXY_SSL_HEADER = ('HTTP_X_FORWARDED_PROTO', 'https')


# --- 缓存配置 (CACHES) ---
# https://docs.djangoproject.com/en/stable/topics/cache/
CACHES = {
    'default': { # 默认缓存配置
        'BACKEND': 'django.core.cache.backends.dummy.DummyCache', # 开发时使用，不做任何实际缓存
    }
    # 示例：内存缓存 (LocMemCache) - 单进程开发/测试环境适用，不推荐生产
    # 'default': {
    #     'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',
    #     'LOCATION': 'unique-snowflake', # 任意唯一字符串，用于区分多个 LocMemCache 实例
    #     # 'TIMEOUT': 300, # 默认缓存时间 (秒)，None 表示永不过期
    #     # 'OPTIONS': {
    #     #     'MAX_ENTRIES': 1000 # 最大缓存条目数
    #     # }
    # }

    # 示例：文件系统缓存
    # 'default': {
    #     'BACKEND': 'django.core.cache.backends.filebased.FileBasedCache',
    #     'LOCATION': BASE_DIR / 'django_cache', # 缓存文件存放目录，确保此目录存在且可写
    #     # 'TIMEOUT': 300,
    #     # 'OPTIONS': {
    #     #     'MAX_ENTRIES': 1000
    #     # }
    # }

    # 示例：数据库缓存 (需要先运行 `python manage.py createcachetable`)
    # 'default': {
    #     'BACKEND': 'django.core.cache.backends.db.DatabaseCache',
    #     'LOCATION': 'my_cache_table', # 数据库中用于存储缓存的表名
    #     # 'TIMEOUT': 300,
    # }

    # 示例：Memcached 缓存 (需要安装 python-memcached 或 pylibmc 库)
    # 'default': {
    #     'BACKEND': 'django.core.cache.backends.memcached.PyMemcacheCache', # 或者 'django.core.cache.backends.memcached.MemcachedCache' for python-memcached
    #     'LOCATION': '127.0.0.1:11211', # 单个 Memcached 服务器
    #     # 'LOCATION': [ # 多个 Memcached 服务器
    #     #     '172.19.26.240:11211',
    #     #     '172.19.26.242:11211',
    #     # ]
    #     # 'TIMEOUT': 300,
    #     # 'KEY_PREFIX': 'myproject_cache_prefix', # 可选，缓存键前缀
    # }

    # 示例：Redis 缓存 (需要安装 django-redis 库: pip install django-redis)
    # 'default': {
    #     "BACKEND": "django_redis.cache.RedisCache",
    #     "LOCATION": "redis://127.0.0.1:6379/1", # Redis 服务器地址, /1 表示使用第1个数据库
    #     "OPTIONS": {
    #         "CLIENT_CLASS": "django_redis.client.DefaultClient",
    #         # "PASSWORD": "your_redis_password", # 如果 Redis 设置了密码
    #         # "CONNECTION_POOL_KWARGS": {"max_connections": 100}
    #     },
    #     # 'TIMEOUT': 300,
    #     # 'KEY_PREFIX': 'myproject_cache_prefix',
    # }
}
# （可选）用于 Session 的特定缓存配置 (如果 SESSION_ENGINE 设置为 'django.contrib.sessions.backends.cache')
# SESSION_CACHE_ALIAS = 'default' # 使用 'default' 缓存配置存储 session


# --- 测试配置 ---
# https://docs.djangoproject.com/en/stable/topics/testing/overview/
# （可选）指定测试运行器
# TEST_RUNNER = 'django.test.runner.DiscoverRunner' # 默认
# TEST_RUNNER = 'pytest_django.plugin.PytestDjangoRunner' # 如果使用 pytest-django

# （可选）为测试数据库指定特定设置
# DATABASES['default']['TEST'] = {
#     'NAME': 'test_myproject_db', # 测试数据库的名称
#     # 可以覆盖其他数据库设置，如 ENGINE, USER, PASSWORD 等
#     # 'SERIALIZE': False, # 默认True，在多数据库场景下，可以加快测试数据库创建
# }


# --- Sites 框架 ---
# https://docs.djangoproject.com/en/stable/ref/contrib/sites/
# 如果使用了 django.contrib.sites 应用 (例如 allauth, sitemaps 等会用到)
# SITE_ID = 1 # 通常是 1，表示默认站点


# --- Fixture 目录 ---
# https://docs.djangoproject.com/en/stable/howto/initial-data/
# 运行 loaddata 命令时，Django 会在这些目录中查找 fixture 文件 (如 .json, .xml, .yaml)
# FIXTURE_DIRS = [
#     BASE_DIR / 'fixtures',
# ]


# --- 文件上传处理 ---
# https://docs.djangoproject.com/en/stable/ref/settings/#file-upload-settings
# FILE_UPLOAD_MAX_MEMORY_SIZE = 2621440  # 2.5MB. 小于此大小的文件会先在内存中处理
# DATA_UPLOAD_MAX_MEMORY_SIZE = 2621440  # 2.5MB. POST 请求体数据（不含文件）的最大内存大小
# DATA_UPLOAD_MAX_NUMBER_FIELDS = 1000   # POST 请求中允许的最大参数数量
# FILE_UPLOAD_PERMISSIONS = 0o644        # 上传文件的默认权限 (八进制)
# FILE_UPLOAD_DIRECTORY_PERMISSIONS = None # 上传目录的默认权限 (None 表示依赖系统 umask)

# （可选）自定义文件存储后端，例如使用 Amazon S3
# DEFAULT_FILE_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
# STATICFILES_STORAGE = 'storages.backends.s3boto3.S3StaticStorage' # 如果静态文件也用S3
# AWS_ACCESS_KEY_ID = 'YOUR_AWS_ACCESS_KEY_ID' # 强烈建议使用环境变量
# AWS_SECRET_ACCESS_KEY = 'YOUR_AWS_SECRET_ACCESS_KEY' # 强烈建议使用环境变量
# AWS_STORAGE_BUCKET_NAME = 'your-s3-bucket-name'
# AWS_S3_REGION_NAME = 'your-s3-bucket-region' # e.g., 'us-east-1'
# AWS_S3_CUSTOM_DOMAIN = f'{AWS_STORAGE_BUCKET_NAME}.s3.amazonaws.com' # 或你的CDN域名
# AWS_S3_OBJECT_PARAMETERS = { # 可选，S3对象参数
#     'CacheControl': 'max-age=86400',
# }
# AWS_LOCATION = 'static' # 可选，S3 bucket 内的子目录 (配合S3StaticStorage)
# AWS_DEFAULT_ACL = None # 或 'public-read' 等


# --- Admin 站点自定义 ---
# (需要在 urls.py 中设置 admin.site.site_header 等，或创建自定义 AdminSite)
# 这些设置不会直接生效，通常在自定义 AdminSite 的子类中或在 urls.py 的 admin.autodiscover() 后设置
# from django.contrib import admin
# admin.site.site_header = "我的项目管理后台"
# admin.site.site_title = "项目管理"
# admin.site.index_title = "欢迎使用管理后台"

# --- 其他第三方库配置 ---
# 例如 Django REST framework:
# REST_FRAMEWORK = {
#     'DEFAULT_AUTHENTICATION_CLASSES': [
#         'rest_framework.authentication.SessionAuthentication',
#         'rest_framework.authentication.TokenAuthentication',
#     ],
#     'DEFAULT_PERMISSION_CLASSES': [
#         'rest_framework.permissions.IsAuthenticatedOrReadOnly',
#     ],
#     'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
#     'PAGE_SIZE': 10
# }

# 例如 Celery (异步任务队列):
# CELERY_BROKER_URL = 'redis://localhost:6379/0' # Broker URL (例如 Redis, RabbitMQ)
# CELERY_RESULT_BACKEND = 'redis://localhost:6379/0' # 结果存储后端
# CELERY_ACCEPT_CONTENT = ['json']
# CELERY_TASK_SERIALIZER = 'json'
# CELERY_RESULT_SERIALIZER = 'json'
# CELERY_TIMEZONE = TIME_ZONE # 使用 Django 的时区
```