---
title: Linux 云服务器图形化桌面配置和连接
authors:
  - Chris
date: 2025-06-30
categories:
  - Linux
tags:
  - Linux
---

# Linux 云服务器图形化桌面配置和连接

<!-- more -->


## **整体思路**


1.  **服务器准备**: 通过 SSH 登录你的 Ubuntu 云服务器。
2.  **创建新用户**: 创建一个名为 `ginger` 的专属用户并授予管理员权限。
3.  **安装图形化桌面 (XFCE)**: 我们选择 XFCE，因为它轻量、快速，非常适合云服务器。
4.  **安装远程桌面服务 (xrdp)**: 让你的 Mac 可以通过 RDP 协议连接到服务器的图形化桌面。
5.  **配置防火墙**: 打开远程桌面所需的端口。
6.  **安装 Google Chrome**: 在服务器上安装 Chrome 浏览器。
7.  **Mac 连接**: 在你的 MacBook Pro 上安装并配置 Microsoft Remote Desktop 客户端。

> Microsoft Remote Desktop（RDP 客户端）不仅可以连接 Windows，也可以连接 Linux，只要 Linux 安装了 RDP 服务端（比如 xrdp）。  
> Microsoft Remote Desktop 新版改为 Windows App。

---

## 第一步：云服务器准备

本教程用的是 `Ubuntu 22.04` 系统，`x86` 架构

---

## 第二步：初始登录与创建 `ginger` 用户

这一步是唯一需要使用云服务商提供的初始用户（如 `root` 或 `ubuntu`）来操作的步骤，目的是创建我们之后要用的专属账户。

1.  **SSH 登录服务器**:
    打开您 Mac 上的“终端” (Terminal) 应用，使用云服务商给您的 IP 和初始账户登录。
    ```bash
    # 如果是 root 用户
    ssh root@你的服务器IP地址

    # 如果是 ubuntu 用户
    ssh ubuntu@你的服务器IP地址
    ```

2.  **更新系统**:
    登录后，先运行一次系统更新，确保所有软件都是最新的。
    ```bash
    sudo apt update && sudo apt upgrade -y
    ```

3.  **创建 `ginger` 用户**:
    ```bash
    sudo adduser ginger
    ```
    您需要为 `ginger` 设置一个新密码，请务必设置一个强密码并牢牢记住。后续的个人信息可以直接按回车键跳过。

4.  **授予 `ginger` 用户管理员 (sudo) 权限**:
    这样 `ginger` 用户就能在需要时执行管理员命令。
    ```bash
    sudo usermod -aG sudo ginger
    ```

5.  **切换到 `ginger` 用户**
    现在，我们将完全切换到新创建的 `ginger` 用户，并在此身份下完成所有剩余的配置。

    ```bash
    su - ginger
    ```
    输入您刚才为 `ginger` 设置的密码。成功后，您的命令行提示符会变为 `ginger@...`。

**✅ 从现在开始，您将一直作为 `ginger` 用户操作。对于需要系统权限的命令，我们将在命令前加上 `sudo`。当提示输入密码时，请输入 `ginger` 用户的密码。**

---

## 第三步：安装图形化桌面 (XFCE)

我们将在 `ginger` 用户下安装 XFCE 桌面环境。因为这是系统级的安装，所以需要 `sudo` 权限。

```bash
sudo apt install xfce4 xfce4-goodies -y
```
这个过程会下载并安装数百兆的文件，请耐心等待其完成。

---

## 第四步：安装并配置远程桌面服务 (xrdp)

1.  **安装 xrdp**:
    这同样是系统级安装，需要 `sudo`。
    ```bash
    sudo apt install xrdp -y
    ```

2.  **配置 xrdp 权限**:
    将 xrdp 用户添加到证书用户组，解决潜在的连接问题。这是系统用户管理，需要 `sudo`。
    ```bash
    sudo adduser xrdp ssl-cert
    ```

3.  **指定 xrdp 使用 XFCE 桌面 (为 ginger 用户配置)**:
    这个操作是告诉系统，当 `ginger` 用户通过远程桌面登录时，应该启动 XFCE。
    **注意：此命令不需要 `sudo`**，因为您是在自己的家目录 (`/home/ginger/`) 下创建配置文件。
    ```bash
    echo "xfce4-session" > ~/.xsession
    ```

4.  **重启 xrdp 服务**:
    要让所有配置生效，需要重启 xrdp 服务。管理系统服务需要 `sudo`。
    ```bash
    sudo systemctl restart xrdp
    ```

---

## 第五步：配置防火墙

1.  **允许远程桌面端口**:
    我们需要在服务器的防火墙上放行 RDP 协议的默认端口 `3389`。修改防火墙规则需要 `sudo`。
    ```bash
    sudo ufw allow 3389/tcp
    sudo ufw reload
    ```

2.  **配置云服务商安全组 (非常重要！)**:
    登录您的云服务商（如阿里云、腾讯云等）的控制台，找到您服务器实例的 **安全组** 或 **防火墙** 规则。
    *   **添加入站规则**：
        *   **协议**: TCP
        *   **端口**: 3389
        *   **源地址**: `0.0.0.0/0` (为了方便，允许任何IP连接)

---

## 第六步：安装 Google Chrome 浏览器

> **小知识：为什么是 `amd64`？**
> 在 Linux 世界里，`amd64` 是 64 位 x86 架构的通用名称，它同时适用于 **Intel** 和 **AMD** 的 64 位 CPU。您的 x86 服务器是 64 位的，所以使用 `amd64` 的安装包是完全正确的。

1.  **下载 Chrome 安装包**:
    我们把文件下载到 `ginger` 用户的家目录。下载文件**不需要 `sudo`**。
    ```bash
    wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
    ```

2.  **安装 Chrome**:
    使用 `apt` 来安装这个下载好的 `.deb` 文件，`apt` 会自动处理依赖关系。安装软件到系统中，**需要 `sudo`**。
    ```bash
    sudo apt install ./google-chrome-stable_current_amd64.deb -y
    ```

3.  **清理安装包**:
    安装完成后，可以删除下载的安装文件以保持整洁。删除您自己家目录里的文件**不需要 `sudo`**。
    ```bash
    rm google-chrome-stable_current_amd64.deb
    ```

**🎉 至此，您服务器上的所有配置都已完成！**

---

## 第七步：从您的 MacBook Pro 连接

现在，回到您的 Mac 电脑进行最后的操作。

1.  **下载微软远程桌面应用**:
    在 Mac 的 `App Store` 中搜索并安装 **Microsoft Remote Desktop**。

2.  **添加并配置服务器连接**:
    *   打开 Microsoft Remote Desktop 应用。
    *   点击左上角的 **“+”** 按钮，选择 **“Add PC”**。
    *   在 **“PC name”** 处，填写您的 **服务器IP地址**。
    *   在 **“User account”** 下拉菜单中，选择 **“Add User Account...”**。
        *   **Username**: `ginger`
        *   **Password**: 输入您为 `ginger` 用户设置的密码。
        *   点击 **“Add”** 保存账户。
    *   确认所有设置后，点击右下角的 **“Add”** 保存这个 PC 配置。

3.  **连接！**:
    *   在主界面双击您刚刚创建的服务器卡片。
    *   如果弹出证书安全警告，请勾选 "Don't ask me again" 并点击 **“Continue”**。
    *   稍等片刻，您会看到一个 xrdp 登录窗口。输入 `ginger` 的密码，点击 **“OK”**。

现在，您应该已经成功进入了远在云端的 Ubuntu 图形化桌面！

---

## 第八步：设置中文语言


1.  **安装中文字体**:
    安装一套或多套高质量的中文字体。
    ```bash
    sudo apt install -y fonts-noto-cjk
    ```

2.  **安装并配置中文语言包（Locale）**:
    安装简体中文语言包：
    ```bash
    sudo apt install language-pack-zh-hans
    ```
    用一条命令直接修改配置文件并更新：
    ```bash
    sudo update-locale LANG=zh_CN.UTF-8
    ```

3.  **重启**:
    所有的语言和字体设置，都需要在用户重新登录后才能完全生效。
    ```bash
    sudo reboot
    ```
