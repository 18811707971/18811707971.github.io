---
layout: post
title: 蓝牙模块——基础知识介绍
date: 2017-09-01
categories: BULETOOTH
tags: [BT,APPLICATION,2.4G]
description: BULETOOTH STUDY AND APPLICATION
---

## **1.数据透传**

蓝牙模块可以通过串口（SPI、IIC）和MCU控制设备进行数据传输。

![这里写图片描述](http://img.blog.csdn.net/20170904100308976?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

蓝牙模块可以做为主机和从机。主机就是能够搜索别的蓝牙模块并主动建立连接，从机则不能主动建立连接，只能等别人连接自己。

## **2.低功耗**

低功耗蓝牙（Bluetooth Low Energy），简称BLE。蓝牙低能耗无线技术利用许多智能手段最大限度地降低功耗。

蓝牙低能耗架构共有两种芯片构成：单模芯片和双模芯片。蓝牙单模器件是蓝牙规范中新出现的一种只支持蓝牙低能耗技术的芯片——是专门针对ULP操作优化的技术的一部分。蓝牙单模芯片可以和其它单模芯片及双模芯片通信，此时后者需要使用自身架构中的蓝牙低能耗技术部分进行收发数据。双模芯片也能与标准蓝牙技术及使用传统蓝牙架构的其它双模芯片通信。

![这里写图片描述](http://img.blog.csdn.net/20170904101711400?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**按应用分为：**

可分为数据蓝牙模块和语音蓝牙模块，前者完成无线数据传输，后者完成语音和立体声音频的无线数据传输。

## **3.蓝牙协议组成**

![这里写图片描述](http://img.blog.csdn.net/20170904104024495?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

蓝牙协议体系中的协议按SIG的关注程度分为四层：

1.核心协议：BaseBand、LMP、L2CAP、SDP； 

2.电缆替代协议：RFCOMM； 

3.电话传送控制协议：TCS-Binary、AT命令集； 

4.选用协议：PPP、UDP/TCP/IP、OBEX、WAP、vCard、vCal、IrMC、WAE。

除上述协议层外，规范还定义了主机控制器接口（HCI），它为基带控制器、连接管理器、硬件状态和控制寄存器提供命令接口。在图1中，HCI位于L2CAP的下层，但HCI也可位于L2CAP上层。

蓝牙核心协议由SIG制定的蓝牙专用协议组成。绝大部分蓝牙设备都需要核心协议（加上无线部分），而其他协议则根据应用的需要而定。总之，电缆替代协议、电话控制协议和被采用的协议在核心协议基础上构成了面向应用的协议。 

## **4.蓝牙基本架构**

![这里写图片描述](http://img.blog.csdn.net/20170904134715826?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### **4.1底层硬件模块**

**（1）无线射频模块（Radio）：**蓝牙最底层，带微带天线，负责数据接收和发送。

**（2）基带模块（BaseBand）：**无线介质访问约定。提供同步面向连接的物理链路（SCO）和异步无连接物理链路（ACL），负责跳频和蓝牙数据及信息帧传输，并提供不同层次的纠错功能（FEC和CTC）。

**（3）链路控制模块（LC）：**蓝牙数据包的编码和解码。

**（4）链路管理模块（LM）**：负责创建、修改和发布逻辑链接，更新设备间物理链接参数，进行链路的安全和控制。

**（5）主机控制器接口（HCI）：**是软硬件接口部分，由基带控制器、连接管理器、控制和事件寄存器等组成；软件接口提供了下层硬件的统一命令，解释上下层消息和数据的传递。硬件接口包含UART、SPI和USB等。

#### **2.中间协议层**

**（1）逻辑链路控制与适配协议（L2CAP）：**蓝牙协议栈的基础，也是其他协议实现的基础。向上层提供面向连接和无连接的数据封装服务；采用了多路技术、分割和重组技术、组提取技术来进行协议复用、分段和重组、认证服务质量、组管理等行为。

**（2）音视频发布传输协议（AVDTP）和音视频控制传输协议（AVCTP）：**二者主要用于Audio/Video在蓝牙设备中传输的协议，前者用于描述传输，后者用于控制信号交换的格式和机制。

**（3）服务发现协议（SDP）：**蓝牙技术框架至关重要一层，所有应用模型基础。动态的查询设备信息和服务类型，建立一条对应的服务通信通道，为上层提供发现可用的服务类型和属性协议信息。

**（4）串口仿真协议（RFCOMM）：**实现了仿真9针RS232串口功能，实现设备间的串行通信。

**（5）二进制电话控制协议（TCS）：**基于 ITU-T Q.931 建议的采用面向比特的协议，它定义了用于蓝牙设备之间建立语音和数据呼叫的控制信令（Call Control Signalling），并负责处理蓝牙设备组的移动管理过程。

#### **3.蓝牙Profile**

Bluetooth Profile是蓝牙设备间数据通信的无线接口规范。目前有四大类、十三种协议规则，厂商可以自定义规格。几种最常见的Profile文件：

**（1）通用访问配置文件（GAP）：**其他所有配置文件的基础，定义了在蓝牙设备间建立基带链路的通用方法，并允许开发人员根据GAP定义新的配置文件。包含所有蓝牙设备实施的功能，发现和连接设备的通用步骤，基本用户界面等通用操作。

**（2）服务发现应用配置文件（SDAP）：**描述应用程序如何用SDP发现远程设备服务，可与向/从其他蓝牙设备发送/接收服务查询的SDP连接。

**（3）串行端口配置文件（SPP）：**基于ETSI TS 07.10规格定义如何设置虚拟串行端口及如何连接两个蓝牙设备。速度可达128kb/s。

**（4）通用对象交换配置文件（GOEP）：**可以将任意对象（如图片、文档等）从一个设备传输到另一个设备。

## **5.蓝牙协议栈层次**

![这里写图片描述](http://img.blog.csdn.net/20170904135907767?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### **5.1.物理层（PHY）:**

射频传输。

#### **5.2.链路层（LL）:**

控制射频状态，包括等待、广告、扫描、初始化、连接。

#### **5.3.主机控制接口层（HCI）:**

主机和控制器通信接口。

#### **5.4.逻辑链路控制及适配协议层（L2CAP）:**

提供数据封装服务，允许逻辑上点对点通信。

#### **5.5.安全管理层（SM）:**

加解密，为安全连接和数据交换提供服务。

#### **5.6.属性协议层（ATT）:**

允许设备（服务器）向另一个设备（客户端）展示特定的数据（属性）。

#### **5.7.通用属性配置文件层（GATT）:**

定义了使用ATT的服务框架，两个建立连接的设备之间的所有数据通信都是通过GATT子程序处理。

#### **5.8.通用访问配置文件层（GAP）:**

对所有蓝牙设备提供共同的功能，如传输模式和访问程序、协议和应用描述。GAP服务包含设备发现、连接模式、安全、认证、联合模型和服务发现。

## **6.蓝牙传输过程**

#### **6.1.主从机建立过程**

![这里写图片描述](http://img.blog.csdn.net/20170904141046713?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### **6.2.主从通信透传模块**

分为主透传协议模块（MTTM）和从透传协议模块（STTM）。

MTTM可以工作在透传模式（TTM）和指令模式（CM），工作如下：用户通过AT指令控制模块连接从设备。在成功建立链接后，MTTM自动查找从设备的透传通道，如果从设备属于STTM，MTTM默认进入TTM，否则进入CM。

透传模式，目的是实现透传或直驱控制功能，主机CPU可以通过通用串口模块与STTM双向通信，MTTM直接将数据转发给STTM，再从STTM串口输出到从机CPU。反向亦可。




## **参考：**

1.[SKYLAB_无线模块的博客](http://blog.sina.com.cn/s/blog_14e4dfb2a0102wu1l.html)

2.[由浅入深，蓝牙4.0/BLE协议栈开发攻略大全（1）](http://www.elecfans.com/news/wangluo/20140813351233.html)

3.[ 蓝牙核心技术了解（蓝牙协议、架构、硬件和软件笔记）](http://blog.csdn.net/qq576494799/article/details/60104798)

4.[通识蓝牙低功耗](https://zhuanlan.zhihu.com/p/27499569)

5.[ nrf51822蓝牙笔记之GAP（通用属性配置文件）解读](http://blog.csdn.net/sinat_23338865/article/details/52479778)

6.[蓝牙的初见初识](https://zhuanlan.zhihu.com/p/26680044)

7.[蓝牙协议分析(5)_BLE广播通信相关的技术分析](http://www.cnblogs.com/heiyue/p/6109283.html)

8.[Bluetooth LE(低功耗蓝牙) - 第一部分](http://www.cnblogs.com/heiyue/p/4095771.html)