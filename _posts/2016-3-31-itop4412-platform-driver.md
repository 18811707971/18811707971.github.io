---
layout: post
title: 以platform总线方式注册杂项设备驱动
date: 2016-03-21
categories: Linux
tags: [Linux,Driver,Bus,Device]
description: 4412 driver study

---
 
1.编写杂项设备驱动程序以及Makefile文件，编译后生成的.o文件和驱动程序是添加到内核的必备材料。

2.在内核目录的字符驱动下修改Kconfig文件（仿写，有编译进入内核，模块，不添加三种

![这里写图片描述](http://img.blog.csdn.net/20161127101158156)

这里和make menuconfig的操作界面匹配

3.修改字符驱动目录下的Makefile文件（仿写），下面的是选择编译，有的是强制编译进入内核

![这里写图片描述](http://img.blog.csdn.net/20161127101223801)

1.注册驱动的时候，系统会通过 platform_match 函数匹配设备和驱动。

2.注册设备的结构体为 platform_device，注册驱动的结构体为 platform_driver。设备和驱动结构体的成员 name 字段，相同则匹配，如果匹配了则会调用 platform_driver 中的 probe 函数，注册驱动。

3.在虚拟总线上注册设备：进入解压之后的内核文件夹“iTop4412_Kernel_3.0”，使用命“vim include/linux/platform_device.h”打开“platform_device”所在文件

```c
structplatform_device {
        const char      * name;
        int             id;
        struct device   dev;
        u32             num_resources;
        struct resource * resource;
 
        const struct platform_device_id*id_entry;
 
        /* MFD cell pointer */
        struct mfd_cell *mfd_cell;
 
        /* arch specific additions */
        struct pdev_archdata    archdata;
};

```
 
第一个参数“name”，是一个字符指针，驱动初始化前需要和注册驱动的“name”字段匹配的参数；

第二个参数“id”，表示子设备编号，一个设备如果有多个子设备号，则需写入子设备号数量，如果只有一个则用-1 表示；

第三个参数“device”，表示结构体内嵌的设备结构体；

第四个参数 num_resource，表示设备使用的资源数组。

在大多数驱动中，需要我们写的只有设备名*name 和设备编号id 。
 
4.添加设备到平台总线：在内核文件夹目录下，用vim arch/arm/mach-exynos/mach-itop4412.c”，打开平台文件，添加设备，仿写即可

![这里写图片描述](http://img.blog.csdn.net/20161127101338489)

![这里写图片描述](http://img.blog.csdn.net/20161127101416255)

保存退出，重新编译内核，烧写到开发板。
开发板启动之后，使用命令“ls /sys/devices/platform/”可以查看到新注册的设备

