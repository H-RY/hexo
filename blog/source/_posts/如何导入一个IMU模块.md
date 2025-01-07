---
title: 如何导入一个IMU模块
date: 2025-01-07 15:13:13
tags:
---

## 如何导入一个IMU模块

### 导入驱动

1. 将驱动源码放入对应路径
2. 更改Kconfig（对应menuconfig选项）
3. 更改Makefile文件，增加源码编译
4. make kernel_menuconfig 选上对应选项

### 配置该模块使用的接口

如：此处IMU配置SPI（IMU的文档规定）

1. 设备树配置SPI节点（对应原理图）（cdts）（sunxi为linux通用）
   1. status需要改为okey（不需要改为disabled）
   2. sys.config.fex优先级高于设备树，所以想设备树生效需注释对应sys.config.fex
2. 配置pin脚（muxsel为function，看的时datasheet）（pull中拉低是0，拉高是1），此处片选需要拉高（SPI有四个配置项，CS片选，CLK时钟，MISO和MOSI）
3. 设备树中添加IMU配置节点

