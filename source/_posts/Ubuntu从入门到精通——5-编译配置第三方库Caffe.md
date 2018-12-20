---
title: Ubuntu从入门到精通——5 编译配置第三方库Caffe
date: 2018-12-20 19:48:34
tags:
- Ubuntu 
- Caffe
- GPU
categories: 
- Ubuntu
---

# 前言
我之前跑过CPU版的Caffe，现在配置了1070Ti的迷你台式，现在开始尝试配置GPU开发环境。

# Caffe配置准备  
## Caffe相关连接
[Caffe官方网站](http://caffe.berkeleyvision.org/)  
[Caffe安装说明界面](http://caffe.berkeleyvision.org/install_apt.html)  
[配置参考——在Ubuntu17.10上使用CPU运行caffe的SSD](https://blog.csdn.net/rockwzy/article/details/79155566)

## 安装Caffe依赖  
[Caffe官方安装指导](http://caffe.berkeleyvision.org/installation.html#compilation)   
1、官网安装指导  
对于>17.04版本的Ubuntu，可以通过一行命令安装依赖项：  
>sudo apt build-dep caffe-cpu        # dependencies for CPU-only version
>sudo apt build-dep caffe-cuda       # dependencies for CUDA version

它需要deb-src。即sources.list中的#deb-src注释项去掉。   
>sudo gedit /ect/apt/sources.list   

>sudo apt-get update  
>sudo apt-get upgrade   

2、网络参考安装依赖
非常不建议使用源码编译安装Caffe的依赖，因为这过程中可能会出现新的依赖问题。caffe官网推荐采用apt获取编译所需的依赖。   
>sudo apt build-dep caffe-cpu   

对于Ubuntu17.04及以上版本，官方提供了caffe的源码编译环境，但是实际上在后面编译的时候可能会提示并未找到某些依赖，如果出现找不到依赖的情况，根据报错补装。  
编译caffe所需要的所有依赖:   
- 基本依赖 
>sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler    
>sudo apt-get install --no-install-recommends libboost-all-dev    

- CUDA高性能并行计算库：我们安装的是CPU版本，不存在CUDA
BLAS线性代数库，有两种开源线性代数库可供选择：   
>atlas sudo apt-get install libatlas-base-dev   
>openblas sudo apt-get install libopenblas-dev   

- 构建caffe的Python接口pycaffe所需的python-dev包：   
>sudo apt-get install the python-dev   

- 对于Ubuntu14.04版本，还需要安装以下依赖：    
>sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev   

综合：  
网上这个配置依赖是配置CPU的，我想配置的是GPU的。我首先是根据第二点进行配置，然后根据第一点，取消sources.list中的注释，（先运行了sudo apt build-dep caffe-cpu然后sudo apt build-dep caffe-cuda）根据官网安装说明进行配置的。  

# 下载Caffe源码  
## 相关连接  
[官方Caffe源码](https://github.com/BVLC/caffe)       
[SSD版本Caffe？](https://github.com/weiliu89/caffe)     

## 下载Caffe源码
caffe是开源项目，所有人都能fork出一个分支，一千个人就有一千个版本。SSD实现的比较好的是这个版本https://github.com/weiliu89/caffe，点击Clone or Download按钮获取源码，推荐使用Download下来再解压。   

# 编译Caffe
1、官方编译说明   
[官方从源码安装编译](http://caffe.berkeleyvision.org/installation.html#compilation)    



2、网上编译说明   
~~git checkout ssd 切换到ssd分支~~  
>cp Makefile.config.example Makefile.config  

复制caffe提供的Makefile.config样例。然后修改Makefile.config：    
- line 8，取消CPU_ONLY字段的注释
- line 50，如果安装的是openblas，则将值atlas改为open
line 94，在INCLUDE_DIRS字段后面添加/usr/include/hdf5/serial
line 107，取消USE_PKG_CONFIG字段的注释，否则可能会报错opencv的相关错误
修改Makefile： 
line 181，将hdf5_hl hdf5替换成hdf5_serial_hl hdf5_serial

make -j 编译caffe 
可能会出现如下报错： 
/usr/bin/ld: cannot find -lboost_regex 
/usr/bin/ld: cannot find -lopenblas 
原因是boost库和openblas没安装好，参考上文重装依赖。
make pycaffe 编译caffe的Python接口 
可能会出现如下报错： 
fatal error: numpy/arrayobject.h: No such file or directory 
原因是numpy模块没安装好，sudo apt-get install python-numpy安装numpy。






