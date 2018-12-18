---
title: Ubuntu从入门到精通——1 win双系统ubuntu的卸载及安装
date: 2018-12-18 10:15:50
tags:
- Ubuntu
- 双系统
categories: 
- Ubuntu
---
# 前言1
我之前装过ubuntu18用过一段时间，但是我笔记本硬件显卡是630M（每次提到这个就想摔电脑😭）配置CUDA没成功，Ubuntu18资料暂时还少，也不想对g++降级，折腾的我也只能卸载ubuntu18安装Ubuntu16了。  
先说明：装好双系统后我电脑有两个引导，一个是Ubuntu那个紫色界面的引导，另一个是我安装win系统时遗留的引导。  
电脑上折腾过很多，每次都没记录下来，时间长了就容易忘，现在我开始用GitHub慢慢记录整理了。  
欢迎加群808760743和我一起学习一起交流吧。
# 前言2
现在我自己配置了台GTX1070Ti，i5-8500的迷你台式机，现在我又重新开始配置了😭之前是win7+ubuntu16，现在我的台式是win10+Ubuntu18，发现有些许差别，主要在于启动引导部分。我现在也想把我之前写的几篇Ubuntu配置博文综合到这篇文章了，这样，当我再有新机的时候，照着这个博文做就行了。修改此博文名，成为一个系列。(20181218)

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
根据自己的情况酌情处理数据吧。（数据无价，谨慎处理)   

# 安装Ubuntu双系统
## 准备
### 硬盘准备
计算机右键->管理->存储->磁盘管理->右键->"删除卷"  
在windows系统下删除分配给ubuntu的硬盘，在安装Ubuntu的时候方便区别其他盘  
### 制作Ubuntu启动盘
推荐大家看[ubuntu官方指导](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-windows#1)
官方推荐rufus工具，选择镜像···  
可参考[博文双系统安装详细教程](https://blog.csdn.net/flyyufenfei/article/details/79187656)
### 安装Ubuntu
1、我是华硕笔记本F2进入BIOS->Boot->Hard Drive BBS Priorities(时间长了我总忘了这个)->Boot Option #1       

2、微星B360M主板，开机按住F11->选择U盘启动    
进入BIOS：开机按Delete->启动选项是左右拖动图标完场的   

>（原先win7+Ubuntu）安装过程中：   
其他选项->**空闲**磁盘分区（我分配了100G）：  
/boot   300M    逻辑分区    空间起始位置    Ext4  
swap    8G      逻辑分区    空间起始位置    交换空间  
/       40G     逻辑分区    空间起始位置    Ext4  
/home   剩下    逻辑分区    空间起始位置    Ext4

**修改安装启动引导器的设备：/boot对应的分区** 

安装完场切记f2切换引导启动，拔出U盘  

由于修改了默认启动引导位置，所以启动后并不会有Ubuntu启动引导  

>**当前win10+ubuntu18安装问题及解决：**   1、**提示：Ubuntu安装提示“无法将grub-efi-amd64-signed软件包安装到/target”**  
win10支持UEFI安装，但是现存（Win7）系统非UEFI引导,主要是这个UEFI引导问题，具体我也不知道UEFI是什么😵这个后期如果有机会还要再研究。  
**我当前的做法是选择非UEFIU盘启动项启动的。**  
2、我当前的做法是从非UEFI启动盘启动安装的->然后使用EasyBCD从win10中添加引导。   
**疑惑**:①网上有些说如果电脑boot支持UEFI一定用UEFI安装，win8之后都支持UEFI，那为什么我安装时会出错😵  
②我的挂载点选的同之前一样是/boot，同学说win10选的是Windows boot manager,我也不知道选啥🤔···  

3、现在我进入Ubuntu的流程是：  
开机->BIOS自检->win10引导选择系统项-选择Ubuntu->BIOS自检->ubuntu引导选择系统项（GRUB）->选择Ubuntu->进入Ubuntu系统  
我表示有些奇怪🧐    

### 修改启动引导
EasyBCD->添加新条目->操作系统选Linux/BSD->类型选GRUB 2->名称Ubuntu16->驱动器选自己选的/boot分区->添加条目  
EasyBCD->编辑引导菜单->倒计时->保存设置

# ubuntu初步配置 
## ubuntu换源  
step1：编辑/etc/apt/sources.list文件  
>sudo gedit /ect/apt/sources.list  
//源地址：  https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/  

step2：更新
>sudo apt-get update  
>sudo apt-get upgrade  
## win下远程操作Ubuntu方式  
1、通过TeamView方式（有网就行）  
注意：  
①Ubuntu版本的TeamView14不支持远程传送文件   
②新安装的Ubuntu18要经过上述更换源和软件更新后才能安装TeamView成功，否则会安装失败   
2、Ubuntu和Window系统之间进行文件互传（同一局域网下）  
step1：现在Ubuntu下共享文件夹，在某一文件夹右键->配置文件共享属性  
如：下载文件夹->右键->本地网络共享->共享此目录->(共享名和注释可选填)->勾上允许其他人来创建和删除这个文件夹里的文件->勾上允许匿名登录（对于没有账号的用户）   
step2：在win文件管理器地址栏输入对应Ubuntu系统的IP地址回车即可看到共享的文件夹。  
如：\\192.168.1.129  
参考链接：[Ubuntu和Windows之间进行远程访问和文件互传](https://zhuanlan.zhihu.com/p/39522612)   
## 更改文件夹及其子文件权限  
打开终端进入需要修改的目录
>sudo su  
>chmod 777 文件夹名 -R  
>chmod 777 文件名  
## 科学上网  
1、我用的是Shadowsocks-Qt5.AppImage   
2、导入gui-config.json配置文件  
3、启动Shadowsocks-Qt5.AppImage并连接  
**4、Ubuntu系统设置->网络->网络代理->手动->Socks主机->127.0.0.1->1080(忽略主机不动，http代理端口置为0)**  
**chrome上需要设置上述代理，否则不能科学上网**








