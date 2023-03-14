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

![1240](https://cdn.jsdelivr.net/gh/smart-huang/bolgImage@main/img/1240.png)

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

#### 重新提交

有时候我们提交完发现漏掉了几个文件没有添加，或者提交信息填写错误。使用下列命令重新提交。

```shell
git commit --amend
```

需要说明的时，这是一个提交命令。此次提交是对上次提交的修改，不会产生新的提交记录。这样做的好处是不会让”忘记添加一个文件“或”小修补，修正笔误“之类的提交信息污染仓库历史。

#### 取消暂存文件

当暂存区有文件等待提交时，我们使用 `git status` 命令可以得到以下说明：

```shell
$ git status
On branch main
Changes to be commited:
	(use "git reset HEAD <file>..." to unstage)
	
	modified: CONTRIBUTING.md
```

提示我们使用 `git reset HEAD` 来取消暂存。

值得注意的时，笔者在自己电脑上运行的时候提示使用 `git restore --staged <file>` 命令。经过测试，两个命令都有效。

#### 撤销对文件的修改

如果不想保留对文件的修改，可以将文件还原成上次提交的状态。使用 `git checkout -- <file>...` 或者 `git restore <file>...` 命令。

值得注意的时，这两个命令**十分危险**。如果文件修改以后未提交，不小心撤销以后，修改的内容是不能找回的。请务必**谨慎使用**。

### 远程仓库的使用

为了能在任意Git项目上协作，需要知道如何管理自己的远程仓库。远程仓库是指托管在因特网或其他网路中的你的项目的版本库。

#### 查看远程仓库

如果你想查看你已经配置的远程仓库服务器，可以使用 `git remote` 命令。它会列出你指定的每一个远程仓库的简写。如果你克隆了一个仓库，你会看到origin ——Git给你克隆的仓库服务器的默认名字。

你也可以使用 `git remote -v` 命令，它会显示需要读写远程仓库的url及Git命令：

```shell
$ git remote -v
origin  https://github.com/smart-huang/MyNote.git (fetch)
origin  https://github.com/smart-huang/MyNote.git (push)
```

#### 添加远程仓库

可以使用 `git clone <url>` 命令添加远程仓库，此时Git会自动给远程仓库一个简称——origin。

我们还可以通过 `git remote add <shortname> <url>` 命令手动指定简称。

#### 从远程仓库抓取和拉取

从远程仓库中获取信息可以使用 `git fetch <shortname>` ，这个命令会访问远程仓库，从中抓取你没有的数据。必须注意，这个命令只会将数据下载到你的本地仓库，但是不会自动合并或修改你当前的工作。需要你手动合并。

如果你当前的本地分支设置跟踪远程分支，那么使用 `git pull` 命令会自动抓取后合并到本地分支。默认情况下，`git clone` 命令会自动设置本地main分支跟踪远程仓库的min分支。运行 `git pull` 命令通常会从最初克隆的服务器上抓取数据并自动尝试合并到当前所在的分支。

#### 推送到远程仓库

使用 `git push <remote> <branch>` 命令将本地仓库的内容推送到远程仓库。基本上以下命令可以满足要求：

```shell
git push origin main
```

这条指令生效需要满足两个条件，你有远程仓库的写入权限，并且**在你之前没有别人推送过**。如果别人在你之前已经推送过，并且你没有拉取改动，那么你的推动会被拒绝。你必须先抓取他们的工作并将其合并进你的工作后才能推动。

#### 查看某个远程仓库

使用 `git remote show <remote>` 命令，查看远程仓库的信息。

```shell
$ git remote show origin
* remote origin
  Fetch URL: https://github.com/smart-huang/MyNote.git
  Push  URL: https://github.com/smart-huang/MyNote.git
  HEAD branch: main
  Remote branches:
    main                       tracked
    refs/remotes/origin/master stale (use 'git remote prune' to remove)
  Local branch configured for 'git pull':
    main merges with remote main
  Local ref configured for 'git push':
    main pushes to main (up to date)
```

它会列出远程仓库的URL以及跟踪分支的信息。这个命令会列出本地分支与远程分支的绑定关系，以及远程分支的状态。

#### 远程仓库的重命名与移除

`git remote rename <old_name> <new_name>` 来给远程仓库重命名。

`git remote remove <remote>` 移除特定的远程仓库。

### 打标签

就像其他版本控制系统（VCS）一样，Git可以给仓库历史中的某一个提交打上标签，以示重要。比较有代表性的是人们使用这个功能来标记发布的版本信息（v1.0、v2.0等等）。

#### 列出标签

输入 `git tag` 命令可以查看所有标签。这个命令以**字母顺序**列出标签，但是他们心事的顺序并不重要。

还可以按照特定的模式查找标签。比如说仓库中的代码数量特别多，而我们只对1.8.5系列感兴趣，就可以：
```shell
git tag -l "v1.8.5*"
```

#### 创建标签

Git支持两种标签：轻量标签（lightweight）与附注标签（annotated）。

1. 附注标签是一个完整的对象。它可以被校验，包含打标签者的名字、邮箱地址、日期时间等信息。通常建议创建附注标签。

   ```shell
   git tag -a v1.0 -m "标签的说明"
   ```

2. 轻量标签

   ```
   git tag v1.0-lw
   ```

使用 `git show` 命令可以查看具体标签的信息。

```
git show v1.0-lw
```

Git的标签还有很多使用方法，比如给已经提交过的历史打标签、共享标签、删除标签等。具体内容请查阅[Git - 打标签 ](https://git-scm.com/book/zh/v2/Git-基础-打标签)。



## Git分支

Git的分支模型是它的“必杀技”，正是这一特性使它从众多版本控制系统中脱颖而出。在Git中创建新的分支几乎能在一瞬间完成，并且在不同分支之间的切换操作也是一样迅速而便捷。理解喝精通这一特性，你便会意识到Git的强大，并且从而真正改变你的开发方式。

本章仅介绍Git分支的使用，具体是原理和细节请查阅[Git - 分支简介](https://git-scm.com/book/zh/v2/Git-分支-分支简介)。

### 分支的创建与合并

#### 查看所有分支

使用 `git branch` 命令可以列出所有分支的名字，用星号标识的就是当前分支。

#### 新建分支

`git branch <branch_name>` 可以创建新的分支。

#### 切换分支

`git checkout <branch_name>` 可以切换到指定分支。必须是已经存在的分支。

`git checkout -b <new_branch>` 可以创建新的分支，并切换到该分支。这条命令等价于：

```shell
git branch <new_branch>
git checkout <new_branch>
```

#### 合并分支

当我们在分支上完成开发工作并测试成功后，可以将分支合并。

```shell
git checkout main
git merge sub
```

#### 删除分支

将sub分支与main分支合并以后，便不再需要sub了。可以从任务跟踪系统中关闭这项任务，并删除这个分支。

```shell
git branch -d sub
```

#### 遇到冲突时的分支合并

有的时候合并操作不会如此顺利。如果你在两个不同的分支中，对同一个文件的同一部分进行了不同的修改，Git就没办法干净的合并它们。当你提交时，Git会暂停下来，等待你解决合并产生的冲突。你可以在合并冲突后的任意时刻使用 `git status` 命令查看冲突所在的文件。

### 分支管理

我们已经知道使用 `git branch` 命令可以查看当前所有分支的列表。如果要查看每个分支的最后一次提交，可以使用 `git branch -v` 命令。

如果要查看哪些分支已经合并到当前分支，可以运行 `git branch --merged` ，已经合并的分支就可以放心删除而不担心会丢失任何内容。

查看所有包含未合并工作的分支，可以运行 `git branch --no-merged` 命令。删除未合并的分支时Git会提示删除失败。

### 分支开发工作流

有一些常见的利用分支进行开发的工作流程。 

#### 长期分支

![image-20230314094258576](https://cdn.jsdelivr.net/gh/smart-huang/bolgImage@main/img/image-20230314094258576.png)

对于一个庞大或者复杂的项目，可以拥有多个不同稳定性的分支。如下图所示，只在master分支上保留完全稳定的代码。在develop分支中，开发或测试稳定性。一旦达到稳定状态，便可以将其合并入master分支。

你可以用这个方法维护不同层次的稳定性，当一个分支具有一定程度的稳定性后，便可将其合并入更高级别稳定性的分支中。值得强调的是，我们不一定必须这样做。但是对于一个非常庞大或者复杂的项目而言，这样做通常是很有帮助的。

#### 主题分支



#### 远程分支





















