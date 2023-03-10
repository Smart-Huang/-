<center><font size='70'>在Windows环境下使用Git和GitHub</center>



本文记录了笔者如何在自己的Win10电脑上配置和使用Git以及GitHub作为代码版本控制和管理工具。本文主要参考了[Pro Git (git-scm.com)](https://git-scm.com/book/zh/v2)


# Git
## Git的安装和配置
[Git - 安装 Git (git-scm.com)](https://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-%E5%AE%89%E8%A3%85-Git)介绍了多种在Windows上安装Git的方法。笔者是从[Git官网](https://git-scm.com/download/win)下载的官方版本。
安装好Git以后，在使用之前需要进行简单的配置。
win+R输入cmd打开命令行界面，输入一下命令查看所有配置以及他们所在的文件。

```shell
git config --list --show-origin
```
大部分内容不需要我们配置。但是用户名和邮件地址是必须要设置的。我们可以通过以下命令进行配置。
```shell
git config --global user.name "your name"
git config --global user.email "youremail@gmail.com"
```
需要说明的是，如果使用了--global选项，那么该命令只需要运行一次。对于该用户，Git都会使用这些信息。如果没有使用--global，则设置仅对当前项目适用。

## Git基础

要真正使用好Git，仅仅会使用Git的命令是不够的，还需要了解Git背后的哲学。再次推荐阅读[Pro Git](https://git-scm.com/book/zh/v2)原文，里面详细介绍了Git各方面的知识，不管是小白还是大神都能有所收获。

本章仅仅介绍基础命令的使用，对于原理性的内容仅作简单说明。

![Git常用命令](https://csdnimg.cn/cdn/content-toolbar/csdn-logo_.png?v=20190924.1 "CSDN图标")

### 本地初始化

对于本地已有的项目，但是尚未进行版本控制，我们首先需要进入到该项目的目录中。这里有两个方法。
1. Git自带Git Bash工具。如果我们在安装的时候将Git Bash加入到鼠标右键菜单的话，只需要在Windows文件资源管理器中打开项目所在的文件夹，在空白处右键鼠标，选择`Git Bash Here`即可。
2. 没有上述功能也没关系，可以使用`win+R` 输入`cmd`进入Windows的命令行工具，使用`cd`（change directory 改变目录）命令进入项目目录。比如假设项目目录为`C:/users/shephard/downloads/project1`则我们输入以下命令
```shell
cd C:/users/shephard/downloads/project1
```
值得一提的是，Windows的`cd`命令不支持跨盘移动，即不能从C盘移动到D盘。跨盘移动只需要输入盘号即可。例如从C盘移动到D盘：
```shell
D:
```
进入项目目录以后，执行初始化命令
```shell
git init
```
初始化以后会自动生成`.git`文件夹，该文件夹是隐藏的。
### 查看文件状态
使用指令 `git status` 命令查看文件的状态。假设我们现在有个项目，项目包含两个文件 `hello.cpp` 和 `README.md` ，可以得到以下反馈：
```shell
$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        README.md
        hello.cpp

nothing added to commit but untracked files present (use "git add" to track)

```
说明我们当前位于master分支，没有任何提交，有两个未跟踪的文件。
### 跟踪新文件
正如上面的提示，我们可以使用 `git add <file>` 命令来跟踪文件。运行：
```shell
git add hello.cpp 
git add README.md
```
此时再输入 `git status` ，会看到文件已经被**跟踪**，并处于**暂存**状体。
```shell
$ git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   README.md
        new file:   hello.cpp
```
### 忽略文件
有些文件我们不需要纳入Git的管理，但是又不希望它们出现在未跟踪文件列表中。这种情况下我们可以创建一个名为 `.gitignore` 的文件，列出要忽略的文件的模式。编写规则这里不作说明，详见[Git - 记录每次更新到仓库](https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E8%AE%B0%E5%BD%95%E6%AF%8F%E6%AC%A1%E6%9B%B4%E6%96%B0%E5%88%B0%E4%BB%93%E5%BA%93)

### 查看修改
使用 `git diff` 命令可以通过文件补丁的格式更加具体地显示哪些行为发生了改变。
要查看尚未暂存的文件更新了哪些部分，直接输入 `git diff` ；若要查看已暂存，将要添加到下次提交里的内容，使用 `git diff --staged` 命令。这条指令将比对已暂存文件和最后一次提交的文件的差异。

`git diff` 显示尚未暂存的改动， `git diff --staged` 显示暂存后即将提交的改动。

### 提交更新

我们知道 `git add` 命令可以将本地修改后的文件添加到暂存区，而 `git commit` 命令则将暂存区的文件提交到本地库。也就是说只会提交已暂存的修改，而未暂存的修改不会被提交。

直接输入 `git commit` 命令会启动文本编辑器来输入提交说明。可以下列指令指定使用vim编写说明。
```shell
git config --global core.editor vim
```
但是跟推荐的方法的是在 `git commit` 命令后添加 `-m` 选项，在命令行编写提交说明，比如说：
```shell
git commit -m "这是提交说明"
```

### 跳过暂存区直接提交

如果每次提交都需要将文件添加到暂存区，有些时候会显得过于繁琐。我们可以使用 `git commit -a` 命令。Git会自动把所有**已经跟踪过的文件**暂存以后一并提交。从而不值得注意的是，只有已经跟踪过的文件才会被自动暂存 。

```shell
git commit -a -m "已经跟踪过的文件自动"
```

### 移除文件

1.  `git rm --cached <file>` 仅将文件从Git仓库中移除，即不再纳入版本管理。但是不会删除原文件。
2.  `git rm -f <file>` 不仅从Git仓库中移除，同时删除原文件。

### 移动文件

`git mv` 命令主要用来**重命名**以及**移动文件**（从文件绝对路径的视角来看，移动文件路径等价于重命名）。

```shell
git mv file_from file_to
```

等价于

```shell
mv file_from
git rm file_from
git add file_to
```

### 查看提交历史

在提交了若干更新，又或者克隆了某个项目以后，想要回顾提交历史，可以使用 `git log` 命令。如果不传入任何参数， `git log` 会按时间顺序列出所有提交，最新的提交拍在上面。`git log` 有许多不同参数，可以定制化输出的内容及格式。当提交历史很多时，选择正确的参数可以极大的提高效率。本文仅介绍最近本的参数，更多介绍详见[Git - 查看提交历史](https://git-scm.com/book/zh/v2/Git-基础-查看提交历史)

1. 使用 `-p` 或 `--patch` 参数，它会以补丁的形式显示每次提交引入的变化。
2. 使用 `-2` 仅查看最近两次的提交。要查看替他次数的提交也同理。
3. `--stat` 选项在每次提交的下面列出所有被修改过的文件、有多少文件被修改了、以及被修改的文件哪些行被移除或添加。是对每次提交的总结。
4. `git --pretty` 可以使用不同于默认的格式的方式展示提交历史。详见[Git - 查看提交历史](https://git-scm.com/book/zh/v2/Git-基础-查看提交历史)

### 撤销操作

在任何一个阶段，我们都可能想要撤销某些操作。特别注意的时，**有些撤销操作时不可逆的**。错误的撤销可能导致之前的工作丢失。

1. 有时候我们提交完发现漏掉了几个文件没有添加，或者提交信息填写错误。使用下列命令重新提交。

   ```shell
   git commit --amend
   ```

   































