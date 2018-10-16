---
title: Git常用指令
date: 2018-10-16 14:55:38
tags:
- GitHub
categories: 
- GitHub
---

# 前言


# 常用指令
1、撤销本地修改
> git checkout . //撤销所有已修改但未提交的文件的修改，但不包括新增的文件

> git checkout [filename] //对指定文件

2、回退项目版本

*对已经add/commit但未push的文件也适用*
>git reset --hard [commit-hashcode] //hashcode可以用git log查看

**注：新增文件还没加到git的记录里面，及属于未tracked的状态，所以撤销修改和回退均对其不影响，可以手动清除 也可以用git clean**

3、从工作目录中移除没有track的文件

>git clean -df  //说明：git clean 从工作目录中移除没有被track的文件，通常始用的参数是-df：d表示同时移除目录，-f表示force，因为在git配置文件中，clean.requireForce=true,如果不加-f，clean将会拒绝执行

4、取消已经暂存的文件，即撤销先前“git add”的操作
>git reset HEAD^

>git reset HEAD <file>

>git reset hash  //回退到某个版本

# Git冲突解决常用指令
1、强制推送本地替换服务器端
>git push -f

2、服务器端完全覆盖本地工作版本
>git fetch --all
>git reset --hard origin/master
>git pull











