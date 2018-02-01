---
layout: post
title: SPI专题（一）——基础知识
date: 2017-08-23
categories: SPI
tags: [SPI,DRIVER,BUS]
description: SPI STUDY AND APPLICATION
---

## **1.概念介绍**

**SPI**（Serial Peripheral Interface）是串行外围设备接口。SPI，是一种**高速的，全双工，同步**的通信总线，并且在芯片的管脚上只占用四根线，节约了芯片的管脚，同时为PCB的布局上节省空间，提供方便，正是出于这种简单易用的特性，现在越来越多的芯片集成了这种通信协议。常用于单片机和 EEPROM、FLASH、实时时钟、数字信号处理器等器件的通信。 

## **2.特点**

#### **2.1 连接方式**

SPI是一个环形总线结构，由**设备选择线（CS）、时钟线（SCK）、串行输出数据线（MOSI）、串行输入数据线（MISO）**构成，其时序其实很简单，主要是在sck的控制下，两个双向移位寄存器进行数据交换。

![这里写图片描述](http://img.blog.csdn.net/20170823154950788?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

（1）**MOSI：**主器件数据输出，从器件数据输入
（2）**MISO：**主器件数据输入，从器件数据输出
（3）**SCLK ：**时钟信号，由主器件产生
（4）**/SS（CS）：**从器件使能信号，由主器件控制

#### **2.2 模式**

SPI 用**主从模式（Master Slave）架构**；支持多slave模式应用，一般仅支持单Master。时钟由Master控制，**在时钟移位脉冲下，数据按位传输，高位在前，低位在后（MSB first**）；SPI接口有 2 根**单向**数据线，为**全双工通信**，目前应用中的数据速率可达几Mbps的水平。

![这里写图片描述](http://img.blog.csdn.net/20170823093427421?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

主片在访问某一从片时，必须使该从片的片选信号有效；主片在 SCK 信号的同步下，通过 MOSI 线发出指令、地址信息；如需将数据输出，则接着写指令，由 SCK 同步在 MOSI 线上发出数据；如需读回数据，则接着读指令，由主片发出 SCK，从片根据 SCK 的节拍通过 MISO 发回数据。对具有SPI接口的从片器件来讲，SCK、MOSI 是输入信号，MISO 是输出信号。

## **3.内部结构**

![这里写图片描述](http://img.blog.csdn.net/20170823094340763?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

工作原理：主机和从机内部都有一个串行的移位寄存器，主机向其串行移位寄存器中写入一个字节发起传输，寄存器通过MOSI信号线将字节发送到从机，从机也将自己的移位寄存器一个字节发送给主机，从而实现两个移位寄存器的一个字节交换。

## **4.通信方式**

#### **4.1.通信协议**

SPI 是单主设备通信协议，这意味着总线中只有一个中心设备能发起通信，当SPI主设备想读/写从设备时，它要**拉低从设备对应的CS线**（CS是低电平有效的）。主机和从机都有一个串行移位寄存器，主机通过向它的SPI串行寄存器写入一个字节来发起一次传输。寄存器通过MOSI信号线将字节传送给从机，实现“写”的功能。从机也将自己的移位寄存器中的内容通过MISO信号线返回给主机，实现“读”的功能。这样，两个移位寄存器中的内容就被交换，**外设的写操作和读操作是同步完成的**。如果只进行写操作，主机只需忽略接收到的字节；反之。若主机要读取从机的一个字节，就必须发送一个空字节来引发从机的传输。

#### **4.2.操作模式**

SPI有四种操作模式，**模式0、模式1、模式2、模式3**其中使用的最为广泛的是 SPI0 和 SPI3 方式。它们的区别是定义了在时钟脉冲的哪条边沿转换输出信号，哪条边沿采样输入信号，还有时钟脉冲的稳定电平值(就是时钟信号无效时是高还是低)。**每种模式都由参数时钟极性CPOL和时钟相位CPHA刻画。****时钟极性设置时钟空闲时的电平，时钟相位设置读取数据和发送数据的时钟沿。**通信双方必须是工作在同一模式下，CPOL 与 CPHA 的选项可实现时钟信号 180 度的相移以及半个时钟周期的数据延迟。（CPHA = 0表示在每个周期的第一个时钟沿采样； CPHA = 1表示在每个周期的第二个时钟沿采样。）

时钟极性：

![这里写图片描述](http://img.blog.csdn.net/20170823141504595?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

|模式|时钟极性|时钟相位|说明|
|---|:---|:---|:---|
|Mode0|CPOL=0|CPHA=0|空闲态时，SCLK处于低电平，数据采样是在第1个边沿，也就是 SCLK 由低电平到高电平的跳变，所以数据采样是在上升沿，数据发送是在下降沿。|
|Mode1|CPOL=0|CPHA=1|空闲态时，SCLK处于低电平，数据发送是在第1个边沿，也就是 SCLK 由低电平到高电平的跳变，所以数据采样是在下降沿，数据发送是在上升沿。|
|Mode2|CPOL=1|CPHA=0|空闲态时，SCLK处于高电平，数据采集是在第1个边沿，也就是 SCLK 由高电平到低电平的跳变，所以数据采集是在下降沿，数据发送是在上升沿。|
|Mode3|CPOL=1|CPHA=1|时空闲态时，SCLK处于高电平，数据发送是在第1个边沿，也就是 SCLK 由高电平到低电平的跳变，所以数据采集是在上升沿，数据发送是在下降沿。|

如图：数据采集的电平

![这里写图片描述](http://img.blog.csdn.net/20170823151644289?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

主机和从机的发送数据是同时完成的，两者的接收数据也是同时完成的。所以为了保证主从机正确通信，应使得它们的SPI具有相同的时钟极性和时钟相位。因为：**其一，主设备SPI时钟和极性的配置应该由外设来决定；其二，二者的配置应该保持一致**，即主设备的SDO同从设备的SDO配置一致，主设备的SDI同从设备的SDI配置一致。因为主从设备是在SCLK的控制下，同时发送和接收数据，并通过2个双向移位寄存器来交换数据。

时序与CPOL、CPHA的关系：

![这里写图片描述](http://img.blog.csdn.net/20170823162652936?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

SPI接口时钟配置心得：在主设备这边配置SPI接口时钟的时候一定要弄清楚从设备的时钟要求，因为**主设备这边的时钟极性和相位都是以从设备为基准的**。因此在时钟极性的配置上一定要搞清楚从设备是在时钟的上升沿还是下降沿接收数据，是在时钟的下降沿还是上升沿输出数据。

#### **4.3.传输时序**

一个完整的传送周期是16位，即两个字节，因为，首先主机要发送命令过去，然后从机根据主机的命令准备数据，主机在下一个8位时钟周期才把数据读回来。

![这里写图片描述](http://img.blog.csdn.net/20170823145351951?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

如下图所示，在SCLK的下降沿上数据改变，上升沿一位数据被存入移位寄存器。

![这里写图片描述](http://img.blog.csdn.net/20170823150210056?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


#### **4.4.数据传输**

在一个SPI时钟周期内，会完成如下操作：

(1) 主机通过MOSI线发送1位数据，从机通过该线读取这1位数据；
(2) 从机通过MISO线发送1位数据，主机通过该线读取这1位数据。
这是通过移位寄存器来实现的。如下图所示，主机和从机各有一个移位寄存器，且二者连接成环。随着时钟脉冲，数据按照从高位到低位的方式依次移出主机寄存器和从机寄存器，并且依次移入从机寄存器和主机寄存器。当寄存器中的内容全部移出时，相当于完成了两个寄存器内容的交换。

![这里写图片描述](http://img.blog.csdn.net/20170823150356910?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## **5.优缺点**

SPI接口具有如下优点：

（1）  支持全双工操作；
（2）操作简单；
（3） 数据传输速率较高。
 
  缺点：
  
（1） 需要占用主机较多的口线（每个从机都需要一根片选线）；
（2）只支持单个主机。
（3） 没有指定的流控制，没有应答机制确认是否接收到数据。


_________




参考：

1.[SPI总线协议及SPI时序图详解 ](http://www.cnblogs.com/adylee/p/5399742.html)

2.[SPI总线协议介绍](http://blog.csdn.net/ce123_zhouwei/article/details/6897293)

3.[SPI总线 通俗易懂讲解](http://blog.csdn.net/ppdyhappy/article/details/51396277)

4.[STM32F4——IIC总线与SPI总线](http://blog.csdn.net/rcj183419/article/details/48734937)

5.[SPI通信协议（SPI总线）学习](http://www.cnblogs.com/deng-tao/p/6004280.html)

6.[SPI](https://baike.baidu.com/item/SPI/4429726?fr=aladdin)

7.[SPI接口扫盲 SPI定义/SPI时序(CPHA CPOL)](http://blog.csdn.net/douqingl/article/details/50452355)
