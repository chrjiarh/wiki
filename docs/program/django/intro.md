---
tags:
  - Django
---

# Django 简介

Django 是一个用于构建 Web 应用程序的高级 Python Web 框架。

Django 提供了一套强大的工具和约定，使得开发者能够快速构建功能齐全且易于维护的网站。

Django 本身基于 MVC 模型，即 Model（模型）+ View（视图）+ Controller（控制器）设计模式，MVC 模式使后续对程序的修改和扩展简化，并且使程序某一部分的重复利用成为可能。

Django 采用了 MVT 的软件设计模式，即模型（Model），视图（View）和模板（Template）。


## Web应用程序处理流程

![alt](./assets/web_processing_sequence.png "Web应用程序处理流程")


## 特点

- **ORM（对象关系映射）**： Django 提供了一个强大的 ORM，允许开发者通过 Python 代码来定义和操作数据库模型，而无需直接使用 SQL。这使得数据库操作更加抽象和易于管理。
- **MVC 架构**： Django 遵循 MVC（模型-视图-控制器）的软件设计模式，但它使用了稍微不同的术语。在 Django 中，模型（Model）表示数据结构，视图（View）负责呈现用户界面，而控制器（Controller）的职责被称为视图（View）。
- **模板引擎**： Django 使用模板引擎来生成 HTML，这使得前端和后端的代码分离更加容易。Django 的模板语言允许开发者在模板中嵌入动态内容。
- **自动化 admin 界面**： Django 自动生成管理后台，使得管理和操作数据库的过程变得非常简单。开发者可以轻松地创建、修改和删除数据库记录，而无需编写自定义的管理界面。
- **表单处理**： Django 提供了强大的表单处理工具，使得用户输入的验证和处理变得更加简单。这对于开发 Web 表单和处理用户提交的数据非常有用。
- **安全性**： Django 内置了一些安全性功能，例如防止常见的 Web 攻击（如 CSRF 攻击），并提供了方便的用户身份验证和授权系统。
- **可扩展性**： Django 的组件是松耦合的，允许开发者使用现有的组件或编写自己的应用程序来扩展框架功能。
- **社区支持**： Django 拥有庞大的社区支持，提供了大量的文档、教程和第三方包，使得学习和使用 Django 变得更加容易。


## MVC 与 MTV模型

### MVC 模型

MVC 模式（Model–view–controller）是软件工程中的一种软件架构模式，把软件系统分为三个基本部分：模型（Model）、视图（View）和控制器（Controller）。

MVC 以一种插件式的、松耦合的方式连接在一起。

- 模型（Model）：编写程序应有的功能，负责业务对象与数据库的映射(ORM)。
- 视图（Viev）：图形界面，负责与用户的交互(页面)。
- 控制器（Controller）：负责转发请求，对请求进行处理。

![alt](./assets/mvc.png "MVC 模型")

### MTV 模型

Django 的 MTV 模式本质上和 MVC 是一样的，也是为了各组件间保持松耦合关系，只是定义上有些许不同，Django 的 MTV 分别是指：

- M 表示模型（Model）：编写程序应有的功能，负责业务对象与数据库的映射(ORM)。
- T 表示模板 (Template)：负责如何把页面(html)展示给用户。
- V 表示视图（View）：负责业务逻辑，并在适当时候调用 Model和 Template。

除了以上三层之外，还需要一个 URL 分发器，它的作用是将一个个 URL 的页面请求分发给不同的 View 处理，View 再调用相应的 Model 和 Template。

![alt](./assets/mvt.png "MVT 模型")


## 相关链接

- Django 官网：[https://www.djangoproject.com/](https://www.djangoproject.com/)
- Django 文档：[https://docs.djangoproject.com/zh-hans/](https://docs.djangoproject.com/zh-hans/)
- 菜鸟教程：[https://www.runoob.com/django/django-tutorial.html](https://www.runoob.com/django/django-tutorial.html)
