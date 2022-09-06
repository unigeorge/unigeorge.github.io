---
layout: note
title: Git
categories: [Git]
description: Git 知识点
keywords: Git
---



## 常用命令

除了基本的拉取、提交等命令外，Git 还有更多样的命令来实现各种功能，熟练掌握才能更游刃有余。目前主要参考 [Git 常用命令大全](https://www.runoob.com/note/56524)。

1. 暂存
    
    ```
    git stash   #将未添加的修改暂存入栈
    git pull    #执行操作，也可以是其他操作，栈内的修改不会影响
    git stash pop     #将之前的修改出栈
    ```

2. 清除 commit 记录

    ```
    #适用于个人开发项目，多人合作项目慎用
    git checkout --orphan new_branch    #基于当前分支创建无 commit 记录的新分支
    git add .
    git commit -m'clear commits'
    git branch -D master                #删除本地 master 分支
    git branch -m master                #将新分支重命名为 master
    git push -f origin master           #强制推送远程
    ```