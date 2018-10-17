---
title: Gocator从零开始开发
date: 2018-10-16 15:57:50
tags:
- Gocator
- 二次开发
- MFC
categories:
- Gocator
---

# 前言
    实验室有个Gocator-2350A-3B-00老师让用起来

# 入门
1、官网查型号，找说明书和开发手册

公司官网[LMI TECHNOLOGIES](https://lmi3d.com/cn/products/gocator)

详细信息：产品->三维双目快照式传感器->Gocator 2300

产品资料下载：支持->产品资料下载->Gocator 2300

*喜欢说明文档做得好的公司*

应用指南中有各种开发文档，有支持HALCON的，有支持Hexsight的等等

我这里有个APPNOTE_Gocator_4.x_5.x_Multi_Sensor_Guide（同学下的，具体在什么位置我就没研究了）我觉得这个资料还不错，虽然是介绍多个传感器的，还是很值得一看的，里面还有些例程。

**说明文档中指出：**
>**1、怎么手动设置，怎么接收数据（不要求多个传感器之间的匹配）--->4.1参看GoMultiSensor_BasicSetup example**

>**2、自动设置流程--->4.2参看GoMultiSensor_AutomaticSetup**

>**3、设备初始化快速开始--->4.3.1参看GoMultiSensor_AutomaticSetup**

>**4、多设备间数据序列和匹配逻辑--->4.4参看GoMultiSensor_AutomaticSetup**

>**5、用GoSensor_Set多线程回调--->4.5.1参看GoMultiSensor_Multi-sensorthread**

>**6、多线程回调--->4.5.1参看GoMultiSensor_Multi-sensorthread**

>**7、多线程回调--->4.5.2参看GoMultiSensor_Multi-datathread**

>**SDK开发文档在GO_SDK-doc->GoSdk->Gocator_2x00下**

# 参看例程
配置环境就不用说了，缺什么就添什么，还算简单。
## GoMultiSensor_BasicSetup
### GoSdk_Construct
```
#include <GoSdk/GoSdk.h>
#include <kApi/kApi.h>
#define SENSOR_COUNT 2 

kStatus status;
DataContext *callbackContext;
kAssembly api;
unsigned int i;
GoSystem system;
GoSensor sensor[SENSOR_COUNT];
```
```
// construct Gocator API Library
if ((status = GoSdk_Construct(&api)) != kOK)
{
	printf("Error: GoSdk_Construct:%d\n", status);
	return;
}
```
解释：

This function should be called prior to calling any other Gocator SDK functions. 
 * When the library is no longer needed, call kObject_Destroy on the assembly object
 * that is returned by this function.
 * 
 * This function can safely be called multiple times.  In order to ensure 
 * final cleanup, kObject_Destroy must be invoked a corresponding number of times.

```
// construct GoSystem object
if ((status = GoSystem_Construct(&system, kNULL)) != kOK)
{
	printf("Error: GoSystem_Construct:%d\n", status);
	return;
}
```
解释：
* During construction, a GoSystem object uses Gocator Discovery Protocol to locate sensors.
* The list of detected sensors can be accessed using the GoSystem_SensorCount and GoSystem_SensorAt
* functions.






