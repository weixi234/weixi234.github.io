---
title: win双系统ubuntu的卸载及安装
date: 2018-10-24 10:15:50
tags:
- Ubuntu
- 双系统
categories: 
- Ubuntu
---
# 前言
我之前装过ubuntu18用过一段时间，但是我笔记本硬件显卡是630M（每次提到这个就想摔电脑😭）配置CUDA没成功，Ubuntu18资料暂时还少，也不想对g++降级，折腾的我也只能卸载ubuntu18安装Ubuntu16了。  
先说明：装好双系统后我电脑有两个引导，一个是Ubuntu那个紫色界面的引导，另一个是我安装win系统时遗留的引导。  
电脑上折腾过很多，每次都没记录下来，时间长了就容易忘，现在我开始用GitHub慢慢记录整理了。  
欢迎加群808760743和我一起学习一起交流吧。  
# 卸载Ubuntu18
## 删除Ubuntu引导
首先感谢这篇博文[Ubuntu/Win10双系统安全删除Ubuntu的方法](https://blog.csdn.net/Meditator_hkx/article/details/52626077?utm_source=blogxgwz0)简介易懂。  
step1：下载MbrFix.exe，解压  
需要注意的是，若电脑是32位系统用MbrFix.exe,64位系统用MbrFix64.exe.  
step2：(我是64位win7)右键MbrFix64.exe，属性->兼容性->勾选以管理员身份运行  
step3：win+r，cmd，切换路劲至MbrFix64.exe存放文件夹，输入命令
>MbrFix /drive 0 fixmbr  
系统响应you are about to Fix MBR,are you sure后输入  Y

至此就成功了，可以重启试试。  
若出现Function failed Error 5 访问拒绝，则是没勾选管理员身份运行。 

至于安装win7过程中遗留下来的引导，直接用EasyBCD 2.3就可以了。  
编辑引导菜单->选中未命名 删除->跳过启动菜单->保存设置->重启即可  
判断系统启动方式[windows、ubuntu双系统正确卸载ubuntu系统](http://www.cnblogs.com/xia-Autumn/p/6294055.html)
## 删除Ubuntu分区
计算机右键->管理->存储->磁盘管理  
当然也可以用一些工具如DiskGenius（请先格式化再调整分区😭速度快一些😭）  
根据自己的情况酌情处理数据吧。（数据无价，谨慎处理）  

