---
title: protobuf从0开始到理解
date: 2025-01-07 15:35:27
tags:

---

### Protobuf 概述

protobuf 也叫 protocol buffer 是 google 的一种数据交换的格式，它独立于语言，独立于平台（什么编程语言，什么平台都可以用），由于它是一种二进制的格式，比使用xml、json进行数据交换快很多。可以用于分布式应用之间的数据通信，或者异构环境下的数据交换。

大白话就是能自动生成各种各样编程语言的结构体和类，我定义了一个结构体和类（对proto来说是.proto），然后我需要传输这些数据，我就用protobuf提供的工具生成对应的代码直接用.

### 生成对应源码步骤

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

   注意：需要protobuf的头文件和库才能用这些生成的文件哦！

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



### 如何编译一个protobuf库

#### 每一个三方库要编译，当然要先把他拉取下来，找到项目的网址，clone下来会发现他项目的根目录没有CMakeList.txt，但是有MakeFile，然后他还有一个cmake文件夹里面放的是CMakeList.txt。猜测是以前用MakeFile直接编译，后来引入了cmake但是也没有删除MakeFile的编译方式，开始对着文档操作

```git
git clone https://github.com/protocolbuffers/protobuf.git #网址不一定是对的
cd protobuf
git submodule update --init --recursive
sudo apt-get install autoconf automake libtool curl make g++ unzip
```

##### 1. 执行./autogen.sh

`./autogen.sh` 是一个脚本文件，通常用于在使用 GNU 构建系统（如 `autoconf`、`automake` 等）开发的项目中，自动生成构建所需的配置文件和其他文件。

**为什么执行 `./autogen.sh`**

`autogen.sh` 脚本通常会执行以下步骤：

- **检查依赖**：确保系统上安装了构建该项目所需的工具和库。
- **生成 `configure` 脚本**：`autogen.sh` 通常会调用 `autoconf`，生成一个 `configure` 脚本。`configure` 脚本用于检查当前系统环境，并生成适合该系统的 Makefile 等构建文件。
- **生成 `Makefile.in` 文件**：`autogen.sh` 还可能调用 `automake`，以确保 `Makefile.in` 文件是最新的。
- **其他自动化步骤**：根据项目配置，还可能会涉及其他自动化任务，如更新 `aclocal.m4` 等文件。



##### 2. 执行./configure

`./configure` 是一个用于准备构建项目的脚本，通常在使用 **GNU 构建系统**（如 `autoconf`、`automake` 等）开发的项目中，在执行 `./autogen.sh` 或从源码获取之后，需要执行它来为项目配置合适的编译环境和构建参数。即生成MakeFile文件

`./configure` 脚本主要用于：

- **检查编译环境**：它会检查你的系统环境，确认是否安装了项目编译所需的依赖库和工具。比如检查是否有 `gcc`（GNU 编译器）、`make` 工具、其他库的存在。
- **自动配置编译选项**：根据系统的具体情况（操作系统、硬件架构、已安装的软件包等），`./configure` 会生成适合该系统的 `Makefile` 文件（编译规则文件）。`Makefile` 文件用于指导 `make` 工具如何编译和安装程序。
- **为项目定制配置**：你可以传递不同的选项给 `./configure`，例如指定安装目录、启用或禁用某些功能等。这样可以根据你的需求来定制编译过程。



##### 3. make或进入cmake文件夹通过cmake生成MakeFile

protobuf有两种编译方式，一种是直接进行make，另一种是进入cmake文件夹后使用cmake构建。

**如果使用make**

`make` 是一个自动化构建工具，用于根据 `Makefile` 中的规则来编译和构建程序。它通常是在执行了 `./configure` 后使用的，`./configure` 会生成一个 `Makefile`，然后你使用 `make` 来实际执行构建操作。

此处由于我们需要的库需要通过交叉编译生成，故需要更改MakeFile文件中，指定工具链，或更改./configure中让生成的MakeFile就是已经指定好工具链的，但是此处不生效，故使用cmake构建。

`Makefile` 中定义了规则，告诉 `make` 如何构建文件。一个典型的 `Makefile` 结构包括：

- **目标（Target）**：要生成的文件，如可执行文件、库文件等。
- **依赖（Dependency）**：目标所依赖的源文件或中间文件。
- **命令（Command）**：用于生成目标的命令，通常是编译或链接命令。

**如果使用cmake**

指定工具链后执行对应命令

```cmake
cmake -DCMAKE_TOOLCHAIN_FILE=../toolchain/R328.cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_SHARED_LIBS=ON -DCMAKE_CXX_FLAGS="-O3 -g0 -s" -DCMAKE_C_FLAGS="-O3 -g0 -s" ..
```

注意工具链需要放在对应路径，如此处工具链的cmake放在../toolchain/中

##### 4. make check

在构建之后验证程序是否按预期工作，`make` 会根据 `Makefile` 中的定义，执行相应的测试命令或脚本。通过自动化测试，`make check` 可以帮助确保代码质量，防止程序在后续版本中出现回归问题。执行 `make check` 后，你可以查看测试结果，根据失败的测试信息进一步修复代码或环境配置。

##### 5. make install

`make install` 是一个用来将已经构建好的程序安装到系统中指定位置的命令。它通常是构建和安装过程中的最后一步，执行该命令后，程序会被复制到系统的适当目录，如可执行文件、库文件、文档等。通常需要 `sudo**`**

**什么情况下执行make install**

问题：为什么我编译好了protobuf，还要make install？我为何不直接在项目内部用已经编译好的呢？

解答：对于一些程序和依赖库，我们make install是为了给别的程序用，因为make install会把可执行文件放到bin文件夹下，库文件放到lib下，总之都是环境变量中，这样别的程序用的时候，直接自己在环境变量里找就好，比如此处的protoc命令，如果不执行make install，你只能手动设置环境变量，如果没加上protoc依赖的库，还可能出错，但是make install后，直接使用该命令就可以了。



综上，protobuf作为数据格式的精髓就全部说完了，类比现实世界就是，各式各样的编程语言，最终到cpu中都是通过二进制来运算，这也是protobuf能实现的最重要的条件。
