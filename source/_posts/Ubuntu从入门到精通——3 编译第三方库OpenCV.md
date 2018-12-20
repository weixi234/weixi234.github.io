---
title: Ubuntu从入门到精通——3 编译第三方库OpenCV和PCL
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
PCL我之前一直都是在win下使用的，现在想在Ubuntu中使用。网上看了一下，发现不是很麻烦，遂和原来配置OpenCV的博文写在一起。

# OpenCV环境准备  
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

**》编译CUDA，勾上WITH_CUDA编译错误，而不勾编译通过，请看下方出现错误及解决《**    
5、generate按钮  
6、输出编译路径下：make -j4  
7、sudo make install   

**出现错误及解决**  
WITH_CUDA出现警告，但是未出错，按照下述操作：  
1、not used beacuse 'OPENCV_TRAITS_ENABLE_DEPRECATED' is defined  
>ENABLE_PRECOMPILED_HEADERS = OFF  

2、CUDA_GENERATION='Pascal'  
3、UDA_HOST_COMPILER=/usr/bin/gcc  
注意：这里/usr/bin/gcc的依据是之前gcc降级的操作（硬连接），还可以是/usr/bin/gcc-5等。  

4、若是命令编译，则：   
>sudo apt install gcc-5 g++-5
>cd opencv
>git co 3.4.1
>cd build
>rm -rf *
>cmake \
>-D BUILD_EXAMPLES=ON \
>-D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules \
>-D CMAKE_INSTALL_PREFIX=/usr/local \
>-D CMAKE_BUILD_TYPE=RELEASE \
>-D WITH_CUDA=ON \
>-D CUDA_GENERATION="Pascal" \
>-D CUDA_HOST_COMPILER:FILEPATH=/usr/bin/gcc-5  
>make

参考链接[Unable to compile OpenCV with CUDA9 and gcc6](http://answers.opencv.org/question/189712/unable-to-compile-opencv-with-cuda9-and-gcc6/)  


## 配置OpenCV环境  
1、在/etc/ld.so.conf.d/opencv.conf文件中加入一行：  
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
1、在opencv/samples\cpp\example_cmake中  ???   (我编译了example，不知道example在哪儿)   
>cmake  
>make  
>运行  

2、查看opencv版本  
>pkg-config - - modversion opencv


# PCL编译说明  
[PCL官网](http://www.pointclouds.org/)   
[PCL官方下载和安装说明](http://www.pointclouds.org/downloads/)  
注：我当前是用的prebuilt binaries方式，因为我在官网上找不到需要哪些依赖😭网上有些，但是我没有采纳，担心版本不对或依赖版本不是我心仪的😝  

# PCL编译
[参考博文-Ubuntu16.04下PCL库的安装与测试](https://blog.csdn.net/fsencen/article/details/79386570)  
依赖库(来自于网络，官网没找到)   
>sudo apt-get update   
>sudo apt-get install git build-essential    linux-libc-dev    
>sudo apt-get install cmake cmake-gui     
>sudo apt-get install libusb-1.0-0-dev libusb-dev   libudev-dev      
>sudo apt-get install mpi-default-dev openmpi-bin openmpi-common     
>sudo apt-get install libflann1.8 libflann-dev   
>sudo apt-get install libeigen3-dev    
>sudo apt-get install libboost-all-dev   
>sudo apt-get install libvtk5.10-qt4 libvtk5.10 libvtk5-dev   
>sudo apt-get install libqhull* libgtest-dev   
>sudo apt-get install freeglut3-dev pkg-config    
>sudo apt-get install libxmu-dev libxi-dev    
>sudo apt-get install mono-complete   
>sudo apt-get install qt-sdk openjdk-8-jdk openjdk-8-jre   

接着从github上把PCL源码clone下来:  
>git clone https://github.com/PointCloudLibrary/pcl.git

编译:  
>cd pcl   
>mkdir release   
>cd release   
>cmake -DCMAKE_BUILD_TYPE=None   -DCMAKE_INSTALL_PREFIX=/usr -D BUILD_GPU=ON  -D BUILD_apps=ON  -DBUILD_examples=ON   -D CMAKE_INSTALL_PREFIX=/usr ..   
>make   

安装

>sudo make install   
# 在程序中测试PCL点云显示 
## 测试代码pcl_test.cpp 

include \<iostream>  
include \<pcl/common/common_headers.h>  
include \<pcl/io/pcd_io.h>  
include \<pcl/visualization/pcl_visualizer.h>  
include \<pcl/visualization/cloud_viewer.h>  
include \<pcl/console/parse.h>  
 
 
int main(int argc, char **argv) {
    std::cout << "Test PCL !!!" << std::endl;
    
    pcl::PointCloud<pcl::PointXYZRGB>::Ptr point_cloud_ptr (new pcl::PointCloud<pcl::PointXYZRGB>);
    uint8_t r(255), g(15), b(15);
    for (float z(-1.0); z <= 1.0; z += 0.05)
    {
      for (float angle(0.0); angle <= 360.0; angle += 5.0)
      {
	pcl::PointXYZRGB point;
	point.x = 0.5 * cosf (pcl::deg2rad(angle));
	point.y = sinf (pcl::deg2rad(angle));
	point.z = z;
	uint32_t rgb = (static_cast<uint32_t>(r) << 16 |
		static_cast<uint32_t>(g) << 8 | static_cast<uint32_t>(b));
	point.rgb = *reinterpret_cast<float*>(&rgb);
	point_cloud_ptr->points.push_back (point);
      }
      if (z < 0.0)
      {
	r -= 12;
	g += 12;
      }
      else
      {
	g -= 12;
	b += 12;
      }
    }
    point_cloud_ptr->width = (int) point_cloud_ptr->points.size ();
    point_cloud_ptr->height = 1;
    
    pcl::visualization::CloudViewer viewer ("test");
    viewer.showCloud(point_cloud_ptr);
    while (!viewer.wasStopped()){ };
    return 0;
}

## CMakeList.txt文件  
cmake_minimum_required(VERSION 2.6)
project(pcl_test)

find_package(PCL 1.2 REQUIRED)

include_directories(${PCL_INCLUDE_DIRS})
link_directories(${PCL_LIBRARY_DIRS})
add_definitions(${PCL_DEFINITIONS})

add_executable(pcl_test pcl_test.cpp)

target_link_libraries (pcl_test ${PCL_LIBRARIES})

install(TARGETS pcl_test RUNTIME DESTINATION bin)

## 执行
把pcl_test.cpp和CMakeLists.txt发在同一个文件夹后执行如下命令：  
>cmake .  
>make  
>./pcl_test  

就可以看到一个漂亮的3D模型








