---
title: Ubuntu从入门到精通——3 编译第三方库OpenCV
date: 2018-12-19 09:55:43
tags:
- Ubuntu
- OpenCV
- GPU
categories: 
- 深度学习
---
# 前言  
## 前言1
我之前编译配置过opencv很多遍，无论是win环境还是Linux环境。这次在配置好CUDA的Ubuntu环境下编译配置OpenCV3.4和contribute库，也顺便将之前的经验总结归纳。另说明一点，之前在Ubuntu下我一直是输入命令cmake，发现CMAKE-GUI在Ubuntu下也蛮好用。  
## 前言2
现修改并完善之前的博文，成一个系列（20181219）

# 环境准备  
## 搭建编译环境  
>sudo apt-get install build-essential  
## 安装辅助工具  
安装cmake git pkg-conflg等辅助工具  
>sudo apt-get install cmake git pkg-config libgtk2.0-dev libavcodec-dev libavformat-dev libswscale-dev  
## 安装关联库  
>sudo apt-get install python-dev python-opencv python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev  

### Ubuntu18.04下依赖包libjasper-dev无法安装问题  
提示：>ubable to locate libjasper-dev    
解决方法：  
>sudo add-apt-repository "deb http://security.ubuntu.com/ubuntu xenial-security main"   
>sudo apt update  
>sudo apt install libjasper1 libjasper-dev  

说明：libjasper1是libjasper-dev的依赖包   
### cmake中not find package libgphoto2  
>sudo apt-get -y install libgphoto2-dev  

# 编译OpenCV  
## 用命令方式  
1、最好将Opencv3.4.zip移动到用户主目录下进行编译：  
>mv [opencv3.4源码存放路径] ~/opencv_3.0.0.zip  
unzip opencv-3.0.0.zip  
cd opencv-3.0.0  
mkdir release  
cd release  
2、CMAKE编译  
2.1 没有安装cuda可以不开cuda进行cmake  
>cmake ../opencv-3.4.1 -DWITH_GTK_2_X=ON -DCMAKE_INSTALL_PREFIX=/usr/local  //参考  
**我之前用的是：**  
>cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local(安装路径，用于存放生成的lib与dll文件) ~/opencv-3.2.0(源路径)

2.2 安装了cuda可以使用支持cuda加速命令进行cmake（可以直接复制）  
>cmake  ../opencv-3.4.1 -DWITH_GTK_2_X=ON -DCMAKE_INSTALL_PREFIX=/usr/local -DCMAKE_BUILD_TYPE=RELEASE -DCMAKE_INSTALL_PREFIX=/usr/local -DWITH_TBB=ON -DBUILD_NEW_PYTHON_SUPPORT=ON -DWITH_V4L=ON -DINSTALL_C_EXAMPLES=ON -DINSTALL_PYTHON_EXAMPLES=ON -DBUILD_EXAMPLES=ON  -DWITH_OPENGL=ON -DENABLE_FAST_MATH=1 -DCUDA_FAST_MATH=1 -DWITH_CUBLAS=1 -DWITH_OPENMP=ON//待验证  


## 用CMAKE-GUI方式  
~~1、输入源码路劲~~  
~~2、输出编译路径~~  
3、勾选Grouped、Advanced->Configure  
**安装好CUDA后 会检测到CUDA：CUDA detected:9.0**   
可以检查一下WITH_CUDA选项，如果cmake检测到安装了CUDA则应该是自动勾上的。    
4、编译选项：  
>OPENCV_EXTRA_MODULES_PATH -> contribtue/modules  
>CMAEK_INSTALL_PREFIX ->/usr/local   
5、generate按钮  
6、输出编译路径下：make -j4  
7、sudo make install   
**出现错误**  
1、not used beacuse 'OPENCV_TRAITS_ENABLE_DEPRECATED' is defined  
>ENABLE_PRECOMPILED_HEADERS = OFF

## 配置OpenCV环境  
1、在/etc/ld.so.conf/opencv.conf文件中加入一行：  
>/usr/local/lib  

若无此路径则新建个：  
>sudo gedit /etc/ld.so.conf/opencv.conf  

2、使上述修改生效  
>sudo ldconfig  

3、在/etc/bash.bashrc中加入:  
>PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/lib/pkgconfig  
export PKG_CONFIG_PATH  

4、修改使上述生效  
>source /etc/profile  

## OpenCV编译验证  
1、在opencv/samples\cpp\example_cmake中  
>cmake  
>make  
>运行  

2、查看opencv版本  
>pkg-config --modversion opencv







