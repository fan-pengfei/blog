---
title: "Git学习记录"
date: 2021-08-18T04:29:14.000Z
draft: false
tags: ["Git"]
---
> Git相关的知识；
> 参考链接：[https://zhuanlan.zhihu.com/p/94008510](https://zhuanlan.zhihu.com/p/94008510)

## Git 常用命令

> 初次使用时，在命令行中配置本地仓库的账号和邮箱：

```bash
$ git config --global user.name "username"
$ git config --global user.email "useremail"
```

> 初始化Git,使用 cd 命令导航到要在终端中设置版本控制的目录，现在你可以像这样初始化 Git 存储库：

```bash
git init
```

> 要开始对现有文件进行版本控制，你应该先跟踪这些文件并进行初始提交。要做到这一点，你首先需要将文件添加到 Git 中，并将它们附加到 Git 项目中。

```bash
git add
git commit -m "first commit"
```

> 还有一些更高级的方法可以将文件添加到 Git 中，从而使你的工作流程更高效。我们可以执行以下操作，而不是试图查找所有有更改的文件并逐个添加它们：

```bash
# 逐个添加文件
git add filename
# 添加当前目录中的所有文件
git add -A
# 添加当前目录中的所有文件更改
git add .
# 选择要添加的更改（你可以 Y 或 N 完成所有更改）
git add -p
```

> 远程备份文件（Github）,因此，首先转到 [http://github.com](https://link.zhihu.com/?target=http%3A//github.com) 并创建一个存储库。然后，使用存储库的链接将其添加为本地 git 项目的来源，即该代码的存储位置；

```bash
git remote add origin \
https://github.com/fan-pengfei/bash_learning.git
```

> 远程备份代码：

```bash
git push origin master
```

> git status 命令用于确定哪些文件处于哪种状态，它使你可以查看哪些文件已提交，哪些文件尚未提交。如果在所有文件都已提交并推送后运行此命令，则应该看到类似以下内容：

```bash
$ git status
# On branch master
nothing to commit (working directory clean)
```

> 我们可以使用 `git commit -m '提交信息'` 来将文件提交到 Git。对于提交简短消息来说，这一切都很好，但是如果你想做一些更精细的事情，你需要来学习更多的操作:

```bash
### 提交暂存文件，通常用于较短的提交消息
git commit -m 'commit message'
### 添加文件并提交一次
git commit filename -m 'commit message'
### 添加文件并提交暂存文件
git commit -am 'insert commit message'
### 更改你的最新提交消息
git commit --amend 'new commit message'
# 将一系列提交合并为一个提交，你可能会用它来组织混乱的提交历史记录
git rebase -i
### 这将为你提供核心编辑器上的界面：
# Commands:
#  p, pick = use commit
#  r, reword = use commit, but edit the commit message
#  e, edit = use commit, but stop for amending
#  s, squash = use commit, but meld into previous commit
#  f, fixup = like "squash", but discard this commit's log message
#  x, exec = run command (the rest of the line) using shell
```

> GitHub存储库的master分支应始终包含有效且稳定的代码。但是，你可能还希望备份一些当前正在处理的代码，但这些代码并不完全稳定。也许你要添加一个新功能，你正在尝试和破坏很多代码，但是你仍然希望保留备份以保存进度！
> 分支使你可以在不影响master分支的情况下处理代码的单独副本。首次创建分支时，将以新名称创建master分支的完整克隆。然后，你可以独立地在此新分支中修改代码，包括提交文件等。一旦你的新功能已完全集成并且代码稳定，就可以将其合并到master分支中！

```bash
### 创建一个本地分支
git checkout -b branchname
### 在2个分支之间切换
git checkout prc/dev-wupx
git checkout master
### 将新的本地分支作为备份
git push -u origin branch_2
### 删除本地分支，这不会让你删除尚未合并的分支
git branch -d branch_2
### 删除本地分支，即使尚未合并，这也会删除该分支！
git branch -D branch_2
### Viewing all current branches for the repository, including both ### local and remote branches. Great to see if you already have a ### branch for a particular feature addition, especially on bigger ### projects
### 查看存储库的所有当前分支，包括本地和远程分支。
git branch -a
### 查看已合并到您当前分支中的所有分支，包括本地和远程。 非常适合查看所有代码的来源！
git branch -a --merged
### 查看尚未合并到当前分支中的所有分支，包括本地和远程
git branch -a --no-merged
### 查看所有本地分支
git branch
### 查看所有远程分支
git branch -r
# 将主分支重新设置为本地分支
$ git rebase origin/master
# 将分支推送到远程存储库源并对其进行跟踪
$ git push origin branchname
```

> 将新功能添加到分支中之后，你需要将其合并回master分支，以便您的master具有所有最新的代码功能。
> 方法如下：

```bash
### 首先确保你正在查看 master 分支
git checkout master
### 现在将你的分支合并到 master
git merge prc/dev-wupx
```

> 你可能必须修复分支与主服务器之间的任何代码冲突，但是 Git 将向你展示在键入该 merge 命令后如何执行所有这些操作。
> 当有错误发生时，Git 提供了你所需的一切，以防你在所推送的代码中犯错，改写某些内容或者只是想对所推送的内容进行更正。

```bash
### 切换到最新提交的代码版本
git reset HEAD
git reset HEAD -- filename # for a specific file
### 切换到最新提交之前的代码版本
git reset HEAD^ -- filename
git reset HEAD^ -- filename # for a specific file
### 切换回3或5次提交
git reset HEAD~3 -- filename
git reset HEAD~3 -- filename # for a specific file
git reset HEAD~5 -- filename
git reset HEAD~5 -- filename # for a specific file
### 切换回特定的提交，其中 0766c053 为提交 ID
git reset 0766c053 -- filename
git reset 0766c053 -- filename # for a specific file
### 先前的命令是所谓的软重置。 你的代码已重置，但是git仍会保留其他代码的副本，以备你需要时使用。 另一方面，--hard 标志告诉Git覆盖工作目录中的所有更改。
git reset --hard 0766c053
```

## 有用的技巧

### 搜索

```bash
### 搜索目录中的字符串部分
git grep 'project'
### 在目录中搜索部分字符串，-n 打印出 git 找到匹配项的行号
git grep -n 'project'
### git grep -C  'something' 搜索带有某些上下文的字符串部分（某些行在我们正在寻找的字符串之前和之后）
git grep -C 'project'
### 搜索字符串的一部分，并在字符串之前显示行
git grep -B 'project'
### 搜索字符串的一部分，并在字符串之后显示行
git grep -A 'something'
```

### 看谁写了什么

```bash
### 显示带有作者姓名的文件的更改历史记录
git blame 'filename'
### 显示带有作者姓名和 git commit ID 的文件的更改历史记录
git blame 'filename' -l
```

### 日志

```bash
### 显示存储库中所有提交的列表 该命令显示有关提交的所有信息，例如提交ID，作者，日期和提交消息
git log
### 提交列表仅显示提交消息和更改
git log -p
### 包含您要查找的特定字符串的提交列表
git log -S 'project'
### 作者提交的清单
git log --author 'wupx'
### 显示存储库中提交列表的摘要。显示提交ID和提交消息的较短版本。
git log --oneline
### 显示昨天以来仓库中的提交列表
git log --since=yesterday
### 显示作者日志，并在提交消息中搜索特定术语
git log --grep "project" --author "wupx"
```

### 每次提交不用重复输入账号和密码

> 参考链接：[https://zhuanlan.zhihu.com/p/81334170](https://zhuanlan.zhihu.com/p/81334170)

1、验证是否真的使用的是https方式

> 使用命令：

```bash
git remote -v
```

> 确定是https方式；

2、修改https的方式为ssh方式

> 移除当前关联的远程仓库

```bash
git remote rm origin
```

> 添加新的ssh地址

```bash
git remote add origin ssh地址
```

3、再次提交

```bash
git push origin master
```

> 这次没有再提示输入密码

## 报错解决办法

> 当出错：! [rejected] master -> master (fetch first) error: failed to push some refs to ‘ 。。。’

出现这个问题是因为github中的README.md文件不在本地代码目录中，可以通过如下命令进行代码合并

```bash
git pull --rebase origin master
```
