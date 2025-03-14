---
title: C++中的基本概念
date: 2025-01-07 15:14:27
tags:
---

## c++中的对比概念

### 指针和引用

#### 为什么有了指针还需要引用

1. 语法的便利和可读
2. 对函数重载的支持和语义的准确性
3. 某些操作的语义要求



### 静态多态和动态多态

#### 静态多态（编译时多态）

1. 函数重载，同名函数，参数列表不同
2. 模板（泛型程序机制）

#### 动态多态（运行时多态）

1. 虚函数和重写
2. 纯虚函数和抽象类



### 指针的一些用法

``` c++
int *p = 0x0;
p++;
```

0x意味着十六进制数，等于十进制的0（c++中0为NULL）

即定义p为一个空指针

而p++，指针自增时，会增加指针指向的地址，具体多少取决于指针指向的类型（具体大小依赖平台）此处int为4个字节，另此时内存地址仍无效

long在32位系统上占4个字节，而在64位系统上占8个字节

指针类型在32位系统上占4个字节，因为系统地址总线32位，而在64位系统上占8个字节，因为系统地址总线64位



### 类的默认访问权限是private，结构体的默认访问权限是public

### const常量具有类型，编译器可以进行安全检查，而define宏定义没有数据类型，只是简单的字符串替换，编译器不能进行安全检查



### 面向对象和面向过程

**面向过程：**分析解决问题的步骤

下五子棋{

开始游戏（）；

黑子先走（）；

绘制画面（）；

判断输赢（）；

轮到白子（）；

绘制画面（）；

判断输赢（）；

返回到 黑子先走（）；

输出最后结果；

}

**面向对象：**一切都是对象

（1）黑白双方，这两方的行为是一样的。

（2）棋盘系统，负责绘制画面

（3）规则系统，负责判定犯规、输赢等。

（4）第一类对象（黑白双方）负责接受用户输入，
并告知第二类对象（棋盘系统）棋子布局的变化，棋盘系统接收到了棋子的变化，
并负责在屏幕上面显示出这种变化，同时利用第三类对象（规则系统）来对棋局进行判定。



### 赋值方法

string赋给char数组：strcpy(char[], string.str());



### static

动态库加载的时候会首先执行init初始化过程，static静态变量就是在这个过程中创建，在初始化完成之后，才把控制权交给main函数。 当卸载动态库的时候，static静态变量会在这个过程中卸载。 因此静态库在程序执行之前就创建了。



### extern

在 C++ 中，extern 是一个关键字，用于声明外部变量和函数。它告诉编译器某个变量或函数是在其他源文件中定义的，并且在当前源文件中需要引用。

#### 作用：

**外部变量声明：**如果你想在一个源文件中定义一个全局的变量，并且希望在其他源文件中也能够访问和使用该变量，那么你需要在其他源文件中使用 extern 关键字进行声明。这样，编译器就知道该变量是在其他地方定义的，而不是当前源文件中定义的。例如：

```c++
// File1.cpp
int globalVar = 42;

// File2.cpp
extern int globalVar;

int main() {
    // 可以在此处访问和使用 globalVar
    return 0;
}
```

**外部函数声明：**当你在一个源文件中定义了一个函数，并且希望在其他源文件中使用该函数时，你需要在其他源文件中使用 extern 关键字进行声明。这样，编译器就知道该函数是在其他地方定义的，并且可以正确地链接到该函数的定义。例如：

```c++
// File1.cpp
void someFunction() {
    // 函数定义
}

// File2.cpp
extern void someFunction();

int main() {
    someFunction();  // 调用定义在 File1.cpp 中的函数
    return 0;
}
```


需要注意的是，extern 声明仅仅是告诉编译器该变量或函数是在其他地方定义的，而不是实际的定义。因此，你不能在 extern 声明语句中初始化变量，而只能在定义的地方进行初始化。同时，如果你声明了一个 extern 变量或函数，但却没有在其他源文件中进行定义，那么在链接时会引发链接错误。
