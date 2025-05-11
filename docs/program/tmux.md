---
tags:
  - Linux
---

# Tmux

Tmux 是一个终端复用器（terminal multiplexer），一般用于 Terminal 的窗口管理。

Tmux 拥有如下特性：

* 可以同时开启多个会话和窗口，并持久地保存工作状态。
* 断线后任务能够在后台继续执行。

Tmux 由如下三个基础组成：

1. Session（会话），任务通常在 session 中运行，在断开连接后 session 仍会保持。
2. Window（窗口），一个会话可以包含多个窗口。
3. Pane（窗格），一个窗口可以包含多个窗格。

## 安装配置

Ubuntu 或 Debian

```bash
sudo apt-get install tmux
```

CentOS 或 Fedora

```bash
sudo yum install tmux
```

Mac

```bash
brew install tmux
```

Tmux 启动后，会调用 ~/.tmux.conf 文件，此文件包含了 Tmux 命令的一系列配置，如需要配置，都需要修改此文件。

??? Note "Tmux配置文件 (.tmux.conf)"
    ```bash
    set-option -g status-keys vi
    setw -g mode-keys vi

    setw -g monitor-activity on

    # setw -g c0-change-trigger 10
    # setw -g c0-change-interval 100

    # setw -g c0-change-interval 50
    # setw -g c0-change-trigger  75


    set-window-option -g automatic-rename on
    set-option -g set-titles on
    set -g history-limit 100000

    #set-window-option -g utf8 on

    # set command prefix
    set-option -g prefix C-a
    unbind-key C-b
    bind-key C-a send-prefix

    bind h select-pane -L
    bind j select-pane -D
    bind k select-pane -U
    bind l select-pane -R

    bind -n M-Left select-pane -L
    bind -n M-Right select-pane -R
    bind -n M-Up select-pane -U
    bind -n M-Down select-pane -D

    bind < resize-pane -L 7
    bind > resize-pane -R 7
    bind - resize-pane -D 7
    bind + resize-pane -U 7


    bind-key -n M-l next-window
    bind-key -n M-h previous-window



    set -g status-interval 1
    # status bar
    set -g status-bg black
    set -g status-fg blue


    #set -g status-utf8 on
    set -g status-justify centre
    set -g status-bg default
    set -g status-left " #[fg=green]#S@#H #[default]"
    set -g status-left-length 20


    # mouse support
    # for tmux 2.1
    # set -g mouse-utf8 on
    set -g mouse on
    #
    # for previous version
    #set -g mode-mouse on
    #set -g mouse-resize-pane on
    #set -g mouse-select-pane on
    #set -g mouse-select-window on


    #set -g status-right-length 25
    set -g status-right "#[fg=green]%H:%M:%S #[fg=magenta]%a %m-%d #[default]"

    # fix for tmux 1.9
    bind '"' split-window -vc "#{pane_current_path}"
    bind '%' split-window -hc "#{pane_current_path}"
    bind 'c' new-window -c "#{pane_current_path}"

    # run-shell "powerline-daemon -q"

    # vim: ft=conf
    ```

Tmux有大量的快捷键，所有的快捷键都要使用 `Ctrl+b` 作为前缀唤醒。  
（使用以上配置可将前缀键改为 `Ctrl+a` ）

## 常用命令

### 新建会话

第一个启动的会话名为 0，之后是 1、2 依次类推。

```bash
tmux
# 指定会话名称
tmux new -s <session-name> [n <window-name>]
```

### 分离会话

如果我们想离开会话，但又不想关闭会话，有两种方式。按下 `Ctrl+b d` 或者输入 `tmux detach` 指令，将会分离会话与窗口。

```bash
tmux detach
```

### 查看会话

```bash
tmux ls
tmux list-session
```

如果是处于会话中，可以使用对应的 Tmux 快捷键 `Ctrl+b s`，此时tmux将打开一个会话列表，按上下键可选中目标会话，按左右键可收起或展开会话的窗口，选中目标会话或窗口后，按回车键即可完成切换。

### 接入会话

```bash
tmux attach -t <session-name>
# 可简写，默认打开上次打开的会话
tmux a
tmux a -t <session-name>
```

### 杀死会话

```bash
tmux kill-session -t <session-name>
```

### 切换会话

```bash
tmux switch -t <session-name>
```

### 重命名会话

```bash
tmux rename-session -t <old-name> <new-name>
```

对应快捷键为 `Ctrl+b $` 。


## 其他命令

列出所有快捷键，及其对应的 Tmux 命令

```bash
tmux list-keys
```

列出所有 Tmux 命令及其参数

```bash
tmux list-commands
```

列出当前所有 Tmux 会话的信息
```bash
tmux info
```

重新加载当前的 Tmux 配置

```bash
tmux source-file ~/.tmux.conf
```

## 快捷指令

Tmux的所有指令，都包含同一个前缀，默认为 `Ctrl+b` ，输入完前缀过后，控制台激活，命令按键才能生效。

### 系统指令

|   前缀   |     指令    |                  描述                |
| :------: | :--------: | :----------------------------------: |
|  Prefix  |     ?      |  显示快捷键帮助文档                    |
|  Prefix  |     d      |  断开当前会话                         |
|  Prefix  |     D      |  选择要断开的会话选择要断开的会话       |
|  Prefix  |   Ctrl+z   |  挂起当前会话                         |
|  Prefix  |     r      |  强制重载当前会话                     |
|  Prefix  |     s      |  显示会话列表用于选择并切换            |
|  Prefix  |     :      |  进入命令行模式，此时可直接输入ls等命令 |
|  Prefix  |     [      |  进入复制模式，按q退出                 |
|  Prefix  |     ]      |  粘贴复制模式中复制的文本              |
|  Prefix  |     ~      |  列出提示信息缓存                     |

### 窗口（window）指令

|   前缀   |     指令    |                  描述                   |
| :------: | :--------: | :-------------------------------------: |
|  Prefix  |     c      |  新建窗口                                |
|  Prefix  |     &      |  关闭当前窗口（关闭前需输入y or n确认）    |
|  Prefix  |    0~9     |  切换到指定窗口                           |
|  Prefix  |     p      |  切换到上一窗口                           |
|  Prefix  |     n      |  切换到下一窗口                           |
|  Prefix  |     w      |  打开窗口列表，用于且切换窗口              |
|  Prefix  |     ,      |  重命名当前窗口                           |
|  Prefix  |     .      |  修改当前窗口编号（适用于窗口重新排序）     |
|  Prefix  |     f      |  快速定位到窗口（输入关键字匹配窗口名称）   |

### 面板（pane）指令

|   前缀   |     指令    |                  描述                                         |
| :------: | :--------: | :-----------------------------------------------------------: |
|  Prefix  |     "      |  当前面板上下一分为二，下侧新建面板                              |
|  Prefix  |     %      |  当前面板左右一分为二，右侧新建面板                              |
|  Prefix  |     x      |  关闭当前面板（关闭前需输入y or n确认）                          |
|  Prefix  |     z      |  最大化当前面板，再重复一次按键后恢复正常                         |
|  Prefix  |     !      |  将当前面板移动到新的窗口打开（原窗口中存在两个及以上面板有效）     |
|  Prefix  |     ;      |  切换到最后一次使用的面板                                       |
|  Prefix  |     q      |  显示面板编号，在编号消失前输入对应的数字可切换到相应的面板         |
|  Prefix  |     {      |  向前置换当前面板                                              |
|  Prefix  |     }      |  向后置换当前面板                                              |
|  Prefix  |   Ctrl+o   |  顺时针旋转当前窗口中的所有面板                                  |
|  Prefix  |   方向键   |  移动光标切换面板                                               |
|  Prefix  |     o      |  选择下一面板                                                  |
|  Prefix  |   空格键   |  在自带的面板布局中循环切换                                      |
|  Prefix  |  Alt+方向键 |  以5个单元格为单位调整当前面板边缘                               |
|  Prefix  | Ctrl+方向键 |  以1个单元格为单位调整当前面板边缘（Mac下被系统快捷键覆盖）        |
|  Prefix  |     t      |  显示时钟                                                      |


## 参考文章

* [Tmux 使用手册](http://louiszhai.github.io/2017/09/30/tmux/)
* [Tmux 使用教程](https://www.ruanyifeng.com/blog/2019/10/tmux.html)