# Git 简介和基础操作

## 简介

Git，是Linux开发者Linus开发的分布式版本控制系统（distributed revision control system）。版本控制系统是能够随着时间的推进记录一系列文件的变化以便于你以后想要的退回到某个版本的系统。版本控制系统分为三大类：本地版本控制系统，集中式版本控制系统和分布式版本控制系统。

## Git 基础

不同于其他以文件变更列表的方式存储信息的系统（通常被称作基于差异（delta-based）的版本控制），在 Git 中，每当提交更新或保存项目状态是，它会对当时的全部文件创建一个快照并保存这个快照的索引。  
Git工程有三个工作区域：工作目录，暂存区域，以及本地仓库。工作目录是你当前进行工作的区域；暂存区域是你运行`git add`命令后文件保存的区域，也是下次提交将要保存的文件；本地仓库就是版本库，记录了你工程某次提交的完整状态和内容，这意味着你的数据永远不会丢失。

文件也有三种状态：已提交（committed），已修改（modified）和已暂存（staged）。已提交表示该文件已经被安全地保存在本地版本库中了；已修改表示修改了某个文件，但还没有提交保存；已暂存表示把已修改的文件放在下次提交时要保存的清单中，即暂存区域。  
基本的 Git 工作流程如下：

- 在工作区中修改文件。
- 将你想要下次提交的更改选择性地暂存，这样只会将更改的部分添加到暂存区。
- 提交更新，找到暂存区的文件，将快照永久性存储到 Git 目录。

## Git 常用命令

- 获取 Git 仓库

有以下两种方式：

1. 在已存在目录中初始化仓库

```git
git init
```

该命令将创建一个名为 `.git` 的子目录，这个子目录含有你初始化的 Git 仓库中所有的必须文件。
2. 克隆现有的仓库

```git
git clone <url>
```

比如，要克隆 Git 的链接库 libgit2，可以用下面的命令：

```git
git clone https://github.com/libgit2/libgit2
```

- 检查当前文件状态

查看仓库下文件处于什么状态。

```git
git status
```

- 跟踪新文件

```git
git add <filename>
```

- 提交更新

```git
git commit
```

- 重新提交

```git
git commit --amend
```

- 查看提交历史

```git
git log
```

- 取消暂存区的文件

```git
git reset HEAD <filename>
```

- 撤消对文件的修改

```git
git checkout -- <filename>
```

- 查看远程仓库

```git
git remote -v
```

将远程仓库全部列出

```git
cd grit
git remote -v
```

- 自动抓取后合并该远程分支到当前分支

```git
git pull
```

- 推送到远程仓库

```git
git push origin master
```

- 获取帮助

若你使用 Git 时需要获取帮助，有三种等价的方法可以找到 Git 命令的综合手册（manpage）：

```git
git help <verb>
git <verb> --help
man git-<verb>
```

例如，要想获得 git config 命令的手册，执行

```git
git help config
```
