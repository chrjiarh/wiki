---
tags:
  - Linux
---

# Git

Git 是目前使用最广泛的版本控制系统之一。

Git 的四个区域：

- 工作区（Working Directory）：就是平时存放项目代码的地方。
- 暂存区（Stage / Index）：也叫索引，数据暂时存放的区域，用于临时存放文件的改动。一般存放在 .git 目录下的 index 中。
- 本地仓库（Repository）：Git 在本地的版本库，仓库信息存储在 .git 这个隐藏目录中。
- 远程仓库（Remote Repository）：托管在远程服务器上的仓库。常用的有GitHub、 GitLab、 Gitee。

Git 的三种文件状态：

- 已修改（Modify）：修改了但是没有保存到暂存区的文件。
- 已暂存（Staged）：修改后已经保存到暂存区的文件。
- 已提交（Committed）：把暂存区的文件提交到本地仓库后的状态。



## 安装配置

### 安装

安装详见 [Git - Downloads](https://git-scm.com/downloads)。

Git 配置文件常用的有两个级别：

1. 当前用户的全局配置文件。
2. 当前仓库的局部配置文件。

> 设置多个配置文件时，局部设置会自动覆盖全局设置。  

### 显示配置

列出所有全局配置：
```bash
$ git config --global -l
```

通过 `git config -l` 列出当前已经设置的所有配置参数。

### 设置用户信息

设置用户名和邮箱：

```bash
$ git config --global user.name xxx
$ git config --global user.email xxxx@xxx.com
```

这里的 `--global` 表示修改的是全局配置，即该设置对当前用户下的所有仓库均有效。

如果不添加 `--global` 选项，则会默认修改当前仓库下的局部配置文件。

### 配置忽略文件

工作区新建一个名称为 `.gitignore` 的文件，然后把要忽略的文件名填进去，Git就会自动忽略这些文件，各种 `.gitignore` 文件可以参考官方提供的 [配置文件](https://github.com/github/gitignore)。

??? abstract "`.gitignore` 忽略规则的匹配语法"
    示例：
    ```bash
    #注释           .gitignore的注释
    *.txt           忽略所有 .txt 后缀的文件
    !src.a          忽略除 src.a 外的其他文件
    /todo           仅忽略项目根目录下的 todo 文件，不包括 src/todo
    build/          忽略 build/目录下的所有文件，过滤整个build文件夹；
    doc/*.txt       忽略doc目录下所有 .txt 后缀的文件，但不包括doc子目录的 .txt 的文件
    
    bin/:           忽略当前路径下的 bin 文件夹，该文件夹下的所有内容都会被忽略，不忽略 bin 文件
    /bin:           忽略根目录下的 bin 文件
    /*.c:           忽略 cat.c，不忽略 build/cat.c
    debug/*.obj:    忽略debug/io.obj，不忽略 debug/common/io.obj和tools/debug/io.obj
    **/foo:         忽略/foo, a/foo, a/b/foo等
    a/**/b:         忽略a/b, a/x/b, a/x/y/b等
    !/bin/run.sh    不忽略bin目录下的run.sh文件
    *.log:          忽略所有 .log 文件
    config.js:      忽略当前路径的 config.js 文件
    
    /mtk/           忽略整个文件夹
    *.zip           忽略所有.zip文件
    /mtk/do.c       忽略某个具体文件
    ```
    注：Git 对于 `.gitignore` 配置文件是按行从上到下进行规则匹配的，意味着如果前面的规则匹配的范围更大，则后面的规则将不会生效。


## 基本操作


### 创建仓库

**初始化仓库**

```bash
$ git init
```

Git 将在当前文件夹新建一个 `.git` 文件夹，一个仓库就这样建好了。

**克隆仓库**

如果想把一个仓库克隆到自己的电脑上，采用 `git clone` 命令即可。

```bash
$ git clone <url>
```

这样，被克隆的仓库的内容就会被储存到当前文件夹下一个与仓库同名的新文件夹。


### 添加和提交

**添加到暂存区**

```bash
$ git add <file>
```

`git add` 命令可将该文件的修改添加到暂存区，`git add .` 表示添加所有文件到暂存区。

如果希望撤销暂存区的文件，重新放回工作区，使用 `git restore --staged <file>`。

**提交更新**

```bash
$ git commit -m <message>
```

`git commit` 命令可以提交所有暂存区的文件到本地仓库，常用参数：

-  `-a` ：在提交前将所有已跟踪的文件的更改放入暂存区。未被跟踪的文件不会被自动加入暂存区，需要用 `git add` 命令手动添加。
-  `-m` ：后面输入的是本次提交的说明，可以输入任意内容，最好是有意义的。
-  `--amend` ：将当前改动追加到最近一次提交上。也叫追加提交，它可以在不增加一个新的提交的情况下，将新修改的代码追加到前一次的提交中。

??? Note "Git &nbsp; Commit &nbsp; Message 规范"
    每次提交，Commit message 都包括三个部分：**Header**（必需），**Body**（可选） 和 **Footer**（可选）。
    ```bash
    <type>(<scope>): <subject>
    <空行>
    <body>
    <空行>
    <footer>
    ```
    不管是哪一个部分，任何一行都不得超过72个字符（或100个字符）。
    
    Header 部分只有一行，包括三个字段：`type`（必需）、`scope`（可选）和 `subject`（必需）。Footer 部分如果提交存在一个与之关联的 Issue 则要关联。

    （1）**type**

    `type` 用于说明 commit 的类别：
    
    - feat：新功能（feature）
    - fix：修补bug
    - docs：文档（documentation）
    - style： 格式（不影响代码运行的变动）
    - refactor：重构（即不是新增功能，也不是修改bug的代码变动）
    - test：增加测试
    - chore：构建过程或辅助工具的变动
    
    （2）**scope**

    `scope` 用于说明 commit 影响的范围，比如数据层、控制层、视图层等等，视项目不同而不同。

    （3）**subject**
    
    `subject` 是 commit 目的的简短描述，一般不超过50个字符。

    - 以动词开头，使用第一人称现在时，比如change，而不是changed或changes
    - 第一个字母小写
    - 结尾不加句号（.）


### 查看状态或差异

**查看状态**

```bash
$ git status
```

`git status` 命令可以查看上次提交之后是否有对文件进行再次修改。

- `-s` ：获得简短的输出结果。

**查看提交历史**

```bash
$ git log
```

`git log` 查看历史提交记录。

- `--oneline` ：表示简洁模式，以简洁的一行格式显示提交信息。
- `--graph` ：用于以图形化的方式展示提交历史。
- `--abbrev-commit` ：缩短提交的 SHA-1 标识。
- `--reverse` ：逆向显示所有日志。

**查看HEAD指针日志**

```bash
$ git reflog
```

`git reflog` 用于查看本地仓库的引用日志，查看HEAD指针的移动历史。可以找回已经丢失的提交记录、分支以及其他引用的历史状态。

**查看差异**

```bash
$ git diff
```

`git diff` 命令比较文件的不同，即比较文件在暂存区和工作区的差异。

- `git diff <file>` ：可查看 `<file>` 文件暂存区和工作区的差异。
- `git diff <commit-id> <commit-id>` ：查看两个提交之间的差异。


### 撤销和恢复

**撤销或恢复文件**

```bash
$ git restore <file>
```

`git restore` 命令可用于撤销或恢复文件的更改。

- `--staged` ：恢复暂存区中的文件到最新提交状态。
- `--source=<commit>` ：恢复文件到指定提交的状态。

!!! Warning " `git restore` 命令是 Git 2.23 版本后引入的，此前版本需使用 `git checkout` 命令。"

**撤消或恢复提交**

```bash
$ git reset <commit-id>
```

`git reset` 命令用于回退版本，可以指定退回某一次提交的版本，并且删除所有之后的提交，可使用的参数：

- `--mixed` ：默认模式，移动 HEAD 指针，重置暂存区为指定提交的状态，工作区文件内容保持不变。
- `--soft` ：只移动 HEAD 指针，不会修改暂存区和工作区。
- `--hard` ：移动 HEAD 指针，并且重置暂存区和工作区，丢弃所有的本地修改。

!!! Danger "如果已经将提交推送到远程仓库，则建议不要使用 `git reset`，因为它会重写提交的历史记录，将会影响他人的提交，所以使用 `git revert` 会更好。"

```bash
$ git revert <commit-id>
```

`git revert` 命令创建一个新的提交，用来撤销指定的提交。 要撤销一串提交可以用 `<commit-id1>..<commit-id2>` 语法。 注意这是一个前开后闭区间，即不包括 `commit1-id`，但包括 `commit2-id`。

- `-n` 或 `--no-commit` ：默认会自动提交commit，使用此参数不进行自动提交。

如果为默认提交，则执行命令后会弹出编辑器用于编辑提交信息，这时候应该标注 revert 的原因。通常情况下，我们不进行自动提交，使用`no-commit`选项有利于连续撤销多个提交，最后统一进行提交。


??? Note "HEAD 说明"
    可以用 `^` 表示前面的版本：

    - `HEAD` 表示当前版本
    - `HEAD^` 表示上一个版本
    - `HEAD^^` 表示上上一个版本
    - 以此类推...
    
    也可以使用 `~` 加数字表示：

    - `HEAD~0` 表示当前版本
    - `HEAD~1` 表示上一个版本
    - `HEAD~2` 表示上上一个版本
    - 以此类推...

**移动和重命名**

```bash
$ git mv <file> <new-file>
```

`git mv` 命令用于移动或重命名一个文件、目录或软连接。

- `-f` ：如果新文件名已经存在，可使用 `-f` 参数来强制执行。

**删除文件**

```bash
$ git rm <file>
```

`git rm` 命令从工作区和暂存区删除一个文件，并且将这次删除放入暂存区。

- `-f` ：如果删除之前修改过并且已经放到暂存区域的话，可使用 `-f` 参数来强制删除。
- `--cached` ：参数可将文件从暂存区中删除，但是本地工作文件还在，只是不希望这个文件被版本控制。



## 分支管理


### 创建和重命名分支

**创建分支**

```bash
$ git branch <branch-name>
```

`git branch <branch-name>` 命令用于创建一个新分支。

**重命名分支**

```bash
$ git branch -m <new-branch-name>
$ git branch -m <old-branch-name> <new-branch-name>
```

`git branch -m` 命令用于将当前分支重命名，或者将对应分支重命名。


### 查看和切换分支

**查看分支**

```bash
$ git branch
```

`git branch` 命令用于查看所有本地分支，当前分支前面会有一个星号 `*`。

- `-r` 或 `--remote` ：查看远程分支。
- `-a` 或 `--all` ：查看所有分支。
- `-u` 或 `--set-upstream-to=<upstream>`：设置本地分支的追踪关系。
- `--unset-upstream` ：移除本地分支的追踪关系。
- `-vv` ：显示详细信息，如每个分支的最新提交、每个分支与远程分支的追踪关系。

??? Note "设置追踪关系"
    设置追踪关系（tracking relationship）是指将本地分支与远程分支进行关联，使得 Git 知道本地分支应该推送到哪个远程分支，以及从哪个远程分支拉取更新。  
    设置追踪关系的命令是 `git branch --set-upstream-to` 或简写为 `git branch -u`，示例如下：
    ```bash
    $ git branch --set-upstream-to=origin/master master
    ```
    这个命令将本地的 `master` 分支与远程的 `origin/master` 分支建立追踪关系。从此之后，在 `master` 分支上使用 `git push` 或 `git pull` 命令时，Git 将自动与 `origin/master` 分支进行交互。

**切换分支**

```bash
$ git switch <branch-name>
```

`git switch` 命令用于从当前分支切换到指定的分支。`git switch -` 表示切换到前一个分支。

- `-c` 或 `--create` ：用于创建一个新分支，并且切换到新创建的分支。

!!! Warning " `git switch` 命令是 Git 2.23 版本后引入的，此前版本需使用 `git checkout` 命令。"


### 删除分支

```bash
$ git branch -d <branch-name>
```

`git branch -d` 命令用于删除分支，如果分支中的更改已经被合并到其他分支中，那么删除操作会成功，否则会提示错误。

`git branch -D` 命令用于强制删除分支，无论它的更改是否已经被合并到其他分支中。


### 合并分支

```bash
$ git merge <branch-name>
```

`git merge` 命令用于合并分支，将 `<branch-name>` 分支合并到当前分支。

**解决合并冲突**

合并分支时，由于不同提交对文件有不同的修改，会产生合并冲突，需要手动解决这些冲突后再提交。

对于每个发生了合并冲突的文件，Git 都会在这些文件中加入标准的冲突解决标记。例如：

```markdown
<<<<<<< HEAD
Creating a new branch is quick & simple.
=======
Creating a new branch is quick AND simple.
>>>>>>> dev
```

Git用 `<<<<<<<`，`=======`，`>>>>>>>` 标记出不同分支的内容。

修改冲突文件后保存，再提交。



## 远程仓库


### 远程仓库操作

**查看远程仓库**

```bash
$ git remote -v
```

`git remote` 命令列出当前仓库中已配置的远程仓库。

- `-v` ：列出当前仓库中已配置的远程仓库，并显示它们的 URL。

```bash
$ git remote show <remote-name>
```

`git remote show <remote-name>` 命令显示指定远程仓库的详细信息，包括 URL 和跟踪分支。

**添加远程仓库**

```bash
$ git remote add <remote-name> <remote-url>
```

`git remote add` 命令添加一个新的远程仓库。指定一个远程仓库的名称和 URL，将其添加到当前仓库中。

**重命名远程仓库**

```bash
$ git remote rename <old-name> <new-name>
```

`git remote rename` 命令将已配置的远程仓库重命名。

**删除远程仓库**

```bash
$ git remote remove <remote-name>
```

`git remote remove` 命令从当前仓库中删除指定的远程仓库。

**修改远程仓库**

```bash
$ git remote set-url <remote-name> <new-url>
```

`git remote set-url` 命令修改指定远程仓库的 URL。


### 从远程获取代码库

```bash
$ git fetch <remote-name>
$ git fetch <remote-name> <branch-name>
```

`git fetch` 命令用于从远程获取代码库，获取所有远程分支。加上 `<branch-name>` 表示获取某一个特定的远程分支。

需要注意的是，`git fetch` 命令只会获取远程仓库的更改，而不会将这些更改合并到本地仓库中。


### 下载远程代码并合并

```bash
$ git pull <remote-name> <branch-name>
$ git pull <remote-name> <branch-name>:<remote-branch-name>
```

`git pull` 命令用于从远程仓库获取代码且合并到本地，默认拉取远程仓库名 `origin` 的 `master` 或者 `main` 分支。

> 如果本地分支名与远程分支名相同，则可以省略冒号。

!!! Note "`git pull` 其实就是 `git fetch` 和 `git merge FETCH_HEAD` 的简写。"


### 上传远程代码并合并

```bash
$ git push <remote-name> <branch-name>
$ git push <remote-name> <branch-name>:<remote-branch-name>
```

`git push` 命令用于推送代码到远程仓库并合并。

- `-u` 或 `--set-upstream` ：将当前分支的提交推送到远程仓库，并将本地分支与远程分支关联起来，以便下次推送时可以简化命令。
- `-f` 或 `--force` ：强制推送，即使远程仓库有更新，也会覆盖远程仓库的提交。
- `--all` ：推送所有分支到远程仓库。
- `--tags` ：默认不会推送标签，使用此选项可以推送标签到远程仓库。



## 标签

**列出标签**

```bash
$ git tag
```

`git tag` 命令用于列出当前仓库中所有的标签。

**查看某个标签**

```bash
$ git show <tag-name>
```

`git show <tag-name>` 命令用于查看某个特定标签对应的提交信息。

**创建标签**

```bash
$ git tag <tag-name>
$ git tag <tag-name> <commit-id>
$ git tag -a <tag-name> -m <message> <commit-id>
```

`git tag <tag-name>` 命令用于创建一个新的标签，默认是指向最新提交的标签。

- `-a` ：创建一个带有注释的标签，并 `-m` 添加附加注释信息。

**删除标签**

```bash
$ git tag -d <tag-name>
```

`git tag -d` 命令用于删除一个标签。

**远程仓库中的标签**

```bash
$ git push <remote-name> <tag-name>
$ git push <remote-name> --tags
```

`git push` 命令可用于推送某个标签，或者推送所有标签到远程仓库。

要删除远程仓库中的标签，需要先删除本地标签，然后用 `$ git push <remote-name> :refs/tags/<tagname>` 命令将删除操作推送到远程仓库中。
 
