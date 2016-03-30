---
title: Git使用笔记
date: 2016-03-30 13:08:52
category: 编程工具
tags: Git
---

用了一段时间的TortoisGit，基本能满足目前的需求。但是当切换到linux的工作环境时，就有点不知所措了，于是又复习了一下git的命令，相对于第一次学习，又有了新的认识。

# 1.关于工作区，暂存区，版本库
工作区（Working Tree）:所有执行过`git add filename`的文件都存在于工作区。
暂存区（Index）:对于已经存在于工作区的文件，执行`git stage filename`后，文件会保存到暂存区中。
版本库：执行`git commit` 后会将暂存区中的修改提交到版本库。

# 2.git add 和 git stage
在学习过程中因为没搞清`git add`的作用导致担误了不少时间，最后发现git 的 add命令本就是一个不直观的命令，它有两个不同的作用：
**1. 用于添加跟踪一个文件。**
**2.用于将工作区的文件添加到暂存区。**

于是git 的后续版本中添加了`git stage`代替`git add`的第2个作用，但为了保持和以前的兼容，git add还保持着之前的用法，在很多教程中都没用使用`git stage`，导致不利于理解。同时还添加了git diff --staged替代git diff --cached。

# 3.git diff的用法
一次commit 之后，执行`git stage filename` 添到暂存区之后，再进行一些修改，此时执行`git diff` ，并添加不同的参数，会有以下区别：
**1.git diff 显示当前工作区的文件和暂存区文件的差异**
**2.git diff --staged 显示暂存区和HEAD的文件的差异**
**3.git diff HEAD 显示工作区和上次提交文件的差异**

# 4.git checkout 与 git reset
`git reset filename`与`git stage filename`的作用是相反的，用于将文件从暂存区删除，不会修改工作区文件。而`git checkout filename` 则是回退到上一次stage时的状态。

# 5.关于hunk
hunk代表一个diff中一段连续的改动，git stage 就可以通过添加 -p选项以hunk为单位添加到暂存区。该功能可以用来在开发一个功能时，突然需要修复一个之前提交中bug，修改完成后添加到暂存区，先提交修复的bug，然后再提交当前实现的功能。

# 6.直接提交
使用git commit -am " "  直接提交工作区。


持续更新......