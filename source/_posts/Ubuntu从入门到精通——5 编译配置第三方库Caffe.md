---
title: Ubuntu从入门到精通——5 编译配置第三方库Caffe
date: 2018-12-21 11:48:34
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
~~[SSD版本Caffe？](https://github.com/weiliu89/caffe)~~     

## 下载Caffe源码
~~caffe是开源项目，所有人都能fork出一个分支，一千个人就有一千个版本。SSD实现的比较好的是这个版本https://github.com/weiliu89/caffe，点击Clone or Download按钮获取源码，推荐使用Download下来再解压。~~    
最后我还是下载官方的源码，如果下载分支源码，会出想错误，让你替换最新的caffe文件。     

# 编译Caffe
1、官方编译说明   
[官方从源码安装编译](http://caffe.berkeleyvision.org/installation.html#compilation)    

2、修改Makefile.config   
step1:复制caffe提供的Makefile.config.examples样例,然后修改Makefile.config：       
>cp Makefile.config.example Makefile.config  

step2:取消USE_CUDNN:=1的注释（并且最好前面不要空格）  

step3：注释CUDA_ARCH:=中的   
>\# For CUDA >= 9.0, comment the *_20 and *_21 lines for compatibility.     
>\CUDA_ARCH :=     
>\# -gencode arch=compute_20,code=sm_20 \   
>\#		-gencode arch=compute_20,code=sm_21 \   
step4:hdf5问题  
将这里替换为下面这样，即后面加上hdf5路径：       
>\# Whatever else you find you need goes here.   
>INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial      
>LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu /usr/lib/x86_64-linux-gnu/hdf5/serial/    

step5：对cv::imread(cv::String const&,int)未定义的引用问题   
是因为用的OpenCV3,去掉注释：     
>\# Uncomment if you're using OpenCV 3    
>OPENCV_VERSION := 3

3、编译   
>make all -j6  

>make test  

>make runtest  

4、编译成功



