---
title: Git常用指令
date: 2018-10-16 14:55:38
tags:
- GitHub
categories: 
- GitHub
---

# 前言
一年前就想用git进行代码和文档的管理，也一直想用git搭建自己的Blog，当时功力还不够，从入门到放弃了。中途断断续续的研究-放弃了几次，现在才算懂了些道道，能上手用了。开心~  
# 常用指令
## 撤销本地修改
> git checkout . //撤销所有已修改但未提交的文件的修改，但不包括新增的文件

> git checkout [filename] //对指定文件

## 回退项目版本  
*对已经add/commit但未push的文件也适用*
>git reset --hard [commit-hashcode] //hashcode可以用git log查看  

**注：新增文件还没加到git的记录里面，及属于未tracked的状态，所以撤销修改和回退均对其不影响，可以手动清除 也可以用git clean**

## 从工作目录中移除没有track的文件  
>git clean -df  //说明：git clean 从工作目录中移除没有被track的文件，通常始用的参数是-df：d表示同时移除目录，-f表示force，因为在git配置文件中，clean.requireForce=true,如果不加-f，clean将会拒绝执行

## 取消已经暂存的文件，即撤销先前“git add”的操作
>git reset HEAD^

>git reset HEAD <file>

>git reset hash  //回退到某个版本

# Git冲突解决常用指令
## 强制推送本地替换服务器端
>git push -f

## 服务器端完全覆盖本地工作版本
>git fetch --all
>git reset --hard origin/master
>git pull

# 多终端同步Hexo命令  
## 日常使用  
**每个平台下都要切换到src分支下**  
- **获取更新**
>git pull  
- 开始写博文  
>hexo new "XXX"
- 博文结束  
>hexo clean  
>hexo g(generate)    
>hexo d(deploy)    
- 同步到master分支  
>git add .  
>git commit -m "XXX"  
>git push origin src  

## 新建博文字段 
1、visible: hide 这里如果加上hide则该文章就不会在文章首页显示，如果留空则表示默认显示    
2、categories:   
3、hexo-blog-encrypt加密博文字段  
>password: 是该博客加密使用的密码            
>abstract: 是该博客的摘要，会显示在博客的列表页       
>message: 这个是博客查看时，密码输入框上面的描述性文字       









