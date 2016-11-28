---
layout: post
title: linux中解析命令行参数 --getopt_long用法
date: 2016-11-28
categories: C语言
tags: [Linux,学习,C语言]
description: linux C
---


这几天在看Linux下的usb摄像头采集视频，看到网上的
[Linux视频采集](http://blog.csdn.net/zgyulongfei/article/details/7526249)	
中有一个选项函数，getopt_long，寻根问底，学习一下，掌握新技能。

一.介绍

	函数功能：支持长选项的命令解析

	函数所在头文件：  #include <getopt.h>

	函数原型定义：

	int getopt_long(int argc, char * const argv[],const char *optstring, const struct option *longopts,int *longindex)

说明：

	argc和argv：通常直接从main()到两个参数传递而来。

	optsting：

	1. 单个字符，表示选项， 

	2. 单个字符后接一个冒号：表示该选项后必须跟一个参数。参数紧跟在选项后或者以空格隔开。该参数的指针赋给optarg。 

	3. 单个字符后跟两个冒号，表示该选项后可以有参数也可以没有参数。如果有参数，参数必须紧跟在选项后不能以空格隔开。该参数的指针赋给optarg。（这个特性是GNU的扩张）。

	optstring是一个字符串，表示可以接受的参数。例如，"a: b: cd"，表示可以接受的参数是a,b,c,d，其中，a和b参数后面跟有更多的参数值。(例如：-a host --b name)。

longopts：指向数组的指针，这个数组是option结构数组，option结构称为长选项表，其声明如下：

	struct option {
	              const char *name;
	              int has_arg;
	              int *flag;
	              int val;
	          };

	name：是长选项的名字

has_arg值为0、1、2，分别表示没有参数、有参数、参数可选，

	no_argument            0            选项没有参数
	
	required_argument      1            选项需要参数
	
	optional_argument      2            选项参数是可选的

flag：

	如果该指针为NULL，那么getopt_long返回val字段的值；

	如果该指针不为NULL，那么会使得它所指向的结构填入val字段的值，同时getopt_long返回0。

int val：

	如果flag是NULL，那么val通常是个字符常量，如果短选项和长选项一致，那么该字符就应该与optstring中出现的这个选项的参数相同；

	或者说每一个结构体包含4个域，第一个域为长选项字符串（--help），第二个域是一个标识，只能为0、1或2，分别代表没有、有和可选。第三个域永远为NULL。第四个域为对应的短选项字符串（-h）。

longindex：

	一般赋为NULL即可；如果没有设置为NULL，那么它就指向一个变量，这个变量会被赋值为寻找到的长选项在longopts中的索引值，这可以用于错误诊断。

options类型参数可以有两种方式：

	  （1）短选项（short options）:顾名思义，就是短小参数。它们通常包含一个连字号和一个字母（大写或小写字母）。例如：-s，-h等。

	（2）长选项（long options）：长选项，包含了两个连字号和一些大小写字母组成的单词。例如，--size，--help等。

 注：一个程序通常会提供包括short options和long options两种参数形式的参数。

  对于其他类型参数的说明：
  
  这种类型的参数，通常跟随在options类型参数之后。例如，ls –s /功能为显示root目录的大小。’/’这个参数告诉ls要显示目录的路径。

![这里写图片描述](http://img.blog.csdn.net/20161128105029760)

二.getopt_long()函数使用规则：

（1）使用前准备两种数据结构

  字符指针型变量：
  
  该数据结构包括了所有要定义的短选项，每一个选项都只用单个字母表示。如果该选项需要参数（如，需要文件路径等），则其后跟一个冒号。例如，三个短选项分别为‘-h’‘-o’‘-v’，其中-o需要参数，其他两个不需要参数。那么，我们可以将数据结构定义成如下形式：
const char * const shor_options = “ho:v” ;

static const char short_options[] = "d:hmru";

  struct option 类型数组：
  
  该数据结构中的每个元素对应了一个长选项，并且每个元素是由四个域组成。通常情况下，可以按以下规则使用。

		第一个元素，描述长选项的名称；

		第二个选项，代表该选项是否需要跟着参数，需要参数则为1，反之为0

		第三个选项，可以赋为NULL；

		第四个选项，是该长选项对应的短选项名称。

		最后一个元素，要求所有域的内容均为0，即{NULL,0,NULL,0}。

eg:

		static const struct option long_options[] = {
			{ "device", required_argument,NULL, 'd' },
			{ "help", no_argument, NULL, 'h' }, 
			{ "mmap", no_argument,NULL, 'm' }, 
			{ "read", no_argument, NULL, 'r' },
			{ "userp", no_argument,NULL, 'u' }, 
			{ 0, 0, 0, 0 }
		};

（2）调用方法

则调用方式可为：

	getopt_long( argc, argv, short_options, long_options, NULL);

（3）几种常见返回值

	   (a)每次调用该函数，它都会分析一个选项，并且返回它的短选项，如果分析完毕，即已经没有选项了，则会返回-1。

	   (b)如果getopt_long()在分析选项时，遇到一个没有定义过的选项，则返回值为‘?’，此时，可以打印出所定义命令行的使用信息给用户。

	   (c)当处理一个带参数的选项时，全局变量optarg会指向它的参数 

	   (d)当函数分析完所有参数时，全局变量optind（into argv）会指向第一个‘非选项’的位置.

三、小例子

	#include <stdio.h>
	#include <getopt.h>
	
	char *l_opt_arg;
	char* const short_options = "nbl:";
	
	struct option long_options[] = {
		{"name", 0, NULL, 'n'},
		{"bf_name",0, NULL, 'b'},
		{"love", 1, NULL, 'l'},
		{0, 0, 0, 0},
	};
	
	int main(int argc, char *argv[])
	{
		int c;
		
		
		while((c = getopt_long (argc, argv, short_options, long_options, NULL)) != -1){
		switch (c)
		{
			case 'n':
				printf("My name is XL.\n");
			break;
			
			case 'b':
				printf("His name is ST.\n");
			break;
			
			case 'l':
			l_opt_arg = optarg;
				printf("Our love is %s!\n", l_opt_arg);
			break;
			
			default:
				printf("This is default option!\n");
			break;
		 }
	  }
	
		 return 0;
	}

现象：

![这里写图片描述](http://img.blog.csdn.net/20161128130006156)

参考：

[详细解析命令行的getopt_long()函数](http://www.thinksaas.cn/topics/0/71/71671.html)

[linux 中解析命令行参数 （getopt_long用法） ](http://www.cnblogs.com/hnrainll/archive/2011/09/15/2176933.html)

