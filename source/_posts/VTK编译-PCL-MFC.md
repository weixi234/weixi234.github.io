---
title: VTK编译 PCL MFC
date: 2018-11-10 17:57:27
tags: 
- VTK
- MFC
- PCL
- CMAEK
categories: 
- VTK
---
# 前言  
之前研究过PCL，PCL中用VTK显示。之前编译是因为我用的是  PCL-AllInOne-msvc2017，其中的第三方库VTK8.0没有QT控件QVTKWidgetPlugin；后来编译是因为老师用的是VS2013，想在VS2013中搭建PCL-MFC开发环境，用的是PCL1.8.0msvc2013-vtk7.0。  
现在这篇博文是根据我的映像回忆来记录的，可能会有些许错误，可以在下方留言，我稍后修改。  
环境说明：  
VTK7.0，VS2013，x32下编译debug和release两个版本。
# 准备  
1、VTK官网下载VTK7.0版本源码包zip，不过7.0版本较老，要仔细看一下官网信息找到历史版本。  
>[VTK下载页面](https://www.vtk.org/download/)  
>[VTK-Git源码库](https://gitlab.kitware.com/vtk/vtk)  
2、下载CMAKE  
3、下载Qt5  
# CMAKE  
1、打开cmake-gui  
2、设置源码路径（源码vtk7.0包含CMakeLists.txt的路径）  
3、设置构建路径（CMake会在该目录下生成solution（针对VS），打开vs就可以编译出结果）  
4、勾选Cmake界面上的Grouped（对配置分组）和Advanced（显示高级配置）
5、点击Configure，CMake要求选择编译器，按需求选即可（Visual Studio 12 2013）（好像有多个选项，有个x64的，有amd，注意别选错了）  
6、等待CMake把配置生成完毕后，对部分配置进行修改（所有红色显示的配置都是需要用户确认的配置或者有问题的配置）  
>BUILD_SHARED_LIBS      //勾选，构建动态库（lib和dll）  
>VTK_Group_Qt           //勾选，使用Qt   
>VTK_GUISupportQtOpenGL //勾  //*Module分类中自己看看，Qt相关的尽量全部勾选上*  
>**CMAKE_INSTALL_PREFIX**   //改为D:/VTK/7.0/msvc2013_x32  
建议修改，此目录可以自行修改，当VTK编译完成后，安装时，会将VTK安装到指定的目录，强烈建议修改。  
>**INSTALL_BIN_DIR**        //改为D:/VTK/7.0/msvc2013_x32/bin  
>**INSTALL_INC_DIR**        //改为D:/VTK/7.0/msvc2013_x32/include  
>**INSTALL_LIB_DIR**        //改为D:/VTK/7.0/msvc2013_x32/lib  
>**INSTALL_MAN_DIR**        //改为D:/VTK/7.0/msvc2013_x32/share/man   
>**INSTALL_PKGCONFIG_DIR**  //改为D:/VTK/7.0/msvc2013_x32/share/pkgconfig   
>VTK_QT_VERSION             //改为5  
*以下7项关于Qt的，如果Qt环境配置好了就会自动填好，不需修改，确认一下就可以了*  
>QT_QMAKE_EXECUTABLE    //D:/Qt/5.5.1/5.5/msvc2013_32/bin/qmake.exe  
>QT5_DIR    //D:/Qt/5.5.1/5.5/msvc2013_32/lib/CMake/Qt5  
>QT5Core_DIR    //D:/Qt/5.5.1/5.5/msvc2013_32/lib/CMake/Qt5Core  
>QT5Gui_DIR    //D:/Qt/5.5.1/5.5/msvc2013_32/lib/CMake/Qt5Gui  
>Qt5Sql_DIR    //D:/Qt/5.5.1/5.5/msvc2013_32/lib/CMake/Qt5Sql  
>QT5UiPlugin_DIR    //D:/Qt/5.5.1/5.5/msvc2013_32/lib/CMake/Qt5UiPlugin    
>QT5Widgets_DIR    //D:/Qt/5.5.1/5.5/msvc2013_32/lib/CMake/QT5Widgets  **7、点击Add Entry**   
>Name:CMAKE_DEBUG_POSTFIX  
>TYPE:STRING  
>**value:-gd**  
>Description:可以为空      
这样做的目的是：让debug版本编译出二进制文件带有-gd，以便和release版本编译出的二进制文件相区分，强烈建议。  
**8、根据上述debug后缀修改Qt控件生成cmake文件**  
cmake生成完后，找到构建目录下的\GUISupport\Qt\Plugininstall.cmake文件。第五行  
>SET(VTK_INSTALL_QT_PLUGIN_FILE"QVTKWidgetPlugind.dll")  
*修改为*  
>IF(BUILD_TYPE MATCHES Debug)  
>   SET(VTK_INSTALL_QT_PLUGIN_FILE"QVTKWidgetPlugin-gd.dll")  
> ELSE()  
>   SET(VTK_INSTALL_QT_PLUGIN_FILE"QVTKWidgetPlugin.dll")  
> ENDIF()  
保存。  
**9、MFC相关勾选**  
>Module_vtkGUISupportMFC  
**10、其他选项**  
>Module_vtkRenderingOpenGL //勾选  
*Module分类中自己看看，尽量全部勾选上，免得使用时缺少相关组件*    
# VS编译  
1、用VS2013打开构建目录下的VTK.sln解决方案  
2、确保ALL_BUILD未启动项，选择ALL_BUILD项目右键点击生成  
3、右键INSTALL项目，选择仅生成INSTALL  
4、切换成release版本，重复上述步骤  
**建议，先安装Debug版，再安装Release版。后安装的版本会覆盖同名的文件**

# 编译结果简要说明  
- bin文件夹   
Debug/Release版本各有109个dll和2个exe  
dll，Debug和Release版一一对应  
exe，同名，但内容不同  
- lib文件夹  
Debug/Release版本一一对应    
- include文件夹  
Debug/Release版本相同  

# 编译过程中一些问题说明  
1、字符集问题  
错误：error MSB8031: Building an MFC project for a non-Unicode character set is deprecated   
解决办法：[MSDN网站来一波](https://msdn.microsoft.com/zh-cn/library/dn251007%28v=vs.120%29.aspx?f=255&MSPPError=-2147217396)  
2、VS2013编译错误vtkMFCWindow.cpp    
错误：error C2661: 'vtkOpenGLRenderWindow::GetPixelData' : no overloaded function takes 5 arguments  
函数是需要6个参数，但是呢只提供了5个。
>unsigned char *pixels =  
>this->pvtkWin32OpenGLRW->GetPixelData(0,0,size[0]-1,size[1]-1,0,>0); 

 解决方案：[官网提问来一波](https://gitlab.kitware.com/vtk/vtk/issues/17164)    

 *当然这只是一小部分问题，有些我也遗忘了，后期再补充；编译过程中可能会遇到各问题，自己学着网上找找解决方案*

 # MFC中用PCL-VTK显示  
 [参考博客](https://www.cnblogs.com/zfluo/p/5131844.html)  
<iframe height=352 width=486 src="MFC-VTK.gif">
</iframe>

 [另外一个关于MFCVTK显示的博客--MFC中嵌入VTK显示点云数据](https://blog.csdn.net/eric_e/article/details/79778651)   

# VTK的学习
关于VTK的学习，我现在觉得有三个较好的途径：  
- [东灵工作室系列教程我觉得较好](https://blog.csdn.net/www_doling_net/article/details/8763686)    
- VTK User's Guide  
- The Visualization Toolkit An Object-Oriented Approach to 3D Graphics  

# VTK的使用  
## VTK宏  
>#include "vtkAutoInit.h"  
**vtk7.0是vtkRenderingOpenGL2，vtk8.0是vtkRenderingOpenGL**  
>VTK_MODULE_INIT(vtkRenderingOpenGL2);  
>VTK_MODULE_INIT(vtkInteractionStyle);  
>VTK_MODULE_INIT(vtkRenderingFreeType);  
## VTK其他说明
- VS2013中配置VTK7.0 debug模式不通过  

连接错误2019 RenderingOpenGL_AutoInit_Construct(void)，而Release模式可以通过。  
解决方法：项目->属性->配置属性->C/C++->预处理器中去掉#;_DEBUG#    
- PCL融合进MFC宏错误  

错误说明：error C2589: “(”:“::”右边的非法标记  
错误代码：  
>size.Width = std::max(size.Width, elementSize.Width);    

错误原因：函数模板max与Visual C++中的全局的宏max冲突  
解决方法：  
方法一：
设置项目属性，在预定义处理器中添加定义NOMINMAX来禁止使用Visual C++的min/max宏定义。  
>项目属性—>C/C++—>预处理器—>预处理器定义 (此处添加预定义编译开关   NOMINMAX）  

但是visual C++中定义能自动匹配double和int，如果进行了上述设置，代码中手动将int型的数据乘以1.0来达到double的目的。   
方法二：加上括号(将max括起来)，与Vsual C++的min/max宏定义区分开  
>size.Width = (std::max)(size.Width, elementSize.Width);   
  




