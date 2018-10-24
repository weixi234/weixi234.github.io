---
title: LINUX中的库文件总结
date: 2018-10-19 08:54:28
tags:
- LINUX
- 库文件
- 编译原理
- LINUX编程
categories:
- Ubuntu
---
# 前言

# gcc编译过程
*我这里就不用什么流程图语法了，发现markdown语法编辑器似乎不支持*

**hello.c**--*预处理器(cpp)[gcc -E]*-->**hello.i(c文件)**--*编译器(gcc)[gcc -S]*-->**hello.s(汇编文件)**--*汇编器(as)[gcc -C]*-->**hello.o(二进制文件)**--*连接器(ld)[gcc]*-->**a.out(可执行文件)**

*预处理器：cpp ===>头文件展开，宏替换，注释去掉   gcc -E hello.c -o hello.i  
*编译器：gcc ===>C文件变成汇编文件 gcc -S hello.i -o hello.s  
*汇编器：as ===>汇编文件变成二进制文件 gcc -c hello.s -o hello.o  
*链接器：ld ===>将函数库中相应的代码组合到目标文件中 gcc hello.o -o hello  
例：
>gcc example.c -I ./include -o app -D DEBUG -O3 -Wall -g  

-I 指定头文件路劲  
-o 指定生成文件名字  
-D 编译时指定宏  
-O 是否优化（冗余）程序（最高3，0不优化）  
-Wall 在程序编译的时候输出警告信息  
-g 程序中生成gdb调试信息（gdb调试时必须添加此）  

# 静态库的制作和使用
1、命名规则
>lib + 库名字 + .a   

2、制作步骤  
step1：生成对应的.o文件 --.c---->.o   参数-c  
step2: 将生成的静态.o文件打包（ar rcs + 静态库的名字（libMytest.a） + 生成的所有.o）  
例：
>*gcc *.c -c -I../include  
>*ar rcs libMyCalc.a *.o  
>mv ../lib   

step3: 发布和使用静态库  
* 发布静态库  /lib
* 头文件  /include  

step4: 使用
>方法一：gcc maintest.c lib/libMyCalc.a -o sum -I include  
>方法二：gcc maintest.c -I include -L lib -l MyCalc -o myapp

-L 指定库的路劲  
-l  指定库的名字

**静态库的优缺点**

1、发布程序时，不需提供对应的库，因为已经将对应的库二进制打包到程序中  
2、加载库速度快  
3、库打包到程序中去，导致程序很大  
4、库发生了改变，需要重新编译程序  

查看库里面的东西：  
> nm libMyCalc.a  
    add.o:  
        0000000000 T add  
    div.o:  
        0000000000 T div

例：>nm myapp //也可以查看可执行文件  
**T表示add 代码存放在代码区**

# 共享库（动态库）
1、命名规则
>lib + 名字 + .so  

2、制作步骤  
step1:生成与位置无关的.o  
>gcc -fPIC -c *.c -I ../include

step2:将.o打包成共享库（动态库）
>gcc -shared libMyCalc.so *.o -I include

step3:发布.so和.include文件  
step4：使用
>gcc maintest.c lib/libMyCalc.so -o app -I include  
>gcc maintest.c -I include -L ./lib -l MyCalc -o myapp  
//-L 指定库路劲  
//-l 指定库名字

**ldd查看可执行文件执行时所依赖的所有动态库**
>ldd myapp  

可执行程序(./a.out)<---动态连接器，会自动调用所需要的动态库---动态库(libmytest.so)  
动态连接器是按照环境变量去找动态连接库  
>echo $PATH  //打印环境变量  
系统加载的时候从系统库(/lib)中加载

 **LINUX中动态库的位置：**  
1、/lib（更目录下lib目录）
所以，可以将用户库.so放到系统库目录下  (ldd myapp 可查看）
2、除系统库外提供了一个环境变量让用户配置LD_LIBRARY_PATH,系统会先搜索这个环境变量里的库，再去搜索系统默认库
>echo $LD_LIBRARY_PATH

>export(导入) LD_LIBRARY_PATH=./lib(将自己库路劲赋给环境变量)

**注：这是临时设置，终端关闭重启就没了，主要用于开发过程中临时测试**  
3、当前用户的.bashrc文件，相对上面是长久生效方法  
将下面添加在.brashrc文件最后,**重启终端生效**
>export(导入) LD_LIBRARY_PATH=./lib(将自己库路劲赋给环境变量)

**4、重要方法**
step1：需要找到动态库连接器的配置文件(/etc下的ld.so.conf文件)
>/etc/ld.so.conf  

step2：将自己的动态库的路径写道配置文件中  
>/home/.../lib  //注意要写绝对路径

step3：更新动态连接器配置文件使其生效
>sudo ldconfig -v(输出信息)

**动态库优缺点**
1、执行程序体积小
2、动态库更新了，不需要重新编译程序，函数接口不变
3、发布时需将动态库提供给用户
4、动态库没有加载到应用程序中，加载速度较慢


