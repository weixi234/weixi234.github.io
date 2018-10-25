﻿---
title: Ubuntu16配置CUDA跑模型
date: 2018-10-15 16:48:55
updated: 2018-10-25 13:42:33
tags:
- CUDA
- ubuntu18
- 深度学习
- LINUX
categories:
- 深度学习
---

# 前言
很早之前就装好Ubuntu双系统，在上面学过视觉SLEAM十四讲、CLion、CMAKE语法，编写过openCV、PCL、caffeCPU版等等，后来重装了Ubuntu18，前几天用Ubuntu18配置GPU版CUDA打算跑跑深度学习，一直没配置好，现在删除了Ubuntu18重新装回Ubuntu16。  
# Ubuntu16.04无法直接安装deb包问题  
>sudo apt-get install gdebi

要安装的包右键->打开方式->gdebi->安装软件包  
# Ubuntu16.04无法获得锁问题
方法一:（无效，但记录一下）
>ps -e|grep apt  
sudo kill ID  

方法二:（粗暴有效）  
无法获得锁/var/lib/dpkg/lock-open  
>sudo rm /var/cache/apt/archives/lock  
>sudo rm /var/lib/dpkg/lock  

无法获得锁/var/lib/apt/lists/lock-open  
>sudo dpkg --configure -a  
sudo rm /var/lib/apt/lists/lock（有效）   

# ubuntu换源#
step1：编辑/etc/apt/sources.list文件  
>sudo gedit /ect/apt/sources.list  
//源地址：https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/  

step2：更新
>sudo apt-get update  
>sudo apt-get upgrade  

# 安装nvidia显卡驱动
显卡驱动可参考[如何安装NVIDIA显卡驱动](https://linuxconfig.org/how-to-inatall-the-nvidia-drivers-on-ubuntu-18-04-bionic-beaver-linux#h8-manual-install-using-the-official-nvidia-com-driver)  
step1：查看显卡设备和驱动
>ubuntu-drivers devices

step2：自动安装合适的显卡驱动
>sudo ubuntu-drivers autoinstall  

安装完成后要重启生效  
**衡量nvidia显卡驱动安装是否成功**
1、终端中确认
>nvidia-smi

2、设置->详细信息->About
3、软件中搜索‘nvidia setting’->Prime profiles

# nvidia驱动与CUDA TOOKit工具对应关系  
![驱动与CUDA对应关系](驱动版本与CUDA工具对应关系.png)  
给出参考网址[CUDA RELEASE NOTES](https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html)  
由于我电脑nvidia-smi显示的是384.130，对照表所以我只能选Cuda9.0.76版本。  
# CUDA官网查询CUDA Toolkit相关信息
英伟达官方文档[CUDA Toolkit Archive](https://developer.nvidia.com/cuda-toolkit-archive)对应工具版本后面的Online Documentation中的安装指南推荐看看，网上的中文教程应该就是按照这个来的。

CUDA Toolkit 包括：NVCC编译器、CUDA函数头文件及库文件，及一些辅助库  
CUDA Toolkit SDK 提供CUDA分析接口函数（帮助用户分析CUDA程序）  
CUDA Toolkit获取[CUDA Toolkit Archive](https://developer.nvidia.com/cuda-toolkit-archive)

CUDA Archive获取[CUDA cuDNN Archive](https://developer.nvidia.com/rdp/cudnn-archive)

注意CUDA cuDNN与CUDA Toolkit 之间的对应关系

# 安装CUDA Toolkit
参照对应版本的说明文档即可
>sudo sh cuda_10.0.130_410.48_linux.run

补丁也按照上述命令安装  
**注意**：出现 更多  点q  accept  驱动不装 其余yes

**配置环境**
step1:打开profile文件  
>sudo gedit  /etc/profile  

step2:打开文件后在文件末尾添加路径(安装目录)  
>export PATH=/usr/local/cuda-9.0/bin:\$PATH(\转义)    
>export  LD_LIBRARY_PATH=/usr/local/cuda-9.0/lib64$LD_LIBRARY_PATH  
>sudo ldconfig//重启生效

step3:
>sudo gedit ~/.barshrc

添加  
>export PATH=/usr/local/cuda-10.0/bin$\{PATH:+:${PATH}}  
export LD_LIBRARY_PATH=/usr/local/cuda-10.0/lib64$\{LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}  

>source /etc/profile//使修改立即生效

**阅读安装完的summary也可知道**

~~**重启**~~  
~~>sudo reboot ~~  

**检查cuda是否安装成功**

1、检测路径~/dev下有无存在名为nvidia*的多个文件，若无安装出错
>ls /dev | grep nvidia*

2、检查CUDA Toolkit是否安装成功
>nvcc -V（大V）

3、编译samples例子

进入Samples安装目录(就是家目录)，然后在该目录下
>make -j4

在bin/x86_64/linux/release/运行deviceQuery程序
 >sudo ./deviceQuery

Pass表示通过
>sudo ./bandwidthTest

Pass表示通过 

# 安装cuDNN  
**应该看官方说明文档，我偷懒了**
查询显卡计算能力[CUDA-GPU](https://deverloper.nvidia.com/cuda-gpus)  
安装方法应该是先查询自己电脑的计算能力，然后确定cudnn版本，然后确定对应的cuda版本  

解压cudnn压缩包，执行或参考下面命令
>sudo cp cuda/include/cudnn.h  /usr/local/include
>sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
>sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*

**验证cuDnn安装成功否**
cudnn官方说明文档(cuDNN Developer Guide)中指出cudnn加速要求计算能力在3.0以上，所以我的凉了~  
官方文档(cuDnn inatall Guide)指出了deb方式下的验证方式，直接照着做就行，现我说明Ubuntu下的验证方式：  
下载mnistCUDNN[cudnn_samples_v7](https://github.com/weixi234/cudnn_samples_v7)  
>make clean && make  
./mnistCUDNN  

**移动cuDnn库文件后可能找不到libcudart.so.9.0**  
- 确认路径[错误参考1](https://blog.csdn.net/u010454261/article/details/71268325)  
- 确认路径[错误参考2](https://github.com/BVLC/caffe/issues/4944)     
- 确认路径[错误参考3](https://github.com/KlausT/ccminer/issues/149)  
- 确认路径[错误参考4](www.cs.virginia.edu/~mwb7w/cuda_support/libcudart.html)















