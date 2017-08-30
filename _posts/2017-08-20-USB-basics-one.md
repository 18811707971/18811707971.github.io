---
layout: post
title: USB总线专题（一）——基础知识
date: 2017-08-20
categories: USB
tags: [usb,agreement,bus]
description: USB STUDY AND APPLICATION
---

**1.基本概念介绍**

**USB** (Universal Serial Bus)是1995年Microsoft、Compaq、IBM等公司联合制定的一种新的PC串行通信协议。它基于通用连接技术，实现外设的简单快速连接，达到方便用户、降低成本、扩展PC连接外设范围的目的。允**许外设在开机状态下热插拔**，最多可串接下来 **127** 个外设，它可以向**低压设备**提供**5伏电源**，同时可以减少 PC 机 I/O 接口数量。

**2.USB版本**

USB 1.0出现在1996年的，速度只有 1.5Mb/s

1998年升级为USB 1.1，速度也提升到 12Mb/s，称之为”full speed”


USB2.0规范是由USB1.1规范演变而来的。它的传输速率达到了 480Mbps，称之为“high speed”


USB3.0提供了十倍于USB 2.0的传输速度和更高的节能效率，速率最高达 5Gbps 被称为”super speed”。都可向下兼容。

**3.USB的电气特性** 

USB 连接器包含四条线：2条用于电源供电(VBUS和GND)，2条用于USB数据传输(D+和D-)。**VBUS提供5V电源，电流可达500mA**。**D+和D-为双向信号线，信号传输速率为12Mbps(每位83ns)。D+和D-信号电平为3.3V**  。

![这里写图片描述](http://img.blog.csdn.net/20170817141021992?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

USB OTG接口中有5条线： 2条用来传送数据（D+ 、D-）; 1条是电源线(VBUS); 1条则是接地线(GND)、1条是ID线

**4.USB硬件接口**

![这里写图片描述](http://img.blog.csdn.net/20170817141727391?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

现如今在移动设备使用比较多的为：

![这里写图片描述](http://img.blog.csdn.net/20170817143151560?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**5.USB的特点**

1.可以热插拔，即插即用。

2.携带方便。USB设备大多以“小、轻、薄”见长，对用户来说，随身携带大量数据时，很方便。当然USB硬盘是首要之选了。

3.标准统一。大家常见的是IDE接口的硬盘，串口的鼠标键盘，并口的打印机扫描仪，可是有了USB之后，这些应用外设统统可以用同样的标准与个人电脑连接，这时就有了USB硬盘、USB鼠标、USB打印机等等。

4.可以连接多个设备。USB在个人电脑上往往具有多个接口，可以同时连接几个设备，如果接上一个有四个端口的USB HUB时，就可以再连上；四个USB设备，以此类推，尽可以连下去，将你家的设备都同时连在一台个人电脑上而不会有任何问题(注：最高可连接至127个设备)。

**6.USB的拓朴结构**

 USB 的总线结构是采用**阶梯式星形**（tiered star）的**拓扑**（topology）结构，如下图所示。每一个星形的**中心是集线器**，而每一个设备可以通过集线器上的接口来加以连接。从图中可以看到USB的设各包含了两种类型：USB集线器与USB设备。位于最顶端的就是**Host（主机端）**。从Host的联机往下连接至Hub（集线器），再由集线器按阶梯式以一层或一阶的方式往下扩展出去，连接在下一层的设各或另一个集线器上。事实上，集线器也可视为一种设备。而其中最大层数为6层（(包括最后一级设备后共7层)）。每一个星形的外接点的数目可加以变化，一般集线器具有2、4或7个接口。

USB的拓扑体系由3种元素组成**：主机(Root Hub与USB主机控制器是绑定在一起的)、Hub和设备**。在PC平台上的USB中，PC就是主机和根Hub，用户可以将设备和下级Hub与之连接。而这些附加的Hub又可以连接更下一级的Hub和设备，从而构成了星形结构。

![这里写图片描述](http://img.blog.csdn.net/20170817144526121?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170817144947102?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

 图中的Hub是一类特殊的USB设备，它是一组USB的连接点，主机中有一个被嵌入的Hub叫根Hub(root Hub)。主机通过根Hub提供若干个连接点。为了防止环状连接，采用星形连接来体现层次性。

**常见的主控制器规格有**：

OHCI：主要是非PC系统上的USB芯片，一般是USB1.1标准。

UHCI：主要是Intel和Via主板上的USB控制器芯片。一般是USB1.1标准。

EHCI：是Intel等几个厂商研发，并且兼容OHCI和UHCI。一般是USB2.0标准。

**7.USB总线的总体结构**

整个USB总线可以分为3个部分进行描述：USB连接、USB设备、USB主机。

![这里写图片描述](http://img.blog.csdn.net/20170821102308230?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**USB主机：**
　　
　　在USB总线中只有一个主机。USB总线与计算机主机系统的接口部分就是主机控制器，它可被看做一个硬件、固件和软件的结合体。主机系统中集成了一个根hub来提供一个或多个连接点。
　　

**USB设备：**

   首先USB设备可被分为两大类：hub类（提供附加USB接入点的设备）和功能 设备类（为系统实现某些功能的设备，如ISDN适配器、数字游戏杆等）。
   
　　按照功能，USB设备又可分为很多类，如：音频、人机交互、显示、通信、电源、打印机、海量存储、物理反馈等设备。每个USB设备都必须提供自鉴定信息和通用的设置。
　　
　　USB设备都有一个标准的USB接口，它的作用为：解释USB协议；对标准USB操作的响应，如挂起和设置等；提供设备的一些描述信息。
　　
　　在实际的设计应用中，USB设备的接口有自已的特点。USB接口的正确设计与设备的性能紧密相关，在USB接口设计之前必须要对设备的功能、指标进行详细的分析。
　　
　　连接在USB接口上的设备通过基于令牌和主机控制的协议来共同享用整个USB带宽。在其它设备正常工作的前提下，USB允许某设备连接、设置、运行和断开连接。

**USB连接：**

　　USB连接是指 USB 主机和 USB 设备的通信方式与方法，包括：总线拓扑（USB主机和设备之间的连接方式）；层内关系（USB总线每一层中的任务）；数据流模式（数据在USB总线上的流动方式）；USB调度（USB提供一个共享的服从调度的互连）。
　　
　　USB设备是通过USB总线连接到USB主机上的。USB总线上的物理连接是一个分层的星形拓扑。处于每个星形拓扑中央的是hub(USB 集线器)。在主机和一个hub或者一个应用之间以及在hub和其它hub或应用之间都是一个点对点的连接。图1表示了USB的拓扑类型。
　　



**8.USB设备探测**

全速和低速设备通过**其端接的上拉电阻的位置**来区分。

◆  全速设备端，D+线上接上拉电阻1.5K欧姆±5％

◆  低速设备端接，D-线上接上拉电阻1.5K欧姆±5％

◆  下行端口的下拉端接电阻为15K欧姆±5％连接到地（USB 主机端：提供USB接口及接口管理能力的硬件、软件及固件的复合体）

当USB下行端口没有连接设备时，由于下拉电阻的存在，使D+和D－上的电压为0，而当端口接上设备时，设备供电后，电源会通过上拉电阻（1.5K欧姆）和端口的下拉电阻（15K欧姆）分压，在D+或D－上产生一个正脉冲，而USB主机根据脉冲产生在哪根数据线上，来判断是全速还是低速。

![这里写图片描述](http://img.blog.csdn.net/20170821091157392?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**9.USB设备与USB驱动的匹配**

USB 设备与 USB 驱动怎么匹配的呢？实际上 USB 设备中有一个模块叫**固件**，是固件信息和 USB 驱动进行的匹配。**固件是固化在集成电路内部的程序代码，USB 固件中包含了 USB 设备的出厂信息，标识该设备的厂商 ID、产品 ID、主版本号和次版本号等。**另外固件中还包含一组程序，这组程序主要完成 USB协议的处理和设备的读写操作。USB 设备固件和 USB 驱动之间通信的规范是通过 USB 协议来完成的。




参考：**

1.[总线接口与计算机通信（四）USB外部总线（初级认识） ](http://www.cnblogs.com/mylinux/p/5902043.html)

2.[USB拓扑结构和USB系统软硬件组成 ](http://blog.sina.com.cn/s/blog_4a3946360100zb6a.html)

3.[USB物理总线的拓扑结构](http://www.elecfans.com/dianlutu/187/2008070210066.html)

4.[ USB系统结构](http://blog.csdn.net/mrwangwang/article/details/17720901)

5.[Linux下的USB总线驱动(01)——USB理论 ](http://www.cnblogs.com/lightsalt/archive/2013/03/09/USB.html)

6.[USB总线的体系结构](http://blog.csdn.net/qinlicang/article/details/4421549)
