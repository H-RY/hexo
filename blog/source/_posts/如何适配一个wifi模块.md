---
title: 如何适配一个wifi模块
date: 2025-02-18 10:03:07
tags:
---

### 如何适配一个wifi模块

一个cpu拿过来，需要整体进行调试，此处只说明一个全新的cpu和一个wifi模块通用的适配过程

#### 第一步：将wifi模块的源码导入cpu的系统源码对应目录，再添加config

首先导入之前，需要make clean清除wifi模块之前编译的结果

其次需要找到cpu中对应放入模块源码的路径在哪里，此处有模块驱动代码文件夹module_driver，所以进入该文件夹找

![image-20250218101237036](C:\Users\Blackvision\AppData\Roaming\Typora\typora-user-images\image-20250218101237036.png)

因为君正平台做过移远rtl8723ds的适配，所以进入该文件夹后全局搜索，发现在devices/wireless/路径下，故找到源码路径，将rtl8723cs的源码放入并改名字

加入源码后，还要改源码配置项，根据IConfig中名字（通过grep -rn "rtl8723ds wireless cards support"）搜到路径，然后根据ds的去配置cs，如果此时想编译驱动，需要在配置项中勾选你新配置的再编译

![image-20250218161244158](C:\Users\Blackvision\AppData\Roaming\Typora\typora-user-images\image-20250218161244158.png)



#### 第二步：配置wifi模块适配对应cpu平台

![image-20250218110352162](C:\Users\Blackvision\AppData\Roaming\Typora\typora-user-images\image-20250218110352162.png)

第一行是编系统时，编译到对应wifi模块驱动时，发现该驱动适配的是全志平台，但是现在平台是君正，所以理论上应该让君正的小伙伴帮我们做适配，但是因为rtl8723cs和rtl8723ds很像，而君正有现成的8723ds的例子，所以可以对着8723ds去修改8723cs的Makefile

一般要找到对应cpu平台配置，然后再加上判断对应配置的逻辑，最后在构建module的时候做修改和确认

这样就可以编译系统，然后查看该wifi驱动有没有进行编译了

#### 问题1：编译系统的时候报错找不到linux/wlan_plat.h

![image-20250218221042900](C:\Users\Blackvision\AppData\Roaming\Typora\typora-user-images\image-20250218221042900.png)

1. 首先发现这个头文件是linux下的，说明是内核文件。然后全局搜这个头文件，发现我们所使用的内核文件夹中根本就不存在这个文件，但是老版的文件夹下都有这个文件，于是进一个老的linux版本看这个文件内容是什么

![image-20250218221915189](C:\Users\Blackvision\AppData\Roaming\Typora\typora-user-images\image-20250218221915189.png)

发现就是一个结构体，所以可以把这个定义直接加进我们现在的这个rtw_android.c这个c文件中

2. 在rtw_android.c这个文件中搜索linux/wlan_plat.h会发现选择哪一个头文件跟linux内核版本有关，正常我们看内核版本都是cat /proc/version或者uname -r，但是这个是系统中的系统，所以要看内核代码的Makefile，一般都会有，如果没有，还可以看menuconfig选中的对应型号，有的也会标在这里

然后我们就发现了，我们是5.10的内核版本，但是5.10已经没有这个头文件了，所以我们要修改代码，让我们的内核不再包含这个头文件。

![image-20250218221850606](C:\Users\Blackvision\AppData\Roaming\Typora\typora-user-images\image-20250218221850606.png)

然后这时候编译会报另一个错误，如下

![image-20250218221950862](C:\Users\Blackvision\AppData\Roaming\Typora\typora-user-images\image-20250218221950862.png)

说我们调用这个函数的时候，参数给多了，发现这个函数就是我们引入的wlan_plat.h中定义的，然而这里面定义的这个函数只有一个参数，但是因我们是5.10的内核，会给两个参数，所以在return处只需要返回一个参数，函数的参数列表不用，因为传了不用就行。

![image-20250218222118320](C:\Users\Blackvision\AppData\Roaming\Typora\typora-user-images\image-20250218222118320.png)

至此，编译通过。另外这个问题也告诉我们，一切都看报错，是有迹可循的。



#### 验证驱动是否加载成功！

![image-20250218222637887](C:\Users\Blackvision\AppData\Roaming\Typora\typora-user-images\image-20250218222637887.png)

首先驱动源代码的目录下有.o文件，说明编译了，不编译.c是不会变成.o的，其次刷进板子后，dmesg可以看驱动是否加载成功



#### 第三步：添加wifi模块固件，再添加config

固件就是专门为硬件编写的程序，有了驱动相当于有了接口，但是还需要接口的具体实现，也就是固件，可以控制wifi的各种功能，为什么要把wifi固件放到linux系统中，让cpu去加载到wifi模块的内存中呢，因为好维护，不然我们调cpu发现wifi固件有问题，还要去接wifi固件调，另外原厂很多东西都会写死，就是为了可以尽量不懂模组本身

找到固件后放入君正对应的路径下，因为此时有ds作为例子，所以直接cp ds 为 cs

![image-20250219092854817](C:\Users\Blackvision\AppData\Roaming\Typora\typora-user-images\image-20250219092854817.png)

![image-20250219092821872](C:\Users\Blackvision\AppData\Roaming\Typora\typora-user-images\image-20250219092821872.png)

修改config.in和Makefile还有所有出现了rtl8723ds的地方为rtl8723cs

然后添加编译选项

![image-20250219093325054](C:\Users\Blackvision\AppData\Roaming\Typora\typora-user-images\image-20250219093325054.png)



#### 第四步：配各种选项

都配好之后，就开始进图形化界面搞事情了



