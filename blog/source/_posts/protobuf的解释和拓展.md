---
title: protobuf的解释和拓展
date: 2025-01-07 15:35:27
tags:

---

### Protobuf 概述

protobuf 也叫 protocol buffer 是 google 的一种数据交换的格式，它独立于语言，独立于平台（什么编程语言，什么平台都可以用），由于它是一种二进制的格式，比使用xml、json进行数据交换快很多。可以用于分布式应用之间的数据通信，或者异构环境下的数据交换。



### 序列化步骤

1. 确定数据格式，数据可简单可复杂

```c++
struct person {
	int id;
	int height;
	string name[5];
	bool sex;
}

enum work {
	software,
	hardware,
	test,
}
```

2. 创建一个新的文件，文件名任意，但是文件后缀为.proto

3. 根据protobuf的语法，编辑.proto文件。

   **注意：**

   **所有的字段都必须定义在message或enum内部！！！**

   **在message中每个元素必须指定初始值（标签号），必须是一个正整数，且不能重复！！！**

   **在enum中每个元素必须指定初始值（标签号），必须是整数，且第一个字段必须是0，且不能重复！！！**

   **如果想要引入别的proto文件，使用 import "xxx.proto" ！！！**

   

```protobuf
syntax = "proto3";

package hry;

message person {
	int32 id = 12;
	int32 height = 175;
	repeated bytes name = 10;
	bool sex = 1;
}

enum work {
	software = 0;
	hardware = 4;
	test = 12;
}
```



2. 使用protoc命令将.proto文件转化为相应的c++文件

   ```c++
   protoc test.proto --cpp_out=./
   ```

   

   1. 源文件：xxx.pb.cc —> xxx对应的名字和.proto文件名相同
   2. 头文件：xxx.pb.h —> xxx对应的名字和.proto文件名相同

3. 需要将生成的c++文件添加到项目中，通过文件中提供的类API实现数据的序列化/反序列化



### 序列化和反序列化

#### 通过protoc生成的文件中有一个头文件，其中有个类名和message关键字后定义的名字相同，.proto文件中message中成员就是生成的类的私有成员

#### 如何访问生成类的私有成员？

可以调用生成的类提供的公共成员函数：

- 初始化：clear_变量（）
- get：变量（）
- set：set_变量（）
- 拿到变量地址：mutable_变量（）
- 数组变量
  - 元素个数：变量_size（）
  - 新增元素：add_变量（）

#### 序列化

##### 将需要传输的数据转化成二进制格式的过程。在计算机科学中，序列化通常用于将内存中的对象持久化存储到磁盘上，或者在分布式系统中进行数据传输和通信。

```c++
// --- 将序列化的数据 数据保存到内存中
// 将类对象中的数据序列化为字符串, c++ 风格的字符串, 参数是一个传出参数
bool SerializeToString(std::string* output) const;
// 将类对象中的数据序列化为字符串, c 风格的字符串, 参数 data 是一个传出参数
bool SerializeToArray(void* data, int size) const;

// ------ 写磁盘文件, 只需要调用这个函数, 数据自动被写入到磁盘文件中
// -- 需要提供流对象/文件描述符关联一个磁盘文件
// 将数据序列化写入到磁盘文件中, c++ 风格
// ostream 子类 ofstream -> 写文件
bool SerializeToOstream(std::ostream* output) const;
// 将数据序列化写入到磁盘文件中, c 风格
bool SerializeToFileDescriptor(int file_descriptor) const;
```

#### 反序列化

##### 反序列化是将序列化后的二进制数据转化成我们需要的原本的数据的过程。

```c++
bool ParseFromString(const std::string& data) ;
bool ParseFromArray(const void* data, int size);
// istream -> 子类 ifstream -> 读操作

// w->写 o: ofstream , r->读 i: ifstream
bool ParseFromIstream(std::istream* input);
bool ParseFromFileDescriptor(int file_descriptor);
```

