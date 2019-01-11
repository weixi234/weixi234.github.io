---
title: WPF学习1
date: 2019-01-11 18:44:14
tags:
- 基础语法
- WPF  
categories: 
- WPF 
---

# 前言  



# WPF基础  
## XAML初识  
- xaml背景渐变  

方法一：直接XAML编辑  
```html
<Grid.Background>
    <LinearGradientBrush>
        <LinearGradientBrush.GradientStops>
            <GradientStop Offset="0.00" Color="Red"/>
            <GradientStop Offset="0.50" Color="Indigo"/>
            <GradientStop Offset="1.00" Color="Violet"/>
        </LinearGradientBrush.GradientStops>
    </LinearGradientBrush>
</Grid.Background>

```

方法二：直接编辑属性  
指定控件->属性->画笔->Background->渐变画笔->

- xaml附加属性  

(1)布局->RowDefinitions->    

(2)显示空格    
>xml:space="preserve"   

(3)显示特定符号   
>\&lt;  =>  <   
\&gt;    =>  >

- xaml事件  

Click="Button_Click"   

# 创建WPF应用程序  
## 只使用代码创建WPF应用程序
优点：可以任意创建窗体    

新建->项目->WPF应用程序    

添加->新建项->类Class1   

```C#
using System.Windows;
using System.Windows.Controls;
using System.Windows.Markup;

namespace WpfApp1
{
    class Class1:Window 
    {
        private Button button1;
        public Class1()
        {
            InitializeComponent();
        }
        private void InitializeComponent()
        {
            //设置窗体
            this.Height = 260;
            this.Width = 285;
            this.Left = this.Top = 100;
            this.Title = "wpf";
            //创建停靠面板对象
            DockPanel panel = new DockPanel();
            //创建按钮对象
            button1 = new Button();
            button1.Content = "click me";
            button1.Margin = new Thickness(30);

            button1.Click += button1_click;

            IAddChild container = panel;
            container.AddChild(button1);
            container = this;
            container.AddChild(panel);
        }
        private  void button1_click(object sender,RoutedEventArgs e)
        {
            button1.Content = "444";
        }
    }
}


using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows;

namespace WpfApp1
{
    class Class2APP:Application
    {
        [STAThread()]

        static void Main()
        {
            Class2APP app = new Class2APP();
            app.MainWindow = new Class1();
            app.MainWindow.ShowDialog();
        }
    }
}

```

项目->属性->应用程序->启动对象->WpfApp1.Class2App  

## 使用代码和未经编译的XAML创建WPF应用程序  







