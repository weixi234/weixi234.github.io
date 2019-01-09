---
title: 再温C++——1 C++中的异常
date: 2018-12-25 09:22:06
tags:
- 基础语法
- C++
- C++异常
categories: 
- 再温CPP
---

# 前言
 之前学过C++，时间长了，有些遗忘。当时学时也有些不扎实，一直想写这个再温系列，一直没抽出时间，今天就以这个CPP中的异常开始吧。（20181225）   

# 异常介绍
1、异常是一种程序控制机制，与函数机制独立和互补    
函数是一种以栈结构展开的上下函数衔接的程序控制系统,异常是另一种控制结构,它依附于栈结构,却可以同时设置多个异常类型作为网捕条件,从而以类型匹配在栈机制中跳跃回馈.   
2、异常设计目的：   
栈机制是一种高度节律性控制机制,面向对象编程却要求对象之间有方向、有目的的控制传动,从一开始，异常就是冲着改变程序控制结构，以适应面向对象程序更有效地工作这个主题，而不是仅为了进行错误处理。   
异常设计出来之后，却发现在错误处理方面获得了最大的好处。

# 异常处理的基本思想
1、C/Cpp中传统错误处理机制是通过函数返回值来处理错误。      
2、C++的异常处理机制使得**异常的引发和异常的处理不必在同一个函数中**，这样**底层的函数可以着重解决具体问题，而不必过多的考虑异常的处理。上层调用者可以再适当的位置设计对不同类型异常的处理**。   
3、异常是专门针对抽象编程中的一系列错误处理的，C++中不能借助函数机制，因为栈结构的本质是先进后出，依次访问，无法进行跳跃，但错误处理的特征却是遇到错误信息就想要转到若干级之上进行重新尝试。   
4、异常超脱于函数机制，决定了其对函数的跨越式回跳。   
5、异常跨越函数。   
![异常图示](异常图示.png)    

# 异常基本语法
## 基本语法
```c++
//抛掷异常程序段
void Fun()
{
    //······
    throw 表达式;
    //······
}
//捕获并处理异常程序段
try
{
    复合语句
}
catch(异常类型声明)
{
    复合语句
}
catch(类型(形参))
{
    复合语句
}
//······
//······

```

## 语法阐述  
1、若有异常则**通过throw操作创建一个异常对象**并抛掷。   
2、将可能抛出异常的程序段嵌在try块之中。控制通过正常的顺序执行到达try语句，然后执行try块内的保护段。   
3、如果在保护段执行期间没有引起异常，那么跟在try块后的catch子句就不执行。程序从try块后跟随的最后一个catch子句后面的语句继续执行下去。   
4、catch子句按其在try块后出现的**顺序被检查**。匹配的catch子句将捕获并处理异常（或继续抛掷异常）。   
5、如果匹配的处理器未找到，则运行函数terminate将被自动调用，其缺省功能是调用abort终止程序。   
6、处理不了的异常，可以在catch的最后一个分支，使用throw语法，向上扔。     
7、异常机制与函数机制互不干涉，但捕捉的方式是基于类型匹配。捕捉相当于函数返回类型的匹配，而不是函数参数的匹配，所以捕捉不用考虑一个抛掷中的多种数据类型匹配问题。    
8、异常捕捉严格按照类型匹配。异常捕捉的类型匹配之苛刻程度可以和模板的类型匹配媲美,它不允许相容类型的隐式转换,比如,抛掷char类型用int型就捕捉不到。    

## 基本语法实例
1、被零整除
```c++  
int divide(int x, int y )
{
    if (y ==0)
    {
        throw x;//x是整形
        //功能函数中抛异常，异常抛出跨函数
        //一直往上抛，直到被catch捕获，或最终抛出异常终止  
    }
    return x/y;
}

void main()
{
    try//上层函数接异常
    {
        cout << "8/2 = " << divide(8, 2) << endl;
        cout << "10/0 =" << divide(10, 0) << endl;
	}
    //catch是按照类型进行
	catch (int e)//x是整形
    {
        cout << "e" << " is divided by zero!" << endl;
    }
    catch(...)//其他类型异常 
    {
        throw;//也可继续往上抛
        cout << "未知异常" << endl;
    }
        
    cout << "ok" << endl;
    system("pause");
    return ;
}

```

2、类中的异常抛出  
```c++
class A{};
void f()
{
  if(...) throw A;  
  //throw A将穿透函数f，g和main，抵达系统的最后一道防线——激发terminate函数,该函数调用引起运行终止的abort函数.
}
void g()
{
  try
  {
    f();
  }
  catch(B)
  {
    cout<<“exception B\n”;
  }
}

int main()
{
  g();
}

```
**补充：**      
最后一道防线的函数可以由程序员设置，从而规定其终止前的行为。   
修改系统默认行为：     
可以通过set_terminate函数修改捕捉不住异常的默认处理器，从而使得发生捉不住异常时，被自定义函数处理：  
- void myTerminate(){cout<<“HereIsMyTerminate\n”;}   
- set_terminate(myTerminate);   
- set_terminate函数在头文件exception中声明，参数为函数指针void(*)()    

3、构造函数中异常抛出
构造函数没有返回类型，无法通过返回值来报告运行状态，所以只通过一种非函数机制的途径，即异常机制，来解决构造函数的出错问题。     

4、异常抛出参数传递机制
异常机制与函数机制互不干涉，但捕捉的方式是**基于类型匹配**。捕捉相当于**函数返回类型的匹配**，而不是**函数参数的匹配**，所以捕捉不用考虑一个抛掷中的多种数据类型匹配问题。

```c++
class A{};  
class B{};

int main()
{
    try
    {
        int 	j = 0;    
        double 	d = 2.3;    
        char 	str[20] = "Hello";
        cout<<"Please input a exception number: ";
        int a; 
        cin>>a;
        switch(a)
        {
            case  1: 
                throw d;      
            case  2: 
                throw j;      
            case  3: 
                throw str;
            case  4: 
                throw A();      
            case  5: 
                throw B();
            default: 
                cout<<"No throws here.\n";    
        }
    }
     catch(int)
    {
        cout<<"int exception.\n";
    }
    catch(double)
    {
        cout<<"double exception.\n";
    }
    catch(char*)
    {
        cout<<"char* exception.\n";
    }
    catch(A)
    {
        cout<<"class A exception.\n";
    }
    catch(B)
    {
        cout<<"class B exception.\n";
    }

    cout<<"That's ok.\n";
    system("pause");
}
//====================================

```
**补充：**  
- catch代码块必须出现在try后，并且在try块后可以出现多个catch代码块，以捕捉各种不同类型的抛掷。   
- **异常机制是基于这样的原理：程序运行实质上是数据实体在做一些操作，因此发生异常现象的地方，一定是某个实体出了差错，该实体所对应的数据类型便作为抛掷和捕捉的依据。**   


5、异常捕捉严格按照类型匹配  
异常捕捉的类型匹配之苛刻程度可以和模板的类型匹配媲美,它不允许相容类型的隐式转换。比如,**抛掷char类型用int型就捕捉不到。**  
例如下列代码不会输出“int exception.”，从而也不会输出“That’s ok.”       

```c++
int main()
{
  try
  {
    throw ‘H’;
  }
  catch(int)
  {
    cout<<"int exception.\n";
  }
  cout<<"That's ok.\n";
}

```

 # 栈解旋(unwinding)
**异常被抛出后，从进入try块起，到异常被抛掷前，这期间在栈上的构造的所有对象，都会被自动析构。析构的顺序与构造的顺序相反。这一过程称为栈的解旋(unwinding)。**
```c++
class MyException {};
class Test
{
public:
    Test(int a=0, int b=0)
    {
        this->a = a;
        this->b = b;
        cout << "Test 构造函数执行" << "a:" << a << " b: " << b << endl;
    }
    void printT()
    {
        cout << "a:" << a << " b: " << b << endl;
    }
    ~Test()
    {
        cout << "Test 析构函数执行" << "a:" << a << " b: " << b << endl;
    }
private:
    int a;
    int b;
};

void myFunc() throw (MyException) //异常接口声明
{
    Test t1;
    Test t2;

    cout << "定义了两个栈变量,异常抛出后测试栈变量的如何被析构" << endl;

    throw MyException();//疑惑，这里抛出的是类对象还是构造函数？？？
}

void main()
{
    //异常被抛出后，从进入try块起，到异常被抛掷前，这期间在栈上的构造的所有对象，
    //都会被自动析构。析构的顺序与构造的顺序相反。
    //这一过程称为栈的解旋(unwinding)
    try  
    {
        myFunc();
    }
    //catch(MyException &e) //这里不能访问异常对象？？？
    catch(MyException ) //这里不能访问异常对象？？？
    {
        cout << "接收到MyException类型异常" << endl;
    }
    catch(...)
    {
        cout << "未知类型异常" << endl;
    }
	
    system("pause");
    return ;
}

```

# 异常接口声明
- 为了加强程序的可读性，可以在函数声明中列出可能抛出的**所有异常类型**，例如：   
>void func() throw (A,B,C,D); //这个函数func（）能够且只能抛出类型A、B、C、D及其子类型的异常。   

- 如果在函数声明中没有包含异常接口声明，则次函数可以**抛掷任何类型的异常**，例如：       
>	void func();  

- 一个**不抛掷任何类型异常**的函数可以声明为：
>	void func()  throw();  

- 如果一个函数抛出了它的异常接口声明所**不允许抛出的异常**，unexpected函数会被调用，该函数默认行为调用terminate函数中止程序。    

# 异常类型和异常变量的生命周期
- throw的异常是有类型的，可以使，数字、字符串、类对象。      
- throw的异常是有类型的，catch严格按照类型进行匹配。     
- 注意：异常对象的内存模型。    

## 传统错误处理
```c++
//文件的二进制copy
int filecopy(char *filename2, char *filename1 )
{
    FILE *fp1= NULL,  *fp2 = NULL;

    fp1 = fopen(filename1, "rb");
    if (fp1 == NULL)
    {
        return 1;
    }

    fp2 = fopen(filename2, "wb");
    if (fp1 == NULL)
    {
        return 2;
    }

    char buf[256];
    int  readlen, writelen;
    while ( (readlen = fread(buf, 1, 256, fp1)) > 0 ) //如果读到数据，则大于0 
    {
        writelen = fwrite(buf, 1, readlen, fp2);
        if (readlen != readlen)
        {
            return 3;
        }
    }

    fclose(fp1);
    fclose(fp2);
    return 0;
}


void main()
{
    int ret;
    ret = filecopy("c:/1.txt","c:/2.txt");
    if (ret !=0 )
    {
        switch(ret)
        {
        case 1:
            printf("打开源文件时出错!\n");
            break;
        case 2:
            printf("打开目标文件时出错!\n");
            break;
        case 3:
            printf("拷贝文件时出错!\n");
            break;
        default:
            printf("发生未知错误!\n");
            break;
        }
    }
}

```

## throw int类型异常
```c++
/文件的二进制copy
void filecopy(char *filename2, char *filename1 )
{
    FILE *fp1= NULL,  *fp2 = NULL;

    fp1 = fopen(filename1, "rb");
    if (fp1 == NULL)
    {
        //return 1;
        throw 1;
    }

    fp2 = fopen(filename2, "wb");
    if (fp1 == NULL)
    {
        //return 2;
        throw 2;
    }

    char buf[256];
    int  readlen, writelen;
    while ( (readlen = fread(buf, 1, 256, fp1)) > 0 ) //如果读到数据，则大于0 
    {
        writelen = fwrite(buf, 1, readlen, fp2);
        if (readlen != readlen)
        {
            //return 3;
            throw 3;
        }
    }

    fclose(fp1);
    fclose(fp2);
    return ;
}

```

## throw字符类型异常
```c++
//文件的二进制copy
void filecopy(char *filename2, char *filename1 )
{
    FILE *fp1= NULL,  *fp2 = NULL;

    fp1 = fopen(filename1, "rb");
    if (fp1 == NULL)
    {
        throw "打开源文件时出错";//**这里抛出时抛的是字符串常量**
    }

    fp2 = fopen(filename2, "wb");
    if (fp1 == NULL)
    {
        throw "打开目标文件时出错";
    }

    char buf[256];
    int  readlen, writelen;
    while ( (readlen = fread(buf, 1, 256, fp1)) > 0 ) //如果读到数据，则大于0 
    {
        writelen = fwrite(buf, 1, readlen, fp2);
        if (readlen != readlen)
        {
            throw "拷贝文件过程中失败";
        }
    }

    fclose(fp1);
    fclose(fp2);
    return ;
}

int main()
{
    try
    {
        filecopy(char *filename2, char *filename1 );
    }
    catch(char *e)//因为抛出时抛出的是字符串常量，所以此处指针可指向字符串常量指针  
    {
        cout<<e<<"char* 类型异常"<<endl;
    }

}


```

## throw类对象类型异常
```c++
class BadSrcFile 
{
public:
    BadSrcFile()
    {
        cout << "BadSrcFile 构造 do "<< endl;
    }
    ~BadSrcFile()
    {
        cout << "BadSrcFile 析构 do "<< endl;
    }
    BadSrcFile(BadSrcFile & obj)
    {
        cout << "拷贝构造  do "<< endl;
    }
    void toString()
    {
        cout << "aaaa" << endl;
    }

};

class BadDestFile {};
class BadCpyFile {};

void filecopy(char *filename2, char *filename1 )
{
    FILE *fp1= NULL,  *fp2 = NULL;

    fp1 = fopen(filename1, "rb");
    if (fp1 == NULL)
    {
        //throw new BadSrcFile();
        throw  BadSrcFile();//**这里必须加括号，抛出的是构造函数**
                            //**这里会不会产生匿名对象呢？**
    }

    fp2 = fopen(filename2, "wb");
    if (fp1 == NULL)
    {
        throw BadDestFile();
    }

    if (fp1 == NULL)
    {
        throw &(BadDestFile());//**抛出指针异常，catch(BadDestFile *e),用指针接**  
    }
    if (fp1 == NULL)
    {//**正确抛出指针异常写法，应该new出来，否则catch接的时候是野指针！！！！**
        throw new BadDestFile;  //!!! 
    }

    char buf[256];
    int  readlen, writelen;
    while ( (readlen = fread(buf, 1, 256, fp1)) > 0 ) //如果读到数据，则大于0 
    {
        writelen = fwrite(buf, 1, readlen, fp2);
        if (readlen != readlen)
        {
            throw BadCpyFile();
        }
    }

    fclose(fp1);
    fclose(fp2);
    return ;
}


//结论：//C++编译器通过throw 来产生对象，C++编译器再执行对应的catch分支，相当于一个函数调用，把实参传递给形参。
void main()
{
    try
    {
        filecopy("c:/1.txt","c:/2.txt");
    }
    catch (int e)
    {
        printf("发生异常：%d \n", e);
    }
    catch (const char * e)
    {
        printf("发生异常：%s \n", e);
    }
    catch ( BadSrcFile *e)//类 指针
    {
        e->toString();
        printf("发生异常：打开源文件时出错!\n");
        delete e;//new之后要delete，不然内存泄漏！！！    
    }
    catch ( BadSrcFile &e)//类 引用；**同一个类型，元素和引用不能同时写**
    {
        e.toString();
        printf("发生异常：打开源文件时出错!\n");
    }
    catch ( BadDestFile e)//类 对象；**是把匿名对象copy给e（调用拷贝构造函数），还是e是该匿名对象**
    //其实e是拷贝构造的！！！！！  
    {
        printf("发生异常：打开目标文件时出错!\n");
    }
    catch ( BadCpyFile e)
    {
        printf("发生异常：copy时出错!\n");
    }
    catch(...) //抓漏网之鱼
    {
        printf("发生了未知异常! 抓漏网之鱼\n");
    }
}


```
**重要结论：**  
**（1）若是用了类元素接抛出的异常，则该异常是调用拷贝构造函数进行构造的**  
**流程入下：**   
throw BadSrcFile()->BadSrcFile构造函数->catch(BadSrcFile e)->拷贝构造函数->析构throw BadSrcFile()匿名对象->析构catch(BadSrcFile e)拷贝构造函数     
**（2）catch（）的时候引用和元素不能同时写**
**使用引用是流程如下：（自会构造和析构一次）**    
throw BadSrcFile()->BadSrcFile构造函数->析构throw BadSrcFile()匿名对象   
**（3）指针和引用，指针和元素都能同时写，但是元素和引用不能同时写**
**如果是指针的话，抛出来的时候要new，捕捉后要delete**
**（4）引用最简单，指针最繁琐**


# 异常的层次结构(继承在异常中的应用)（重要）
- 异常是类 – 创建自己的异常类  
- 异常派生  
- 异常中的数据：数据成员  
- 按引用传递异常  
- 在异常中使用虚函数  
案例：设计一个数组类 MyArray，重载[]操作，数组初始化时，对数组的个数进行有效检查   
(1) index<0 抛出异常eNegative     
(2) index = 0 抛出异常 eZero      
(3) index>1000抛出异常eTooBig     
(4) index<10 抛出异常eTooSmall     
(5) eSize类是以上类的父类，实现有参数构造、并定义virtual void printErr()输出错误。  

```c++
class MyArray
{
    public:
        MyArray(int len);
        ~MyArray();
    public:
        int & operator[](int index);
        int getLen();

        class eSize
        {
        public:
            eSize(int size)
            {
                m_size=size;
            }
            virtual void printErr()
            {
                cout<<"size:"<<m_size<<" ";
            }
        protected:
            int m_size;

        };
        class eNegative:public eSize
        {
        public:
            eNegative(int size):eSize(size)
            {

            }
            virtual void printErr()
            {
                cout<<"eNegative类错误:"<<m_size<<" ";
            }

        };
        class eZero:public eSize
        {
        public:
            eNegative(int size):eSize(size)
            {
                
            }
            virtual void printErr()
            {
                cout<<"eZero类错误:"<<m_size<<" ";
            }
        };
        class eTooBig:public eSize
        {
        public:
            eNegative(int size):eSize(size)
            {
                
            }
            virtual void printErr()
            {
                cout<<"eTooBig类错误:"<<m_size<<" ";
            }    
        };
        class eTooSmall:public eSize
        {
        public:
            eNegative(int size):eSize(size)
            {
                
            } 
            virtual void printErr()
            {
                cout<<"eTooSmall类错误:"<<m_size<<" ";
            } 
        };


    private:
    int *m_space;
    int m_len;
}

MyArray::MyArray(int len)
{
    //构造函数中异常的抛出，因为构造函数中没有返回值
    if(len<0)
    {
        throw eNegative();
    }
    if(len=0)
    {
        throw eZero();
    }
    if(len<0)
    {
        throw eTooBig();
    }
    if(len<0)
    {
        throw eTooSmall();
    }

    m_len=len;
    m_space=new int[len];
}

MyArray::~MyArray()
{
    if(m_space!=NULL)
    {
        delete [] m_space;
        m_space=NULL;
        m_len=0;
    }
}
int & MyArray::operator[](int index)
{
    return m_space[index];
}
int MyArray::getLen()
{
    return m_len;
}


void main()
{
    //常见方法
    try
    {
        MyArray a(5);
        for(int i=0;i<a.getLen();i++)
        {
            a[i]=i+1;    
            printf("%d ",a[i]);
        }
    }
    catch(MyArray::eNegative &e)
    {
        cout<<"eNegative类型异常"<<endl;
    }
    catch(MyArray::eZero &e)
    {
    cout<<"eZero类型异常"<<endl;
    }
    catch(MyArray::eTooBig &e)
    {
    cout<<"eTooBig类型异常"<<endl;
    }
    catch(MyArray::eTooSmall &e)
    {
        cout<<"eTooSmall类型异常"<<endl;
    }

    //优化方法 类继承方式
    try
    {
        MyArray a(5);
        for(int i=0;i<a.getLen();i++)
        {
            a[i]=i+1;    
            printf("%d ",a[i]);
        }
    }
    catch(MyArray::eSize &e)//父类指针指向子类对象
    {
        e.printErr();
    }

}

```


# 标准程序库异常

example1:

```c++
// out_of_range
#include "iostream"
using namespace std;
#include <stdexcept>  
 
class Teacher
{
public:
	Teacher(int age)  //构造函数, 通过异常机制 处理错误
	{
		if (age > 100)
		{
			throw out_of_range("年龄太大");
		}
		this->age = age;
	}
protected:
private:
	int age;
};

void mainxx()
{
	try
	{
		Teacher t1(102);
	}
	catch (out_of_range e)
	{
		
		cout << e.what() << endl;
	}

	exception e;
	system("pause");
}
```


## 从标准类继承  

```c++
class MyException:public exception
{
public:
    MyException(const char *p)
    {
        this->m_p=p;
    }
    virtual const char* what()
    {
        cout<<"MyException:类型"<< m_p << endl;
        return;
    }
protected:
private:
    char *m_p;
}
void testMyExcept()
{
    throw MyException("函数异常");
}
void main()
{
    try
    {
        testMyExcept();
    }
    catch(MyException &e)
    {
        e.what();
    }
    catch(...)
    {
       cout<<"未知异常"<<endl;
    }
}

```







