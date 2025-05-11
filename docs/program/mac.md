---
tags:
  - Mac
---

# Mac 环境配置

## 安装 Command Line Tools

- 方法一：使用 Terminal 运行如下代码安装，默认安装最新版本

    ```bash
    xcode-select --install
    ```

    在弹出的窗口点击安装。

- 方法二：登录苹果开发网站下载 `.dmg` 文件安装

    下载地址：[https://developer.apple.com/download/all/](https://developer.apple.com/download/all/)

验证安装，如果返回路径则表示安装成功。

```bash
xcode-select --print-path
```

## 关闭 MacOS 自动更新

首先关闭所有自动更新和安装更新的按钮：系统设置 > 通用 > 软件更新 > 自动更新。

然后配置屏蔽网络访问，让系统无法检测软件更新。

```bash
sudo vim /etc/hosts
```

添加以下内容：

```bash
## Mac Software Update
127.0.0.1 swdist.apple.com
127.0.0.1 swscan.apple.com
127.0.0.1 swcdn.apple.com
127.0.0.1 gdmf.apple.com
127.0.0.1 mesu.apple.com
127.0.0.1 xp.apple.com
```

观察是否成功屏蔽软件更新检测：

```bash
softwareupdate --list
```

> 恢复升级：删除上面在 hosts 文件中添加的内容即可恢复检测软件更新。

??? warning "注意：使用系统代理或虚拟专用网时，将绕过本地 DNS 设置，所以仅设置 hosts 文件仍然会检测到更新。"
    解决办法（此方法适用于 Clash，配置优先级 Mixin > TUN Mode > Profile ）：  

    方法一：配置文件预处理  
    打开 Clash > 配置[Profiles] > 右键选择某个配置 > 配置文件预处理[Parsers] > 编辑解析器：
    ```yaml
    parsers: # array
      - url: 机场订阅
        yaml:
          # 代表在原规则前面追加规则
          prepend-rules:
            - DOMAIN-SUFFIX,swdist.apple.com,REJECT
            - DOMAIN-SUFFIX,swscan.apple.com,REJECT
            - DOMAIN-SUFFIX,swcdn.apple.com,REJECT
            - DOMAIN-SUFFIX,gdmf.apple.com,REJECT
            - DOMAIN-SUFFIX,mesu.apple.com,REJECT
            - DOMAIN-SUFFIX,xp.apple.com,REJECT
    ```
    这样每次更新订阅后，自定义的规则将添加到配置文件中。

    方法二：使用 Mixin （混合配置）  
    直接用 mixin 配置，js 代码功能更强；修改过程是发生在内存中的，不会影响原配置文件；启用 Mixin 混合模式后对所有配置文件都生效。  
    打开 Clash > 设置[Settings] > 混合配置[Mixin] > 类型选择 JavaScript > 编辑：
    ```javascript
    module.exports.parse = async ({ content, name, url }, { axios, yaml, notify }) => {
      content["proxy-groups"].unshift({
        name: "自定义规则组-REJECT",
        proxies: ["REJECT"],
        type: "select"
      });

      content.rules.unshift(
        "DOMAIN-SUFFIX,swdist.apple.com,REJECT",
        "DOMAIN-SUFFIX,swscan.apple.com,REJECT",
        "DOMAIN-SUFFIX,swcdn.apple.com,REJECT",
        "DOMAIN-SUFFIX,gdmf.apple.com,REJECT",
        "DOMAIN-SUFFIX,mesu.apple.com,REJECT",
        "DOMAIN-SUFFIX,xp.apple.com,REJECT"
      );

      let msg = content.rules.slice(0, 5).join(" | ").toString();
      notify("CFW content.rules 被 Mixin 更新！", msg, false);

      return content;
    };
    ```
    打开 Clash > 主页[General] > 混合模式[Mixin] > 打开右侧按钮。


## 终端配置

### 配置代理

使用的代理软件的端口是 7890，配置文件在 `～/.zshrc` 文件中。

??? abstract "配置代理 `～/.zshrc`"
    ```bash
    function proxy_off(){
        unset http_proxy
        unset https_proxy
        unset socks_proxy
        unset ftp_proxy
        unset rsync_proxy
        unset HTTP_PROXY
        unset HTTPS_PROXY
        unset SOCKS_PROXY
        unset FTP_PROXY
        unset RSYNC_PROXY
        echo -e "已关闭代理"
    }
    function proxy_on() {
        # 设置不需要代理的地址
        export no_proxy="localhost,127.0.0.1,localaddress,.localdomain.com"
        export http_proxy="http://127.0.0.1:7890"
        export https_proxy=$http_proxy
        export socks_proxy="socks5://127.0.0.1:7890"
        export ftp_proxy=$http_proxy
        export rsync_proxy=$http_proxy
        export HTTP_PROXY=$http_proxy
        export HTTPS_PROXY=$http_proxy
        export SOCKS_PROXY=$socks_proxy
        export FTP_PROXY=$http_proxy
        export RSYNC_PROXY=$http_proxy
        # echo -e "已开启代理"
    }
    function proxy_status() {
        echo $https_proxy
        curl cip.cc
    }
    proxy_on
    ```

### 配置 Vim

创建 Vim 配置文件

```bash
touch ~/.vimrc
```

编辑此文件

??? abstract "配置Vim `～/.vimrc`"
    ```bash
    " An example for a vimrc file.
    "
    " To use it, copy it to
    "     for Unix and OS/2:  ~/.vimrc
    "             for Amiga:  s:.vimrc
    "  for MS-DOS and Win32:  $VIM\_vimrc
    "           for OpenVMS:  sys$login:.vimrc
    
    " When started as "evim", evim.vim will already have done these settings.
    if v:progname =~? "evim"
      finish
    endif
    
    " Use Vim settings, rather then Vi settings (much better!).
    " This must be first, because it changes other options as a side effect.
    set nocompatible
    
    " allow backspacing over everything in insert mode
    set backspace=indent,eol,start
    
    if has("vms")
      set nobackup          " do not keep a backup file, use versions instead
    else
      set backup            " keep a backup file
    endif
    set history=50          " keep 50 lines of command line history
    set ruler               " show the cursor position all the time
    set showcmd             " display incomplete commands
    set incsearch           " do incremental searching
    "==========================================================================
    "My Setting-sunshanlu
    "==========================================================================
    vmap <leader>y :w! /tmp/vitmp<CR>
    nmap <leader>p :r! cat /tmp/vitmp<CR>
    
    "语法高亮
    syntax enable
    syntax on
    "显示行号
    set nu
    
    "修改默认注释颜色
    "hi Comment ctermfg=DarkCyan
    "允许退格键删除
    "set backspace=2
    "启用鼠标
    set mouse=a
    set selection=exclusive
    set selectmode=mouse,key
    "按C语言格式缩进
    set cindent
    set autoindent
    set smartindent
    set shiftwidth=4
    
    " 允许在有未保存的修改时切换缓冲区
    "set hidden
    
    " 设置无备份文件
    set writebackup
    set nobackup
    
    "显示括号匹配
    set showmatch
    "括号匹配显示时间为1(单位是十分之一秒)
    set matchtime=5
    "显示当前的行号列号：
    set ruler
    "在状态栏显示正在输入的命令
    set showcmd
    
    set foldmethod=syntax
    "默认情况下不折叠
    set foldlevel=100
    " 开启状态栏信息
    set laststatus=2
    " 命令行的高度，默认为1，这里设为2
    set cmdheight=2
    
    
    " 显示Tab符，使用一高亮线代替
    set list
    "set listchars=tab:\|\ ,
    set listchars=tab:>-,trail:-
    
    
    "侦测文件类型
    filetype on
    "载入文件类型插件
    filetype plugin on
    "为特定文件类型载入相关缩进文件
    filetype indent on
    " 启用自动补全
    filetype plugin indent on 
    
    
    "设置编码自动识别, 中文引号显示
    filetype on "打开文件类型检测
    "set fileencodings=euc-cn,ucs-bom,utf-8,cp936,gb2312,gb18030,gbk,big5,euc-jp,euc-kr,latin1
    set fileencodings=utf-8,gb2312,gbk,gb18030
    "这个用能很给劲，不管encoding是什么编码，都能将文本显示汉字
    "set termencoding=gb2312
    set termencoding=utf-8
    "新建文件使用的编码
    set fileencoding=utf-8
    "set fileencoding=gb2312
    "用于显示的编码，仅仅是显示
    set encoding=utf-8
    "set encoding=utf-8
    "set encoding=euc-cn
    "set encoding=gbk
    "set encoding=gb2312
    "set ambiwidth=double
    set fileformat=unix
    
    
    "设置高亮搜索
    set hlsearch
    "在搜索时，输入的词句的逐字符高亮
    set incsearch
    
    " 着色模式
    set t_Co=256
    "colorscheme wombat256mod
    "colorscheme gardener
    colorscheme elflord
    "colorscheme desert
    "colorscheme evening
    "colorscheme darkblue
    "colorscheme torte
    "colorscheme default
    
    " 字体 && 字号
    set guifont=Monaco:h10
    "set guifont=Consolas:h10
    
    " :LoadTemplate       根据文件后缀自动加载模板
    "let g:template_path='/home/ruchee/.vim/template/'
    
    " :AuthorInfoDetect   自动添加作者、时间等信息，本质是NERD_commenter && authorinfo的结合
    ""let g:vimrc_author='example'
    ""let g:vimrc_email='example@qq.com'
    ""let g:vimrc_homepage='http://www.example.com'
    "
    "
    " Ctrl + E            一步加载语法模板和作者、时间信息
    ""map <c-e> <ESC>:AuthorInfoDetect<CR><ESC>Gi
    ""imap <c-e> <ESC>:AuthorInfoDetect<CR><ESC>Gi
    ""vmap <c-e> <ESC>:AuthorInfoDetect<CR><ESC>Gi
    
    
    
    " ======= 引号 && 括号自动匹配 ======= "
    "
    ":inoremap ( ()<ESC>i
    
    ":inoremap ) <c-r>=ClosePair(')')<CR>
    "
    ":inoremap { {}<ESC>i
    "
    ":inoremap } <c-r>=ClosePair('}')<CR>
    "
    ":inoremap [ []<ESC>i
    "
    ":inoremap ] <c-r>=ClosePair(']')<CR>
    "
    ":inoremap < <><ESC>i
    "
    ":inoremap > <c-r>=ClosePair('>')<CR>
    "
    "":inoremap " ""<ESC>i
    "
    ":inoremap ' ''<ESC>i
    "
    ":inoremap ` ``<ESC>i
    "
    ":inoremap * **<ESC>i
    
    " 每行超过80个的字符用下划线标示
    ""au BufRead,BufNewFile *.s,*.asm,*.h,*.c,*.cpp,*.java,*.cs,*.lisp,*.el,*.erl,*.tex,*.sh,*.lua,*.pl,*.php,*.tpl,*.py,*.rb,*.erb,*.vim,*.js,*.jade,*.coffee,*.css,*.xml,*.html,*.shtml,*.xhtml Underlined /.\%81v/
    "
    "
    " For Win32 GUI: remove 't' flag from 'guioptions': no tearoff menu entries
    " let &guioptions = substitute(&guioptions, "t", "", "g")
    
    " Don't use Ex mode, use Q for formatting
    map Q gq
    
    " This is an alternative that also works in block mode, but the deleted
    " text is lost and it only works for putting the current register.
    "vnoremap p "_dp
    
    " Switch syntax highlighting on, when the terminal has colors
    " Also switch on highlighting the last used search pattern.
    if &t_Co > 2 || has("gui_running")
      syntax on
      set hlsearch
    endif
    
    " Only do this part when compiled with support for autocommands.
    if has("autocmd")
    
      " Enable file type detection.
      " Use the default filetype settings, so that mail gets 'tw' set to 72,
      " 'cindent' is on in C files, etc.
      " Also load indent files, to automatically do language-dependent indenting.
      filetype plugin indent on
    
      " Put these in an autocmd group, so that we can delete them easily.
      augroup vimrcEx
      au!
    
      " For all text files set 'textwidth' to 80 characters.
      autocmd FileType text setlocal textwidth=80
    
      " When editing a file, always jump to the last known cursor position.
      " Don't do it when the position is invalid or when inside an event handler
      " (happens when dropping a file on gvim).
      autocmd BufReadPost *
        \ if line("'\"") > 0 && line("'\"") <= line("$") |
        \   exe "normal g`\"" |
        \ endif
    
      augroup END
    
    else
    
      set autoindent                " always set autoindenting on
    
    endif " has("autocmd")
    
    " 增加鼠标行高亮
    set cursorline
    hi CursorLine  cterm=NONE   ctermbg=darkred ctermfg=white
    
    " 设置tab是四个空格
    set ts=4
    set expandtab
    
    " 主要给Tlist使用
    let Tlist_Exit_OnlyWindow = 1
    let Tlist_Auto_Open = 1
    ```

## 安装 Homebrew

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

## 安装 Oh My Zsh

```bash
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

再安装 `zsh-syntax-highlighting` 和 `zsh-autosuggestions` 插件

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```
```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

修改配置文件 `~/.zshrc`，配置插件

```bash
plugins=(git zsh-syntax-highlighting zsh-autosuggestions)
```

## 安装 Tmux

```bash
brew install tmux
```

??? abstract "修改 tmux 配置文件 `~/.tmux.conf`"
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

    bind-key -n C-l send-keys -R \; clear-history \; send-keys C-l

    set -g status-interval 1
    # status bar
    set -g status-bg black
    set -g status-fg brightred


    #set -g status-utf8 on
    set -g status-justify centre
    #set -g status-bg default

    set -g status-left "#[fg=#D75F00]#(whoami)@#H #[default]"
    set -g status-left-length 30

    set -g window-status-format "#[fg=brightred]#S:#I#F #W #[default]"
    set -g window-status-current-format "#[fg=brightred]#S:#I#F #W #[default]"

    #set -g status-right-length 25
    set -g status-right "#[fg=green]%H:%M:%S #[fg=#268BD2]%a %m-%d-%y #[default]"

    set -g message-style fg=black,bg=#82AAFF
    set -g message-command-style fg=white,bg=#3b4252


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


    # fix for tmux 1.9
    bind '"' split-window -vc "#{pane_current_path}"
    bind '%' split-window -hc "#{pane_current_path}"
    bind 'c' new-window -c "#{pane_current_path}"

    # run-shell "powerline-daemon -q"

    # vim: ft=conf
    ```

重新加载当前的 Tmux 配置

```bash
tmux source-file ~/.tmux.conf
```

> 注：在 Tmux 中，需要按住 `Shift` 键，才可以选中文本，进行复制操作。Mac 终端是 `fn` 键。

[Tmux 使用教程](tmux.md)

## 安装 Miniconda

下载地址：[https://repo.anaconda.com/miniconda/](https://repo.anaconda.com/miniconda/)

修改 `~/.condarc` 文件，配置 conda [清华源](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)，并且取消默认进入 conda 环境。

```bash
channels:
  - defaults
show_channel_urls: true
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
  - https://repo.anaconda.com/pkgs/main
  - https://repo.anaconda.com/pkgs/r
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
auto_activate_base: false
```

查看 conda 配置信息

```bash
conda config --show-sources
```

修改 `~/.pip/pip.conf` 文件，配置 pip 清华源。

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

## 安装 Docker 部署环境

### 安装 OrbStack

Orbstack 是一个基于 Docker 的轻量级虚拟化平台，专门针对 Mac 进行了优化，尤其是在 M 芯片的设备上。直接安装 Orbstack，而无需先安装 Docker。

官网：[https://orbstack.dev/](https://orbstack.dev/)。

需要修改配置，否则 Docker 拉取不到镜像。

??? abstract "修改配置文件：`~/.orbstack/config/docker.json`"
    ```json
    {
      "proxies" : {
        "http-proxy" : "http://127.0.0.1:7890",
        "no-proxy" : "localhost,127.0.0.1",
        "https-proxy" : "http://127.0.0.1:7890"
      }
    }
    ```

修改配置后，重新启动 Docker

```bash
orb restart docker
```

### 部署 MySQL

??? abstract "MySQL 配置文件 `my.cnf`"
    ```bash
    [mysqld]
    character-set-server=utf8
    max_connections=1000
    [client]
    default-character-set=utf8
    [mysql]
    default-character-set=utf8
    ```


拉取镜像

```bash
docker pull mysql
```

启动 MySQL 容器

```bash
docker run -p 3306:3306 \
 --name mysql \
 -v mysql_log:/var/log/mysql \
 -v mysql_data:/var/lib/mysql \
 -v mysql_conf:/etc/mysql/conf.d \
 --privileged=true \
 -e MYSQL_ROOT_PASSWORD=123456 \
 -d mysql
```

进入容器

```bash
docker exec -it mysql /bin/bash
```

进入 MySQL

```bash
mysql -uroot -p123456
```

### 部署 Redis

??? abstract "Redis 配置文件 `redis.conf`"
    [http://download.redis.io/redis-stable/redis.conf](http://download.redis.io/redis-stable/redis.conf)


拉取镜像

```bash
docker pull redis
```

启动 Redis 容器

```bash
docker run -p 6379:6379 \
--name redis \
-v redis_data:/data \
-v redis_conf:/etc/redis \
-d redis \
redis-server /etc/redis/redis.conf
```

进入容器

```bash
docker exec -it redis /bin/bash
```

进入 Redis

```bash
redis-cli
```

如果需要连接 Redis，可[参考博客](../blog/posts/redis_conf.md)。
