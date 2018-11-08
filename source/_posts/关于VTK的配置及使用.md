---
title: 关于VTK的配置及使用
date: 2018-11-08 11:54:14
tags: 
- 三方库
- VTK
category:
- VTK
---

# 前言


# VTK的使用
>#include "vtkSmartPointer.h"  
#include "vtkRenderWindow.h"  
#include "vtkRenderer.h"  
#include "vtkRenderWindowInteractor.h"  
#include "vtkInteractorStyleTrackballCamera.h"  
#include "vtkCylinderSource.h"  
#include "vtkPolyDataMapper.h"  
#include "vtkActor.h"  
#include <vtkImageViewer.h>  
#include "vtkAutoInit.h"  

VTK_MODULE_INIT(vtkRenderingOpenGL);  
VTK_MODULE_INIT(vtkInteractionStyle);  
VTK_MODULE_INIT(vtkRenderingFreeType);  


# 其他说明项
## VS2013中配置VTK7.0
### debug模式不通过
连接错误2019 RenderingOpenGL_AutoInit_Construct(void)，而Release模式可以通过。  
解决方法：项目->属性->配置属性->C/C++->预处理器中去掉#;_DEBUG#  
### VTK_MODULE_INIT宏  
vtk7.0是vtkRenderingOpenGL2
VTK_MODULE_INIT(vtkRenderingOpenGL2);