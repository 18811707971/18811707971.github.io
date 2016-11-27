---
layout: post
title: 基于迅为4412精英版连接SIM7100C模块Android下调试——RIL库分析（一）
date: 2016-10-23
categories: 4G
tags: [Android,RIL]
description: about sim7100c study

---

1.概念介绍

	Android电话系统围绕底层使用Modem硬件来搭建，提供呼叫、短信和网络连接功能，其中modem也称为基带。Modem驱动程序包含在Linux内核层中,所以3G/4G模块的搭建其实就是电话系统的搭建 。
	主流3G/4G模块：主要分有 内置(集成在处理器中如高通等，内置模块主要通过共享内存与处理器通信）；  外置(较常见，大多通过usb转串口接口并利用AT命令与处理器通信,usb串口一般使用标准的驱动,AT命令由Hayes公司发明，一种调制解调器命令语言。
	
	
2.基本架构概述
    Android RIL (Radio Interface Layer)提供了Telephony服务和Radio硬件之间的抽象层。RIL负责数据的可靠传输、AT命令的发送以及response（响应）的解析。一般的，应用处理器（AP）通过AT命令集与无线通讯模块（基带/BP）通信。通信的方式又分为主动请求的request(诸如拨号、发短信……)，以及Modem主动上报的例如信号强度、基站信息、来电、来短信等，称之为unsolicitedresponse（未经请求的响应）。
   
   电话系统大体框架）:分三大层，硬件(连接modem设备)，Android系统（主要包括了Linux内核层，运行库层和Framework层),平台API（即再往上为应用层）。
       
   ![电话系统大体框架](http://img.blog.csdn.net/20161009100641850)
   
   具体实现自下到上：Modem驱动，RIL库 、RIL守护进程，电话JAVA框架和电话应用(其中电话系统部分无JNI) 。
    Kernel Space 属于驱动层在内核中实现，一般分为AT命令通道和数据通道两路接口。   
   RIL层：贯穿Android的内核层至应用层，由三部分组成：RIL守护进程(本质是rild可执行程序，开机通过init启动，与framework层主要通过socket来进行）, libril库和ril实现库(电话层硬件抽象层)。主要负责负责数据的可靠传输、AT命令的发送以及response(来自modem)的解析 。
   
   ![这里写图片描述](http://img.blog.csdn.net/20161009164434526)

RIL接口下层结构：ril实现库的接口非常复杂，需要处理的命令，相关结构体比较多，其接口主要定义在 hardware/ril/include/telephony 目录的ri.h中。    rild socket:与框架层进行通信   
 RIL_Env: 主要用于请求完成函数、上报消息响应函数和周期行处理函数三个功能。
 ril硬件抽象层主要实现 ril命令与AT命令的转换。
 
   ![这里写图片描述](http://img.blog.csdn.net/20161009163305865)

3.Modem与AP通讯

 在Android系统中rild运行在AP上，AP上的应用通过rild发送AT指令给BP，BP接收到信息后又通过rild传送给AP。AP与BP之间有两种通信方式：

 1.Solicited Response:Ap向Bp发送请求，Bp给Ap发送回复,该类型的AT指令及其回调函数以数组的形式存放在Ril_commands.h文件中；

 2.unSolicited Response:Bp主动给Ap发送事件,该类型的AT指令及其回调函数以数组的形式存放在ril_unsol_commands.h文件中。

不同手机厂商使用的AT命令不完全相同，AP与BP之间通过各厂商自己的相关动态库来通信。

![这里写图片描述](http://img.blog.csdn.net/20161009101453302)

目前市面上的3G/4G modem和主机的连接方式主要有串口、USB和mini-PCIE，串口模块比较传统且使用比较简单。加载驱动后，3G/4G模块的usb口会映射为ttyUSB*的形式。

4.Android RIL组成

		Android RIL可以分成2个模块，一个部分RIL Demon(RILD)，用于通过socket和framework通讯；另一部分是第三方自己客制化的部分，暂时称之为vendor RIL。这样设计是因为不同的厂商使用的Modem不一样，而RIL又和Modem紧密联系，所以Android有把和Modem联系紧密的部分和公共部分剥离开，让不同的厂商可以客制化vendor RIL以适应厂商自己的Modem。Vendor RIL专门负责通过AT和Modem进行通讯。可以细化称为:

![这里写图片描述](http://img.blog.csdn.net/20161009160806635)
		
RIL模块由rild守护进程、libril.so、librefrence.so三部分组成：

1.rild模块:
被编译为一个可执行文件，RIL守护进程，开机时被init守护进程调用启动，实现一个main函数作为整个ril模块的入口点。
在初始化时使用dlopen打开librefrence-ril.so，从中取出并执行RIL_Init函数，得到RIL_RadioFunctions指针，通过RIL_register()函数注册到libril.so库中，其源码结构如下：

![这里写图片描述](http://img.blog.csdn.net/20161009161441341)

在rild.c文件中，将完成ril的加载过程，它会执行如下操作：

a. 动态加载Vendor RIL的.so文件;

b.执行RIL_startEventLoop()开启消息队列以进行事件监听;
  
c.  通过执行Vendor RIL的rilInit()方法来进行Vendor RIL与libril的关系建立。

d.在rild文件夹中还包括一个radiooptions.c文件,它的作用是通过串口将一些radio相关的参数直接传给rild来对radio进行配置。radiooptiongs通过获取启动参数， 利用socket与rild通信，可供调试时配置Modem参数。


2. libril.so：

主要负责同上层的通信工作，接收ril的请求，并传递给librefrence_ril.so，同时将librefrence_ril.so返回的消息送给调用进程。与 rild结合相当紧密，是其共享库，编译时就已经建立了这一关系。源码结构如下所示：

![这里写图片描述](http://img.blog.csdn.net/20161009161959103)

a.在编译时libril.so被链入rild,它为rild提供了event处理功能，还提供了在rild与Vendor RIL之间传递请求和响应消息的能力。

b.libril.so提供的主要功能分布在两个主要方法内，一个RIL_startEventLoop()方法，另一个是RIL_register()方法。

c.RIL_startEventLoop()方法所提供的功能就是启用eventLoop线程，开始执行RIL消息队列。

d.RIL_register()方法的主要功能是启动名为 rild 的监听端口，等待java 端通过socket进行连接。

3.librefrence_ril.so：

是由各手机厂商自己实现，在rild进程运行中通过dlopen方式加载，主要负责跟modem硬件通信，转换来自libril.so的请求为AT命令，并且将AT指令写入radio中。同时监听Modem的反馈信息给libril.so。在初始化时， rild通过符号RIL_Init获取一组函数指针并以此与之建立联系。源码结构如下所示：

![这里写图片描述](http://img.blog.csdn.net/20161009162555863)

Android自带的Vendor RIL的参考实现。被编译成.so文件，由于本部分是厂商定制的重点所在。所以被设计为松散耦合，且可灵活配置的。reference-ril会接收调用者传来的参数，参数内容为与radio的通信方式。如通过串口连接radio,那么参数为这种形式：-d /dev/ttyUSB*。

5.参考：

http://blog.csdn.net/yangwen123/article/details/8914291

http://blog.csdn.net/hanbo622/article/details/42520007

http://blog.csdn.net/tronteng/article/details/39031615

http://46aae4d1e2371e4aa769798941cef698.devproxy.yunshipei.com/column/details/ril-3g.html

 http://blog.csdn.net/jscese/article/details/40046493

http://www.cnitblog.com/luofuchong/archive/2011/05/28/74190.html

今天先开个头，继续加油！
以上内容多参考网上的诸位前辈的博客，谢谢你们！