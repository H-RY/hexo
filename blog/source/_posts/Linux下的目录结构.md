---
title: Linux下的目录结构
date: 2025-01-06 21:20:18
tags:
---
## Linux下的目录结构

### /：根目录，包含所有内容

### /bin：放shell，可执行文件，主要是busybox

### /dev：设备文件

### /etc：配置文件，被linuxrc调用

### /usr：系统用户文件，busybox安装时默认创建（可删）

### /home：用户个人数据

### /lib：当前操作系统的动态和静态链接库文件

### /sbin：只能root权限的bin，链接指向/bin/busybox

### /tmp：临时文件

### /var：变量

### /boot：启动

### /proc：进程和内核文件

### /opt：可选

### /root：根目录的主目录

### /media：可移动媒体

### /mnt：挂载（可删）

### /srv：服务数据

### /sys：虚拟文件系统，不可省略，但空文件夹即可



