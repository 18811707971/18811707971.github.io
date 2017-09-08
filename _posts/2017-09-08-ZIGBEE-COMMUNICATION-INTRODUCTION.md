---
layout: post
title: zigbee知识介绍
date: 2017-09-08
categories: ZigBee
tags: [ZigBee,2.4G,IOT]
description: ZigBee STUDY AND APPLICATION
---

## **1.概念**

 zigbee:是基于IEEE802.15.4标准的低功耗局域网协议。根据国际标准规定，ZigBee技术是一种短距离、低功耗的无线通信技术，又称紫蜂协议。

![这里写图片描述](http://img.blog.csdn.net/20170908104539734?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

 Zigbee是基于IEEE802.15.4的通信协议，IEEE802.15.4处理低级MAC层和物理层协议，而Zigbee协议对网络层和API进行了标准化。简言之，ZigBee就是一种便宜的、低功耗、自组网的近程无线通讯技术。 

![这里写图片描述](http://img.blog.csdn.net/20170908110131864?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

ZigBee协议从下到上分别为物理层(PHY)、媒体访问控制层(MAC)、传输层(TL)、网络层(NWK)、应用层(APL)等。其中物理层和媒体访问控制层遵循IEEE 802.15.4标准的规定。


## **2.特点**

Zigbee是一个由可多到65000个无线数传模块组成的一个无线数传网络平台，在整个网络范围内，每一个Zigbee网络数传模块之间可以相互通信，每个网络节点间的距离可以从标准的75米无限扩展。　

ZigBee技术是一种近距离、低复杂度、低功耗、低速率、低成本的双向无线通讯技术。主要用于距离短、功耗低且传输速率不高的各种电子设备之间进行数据传输，以及典型的有周期性数据、间歇性数据和低反应时间数据传输的应用，适合用于自动控制和远程控制领域，可以嵌入各种设备。

#### **(1)低功耗：**

在低耗电待机模式下，2节5号干电池可支持1个节点工作6-24个月，甚至更长。这是ZigBee的突出优势。相比之下蓝牙可以工作数周、WiFi可以工作数小时；


#### **(2)低成本：**

通过大幅简化协议是成本很低(不足蓝牙的1/10)，降低了对通信控制器的要求，按预测分析，以8051的8位微控制器测算，全功能的主节点需要32KB代码，子功能节点少至4KB代码，而且ZigBee的协议专利免费；


#### **(3)低速率：**

ZigBee工作在250kbps的通讯速率，满足低速率传输数据的应用需求；


#### **(4)近距离：**

传输范围一般介于10～100m之间，在增加RF发射功率后，亦可增加到1-3km。这指的是相邻节点间的距离。如果通过路由和节点间通信的接力，传输距离将可以更远；


#### **(5)短时延：**

ZigBee的响应速度较快，一般从睡眠转入工作状态只需15ms，节点连接进入网络只需30ms，进一步节省了电能。相比较，蓝牙需要3-10s、WiFi需要3s；


#### **(6)高容量：**

ZigBee可采用星状、片状和网状网络结构，由一个主节点管理若干子节点，最多一个主节点可管理254个子节点;同时主节点还可由上一层网络节点管理，最多可组成65000个节点的大网；


#### **(7)高安全：**

ZigBee提供了三级安全模式，包括无安全设定、使用接入控制清单(ACL)防止非法获取数据以及采用高级加密标准(AES128)的对称密码，以灵活确定其安全属性；


#### **(8)免执照频段：**

采用直接序列扩频在工业科学医疗2.4GHz(全球)频段。

IEEE 802.15.4（ZigBee）工作在ISM频带，定义了两个频段，2.4GHz频段和896/915MHz频带。在IEEE 802.15.4中共规定了27个信道：

a.在2.4GHz频段，共有16个信道，信道通信速率为250kbps；

b.在915MHz频段，共有10个信道，信道通信速率为40kbps；

c.在896MHz频段，有1个信道，信道通信速率为20kbps

![这里写图片描述](http://img.blog.csdn.net/20170908110333538?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## **3.ZigBee 应用范围**

ZigBee 已广泛应用于物联网产业链中的M2M 行业，如智能电网、智能交通、智能家居、金融、移动POS 终端、供应链自动化、工业自动化、智能建筑、消防、公共安全、环境保护、气象、数字化医疗、遥感勘测、农业、林业、水务、煤矿、石化等领域。

![这里写图片描述](http://img.blog.csdn.net/20170908110554293?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## **4.目前主要的ZigBee芯片**

![这里写图片描述](http://img.blog.csdn.net/20170908111014646?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**CC2530 ：**内核就是一个增强型的8051 内核，从而降低了芯片成本。每块CC2530 大约15 块RMB。

**EM357：**高性能ZigBee/802.15.4系统级芯片(SoC)，基于ARM Cortex-M3内核，集成了2.4GHz IEEE 802.15.4无线收发器和32位微处理器、闪存和RAM以及支持网络级调试的功能强大硬件。

## **5.Zigbee 无线控制解决方案**

![这里写图片描述](http://img.blog.csdn.net/20170908111912609?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## **6.几种无线组网技术对比**

**蓝牙：**

是一种支持设备短距离通信（一般10m内）的无线电技术。能在包括移动电话、PDA、无线耳机、笔记本电脑、相关外设等众多设备之间进行无线信息交换。这种技术通讯距离太短，同时属于点对点通讯方式。

  **WIFI：**其实就是 IEEE 802.11b 的别称，是由一个名为“无线以太网相容联盟”（Wireless Ethernet Compatibility Alliance, WECA）的组织所发布的业界术语，中文译为“无线相容认证”。它是一种短程无线传输技术，能够在数百米范围内支持互联网接入的无线电信号。它的最大特点就是方便人们随时随地接入互联网。
  
  **315M/433M/868M/915M：**这些无线射频技术广泛运用在车辆监控、遥控、遥测、小型无线网络、工业数据采集系统、无线标签、身份识别、非接触RF等场所。
  
  **ZigBee：**

相比433/315技术，解决了同频干扰、传送距离短、非双向通信、有无线盲区等问题。相比蓝牙技术，解决了传输距离短（手机、电脑上的蓝牙有效通讯距离小于10米）、功耗大、成本高等问题。相比WiFi技术，解决了传输距离短（信号不能进行路由转发，一般跨层后信号就微弱到不能使用的程度）、功耗大、成本高等问题。

![这里写图片描述](http://img.blog.csdn.net/20170908114756361?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)





## **参考：**

1.[主流的智能家居的通信技术除了 ZigBee 和 Wi-Fi，还有哪些？](https://www.zhihu.com/question/20762824)

2.[通俗易懂介绍一下ZigBee的特性](http://blog.csdn.net/geek_monkey/article/details/72819305)

3.[ZigBee最全资料介绍](http://blog.csdn.net/haifengid/article/details/51658592)

4.[[ZigBee] 1、 ZigBee简介 ](http://www.cnblogs.com/zjutlitao/p/5641958.html)

5.[基于ZigBee的家居控制系统的设计与应用 ](http://www.cnblogs.com/star91/p/4889017.html)
