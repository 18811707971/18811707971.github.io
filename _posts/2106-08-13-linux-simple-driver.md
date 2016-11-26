---
layout: post
title: Linux最简驱动
date: 2016-08-13
categories: Linux
tags: [ITOP4412,LINUX]
description: about driver study

---

一、实验目的：编写Linux最简驱动

二、实验平台
		迅为itop4412开发板（开发板运行Linux最小系统）

三、实验流程:
(1)了解最简驱动：包含头文件；模块信息声明；模块驱动的入口、出口;功能实现四个部分。

 Linux头文件：它们是在Linux源码目录下的：include/linux/
 
信息声明：所有的Linux 代码必须遵循GPL 协议，如果不声明GPL 协议，模块将无法在Linux 中使用。

– MODULE_LICENSE(_license)添加遵循GPL协议，必选！

– MODULE_AUTHOR(_author)代码作者，可选！

– 入口函数module_init(x)

– 出口函数module_exit(x)


(2)实现代码：

```c
	/*包含初始化宏定义的头文件,代码中的module_init和module_exit在此文件中*/
	#include <linux/init.h>
	/*包含初始化加载模块的头文件,代码中的MODULE_LICENSE在此头文件中*/
	#include <linux/module.h>

	/*初始化*/
	static int __init hello_init(void)
	{
		printk(KERN_EMERG "HELLO WORLD enter!\n");
		return 0;
	}

	/*退出*/
	static void __exit hello_exit(void)
	{
		printk(KERN_EMERG "HELLO WORLD exit!\n");
		
	}
	/*入口*/
	module_init(hello_init);
	/*出口*/
	module_exit(hello_exit);

	/*声明*/
	MODULE_LICENSE("Dual BSD/GPL");/*遵循的协议;必须的*/
	MODULE_AUTHOR("star sky");
	MODULE_DESCRIPTION("Linux the simplest driver");
	MODULE_VERSION("V1.0");
```

(3)Makefile
 单独编译模块的话，需要我们写一个Makefile文件，正如迅为的教程说的一样，我们学会仿写即可。
 
```bash
	#!/bin/bash
	#通知编译器我们要编译模块的哪些源码
	#这里是编译itop4412_hello.c这个文件编译成中间文件itop4412_hello.o
	obj-m += mini_linux_module.o 
	
	#源码目录变量，这里用户需要根据实际情况选择路径
	#作者是将Linux的源码拷贝到目录/home/topeet/android4.0下并解压的
	KDIR := /home/topeet/android4.0/iTop4412_Kernel_3.0
	
	#当前目录变量
	PWD ?= $(shell pwd)
	
	#make命名默认寻找第一个目标
	#make -C就是指调用执行的路径
	#$(KDIR)Linux源码目录，作者这里指的是/home/topeet/android4.0/iTop4412_Kernel_3.0
	#$(PWD)当前目录变量
	#modules要执行的操作
	all:
		make -C $(KDIR) M=$(PWD) modules
			
	#make clean执行的操作是删除后缀为o的文件
	clean:
		rm -rf *.o
```

(4)编译
流程分析：

 ![这里写图片描述](http://img.blog.csdn.net/20160813212708613)

 我们将最简驱动和Makefile文件拷贝到ubuntu下，注意放到一个文件夹，然后make，成功编译后，文件夹下就有驱动模块：.ko文件。这样最简驱动就完成了。

(5)测试
	使用U盘或者TF卡，当然也可以使用更方便的tftp下载或者NFS系统方式，将.ko文件拷贝到开发板上.加载命令为：insmod ;查看命令：lsnod ;卸载命令:rmmod.
		加载：insmod XXX.ko
	可在终端看到：HELLO WORLD enter!
		卸载：
	可在终端看到：HELLO WORLD exit!
	这样最简驱动编写就完成了！！

四、感谢：
	此次实验参照迅为驱动教程进行操作。
	
