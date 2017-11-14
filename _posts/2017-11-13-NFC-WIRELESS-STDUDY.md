---
layout: post
title: NFC学习笔记
date: 2017-11-14
categories: IOT
tags: [NFC,SE,COMMUNICATION]
description: NFC STUDY 
---

## **1.概念**

NFC （Near Field Communication），即距离无线通信技术。由飞利浦公司和索尼公司共同开发的NFC 是一种**非接触式识别和互联技术**，一般频率在**13.56MHZ**，它是RFID与互联互通技术整合而来，具有低成本、方便易用和更富直观性特点，可在移动设备、消费电子类产品、PC 和智能控件工具间进行近距离无线通信。NFC 提供了一种简单、触控式的解决方案，可以让消费更简单直观的交换信息、访问与服务。

![这里写图片描述](http://img.blog.csdn.net/20171113102723020?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

nfc是一种短距高频的无线电技术，在13.56MHz频率运行于**10厘米距离内**。其传输速度有106 Kbit/秒、212Kbit/秒或者424 Kbit/秒三种。目前近场通信已通过成为ISO/IEC IS 18092国际标准、ECMA-340标准与ETSI TS 102 190标准。NFC采用主动和被动两种读取模式。

## **2.通信模式**

NFC 的三种工作模式：

![这里写图片描述](http://img.blog.csdn.net/20171110092037397?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### **2.1.读卡器模式(Reader/writer mode)**

 作为非接触读卡器使用，比如从海报或者展览信息电子标签上读取相关信息,可实现NFC手机之间的数据交换。
 
NFC目标设备(从设备)不必产生射频域，而使用负载调制(load modulation)技术，以相同的速度将数据传回发起设备。此通信机制与基于ISO14443 A/B、Mifare和FeliCa的非接触式智能卡兼容，因此，在被动模式下，NFC发起设备可以用相同的连接和初始化过程检测非接触式智能卡或NFC目标设备，并与之建立联系。

#### **2. 2. 点对点模式(P2Pmode)**

此模式和红外线差不多，可用于数据交换，只是传输距离较短，传输创建速度较快，传输速度也快些，功耗低(蓝牙也类似)。将两个具备NFC功能的设备无线链接，能实现数据点对点传输，如下载音乐、交换图片或者同步设备地址薄。因此通过NFC，多个设备如数码相机、PDA、计算机和手机之间都可以交换资料或者服务。

#### **2.3.卡模式(Cardemulation)：**

相当于一张采用RFID技术的IC卡，可以替代大量的IC卡(包括信用卡)使用的场合，如商场刷卡、公交卡、门禁管制，车票，门票等等。此种方式下，有一个极大的优点，那就是卡片通过非接触读卡器的 RF 域来供电，即使寄主设备(如手机)没电也可以工作。

## **3.NFC与SE**

① SE 在 SD 卡上；

② SE 在 SIM 卡上；

③ SE 在 NFC 芯片里面。

现实中支付芯片会在手机主板内嵌入，支付芯片内有时还有一块安全芯片，若没有安全芯片，一般该支付芯片会和SIM卡关联，让SIM卡实现安全芯片功能。

NXP的安全芯片 实现方案（PN6XN）将是将支付芯片和安全芯片(即SE)合并在一起封装后芯片的型号。该芯片实现了上节中讲述的支付芯的五个重要作用，即建RF通道、做交易、存数据、下载卡、保安全。

![这里写图片描述](http://img.blog.csdn.net/20171110093453561?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## **4.NFC与蓝牙**

1.蓝牙是一种近距离、低功耗的无线通讯标准，支持设备短距离通信（10m左右）。能够在手机、笔记本电脑、无线耳机、PDA等相关外设之间进行无线信息交换。

2.蓝牙采用分散式网络结构以及快跳频和短包技术，支持一对一或一对多通信，在2.4GHz频段工作，速率一般为1Mbps。蓝牙兼容性广、开放性强、便携性、对人体伤害小。

NFC发起设备在整个通信过程中提供射频场（RF-field）。它可以选择不同传输速度，将数据发送到另一台设备。另一台设备称为NFC目标设备，不必产生射频场，而使用负载调制（load modulation）技术，即可以相同的速度将数据传回发起设备。

相对于蓝牙，NFC的距离短，速率低，无需配对，适用于近场通信，安全性高因为不用担心数据被拦截。

## **5.NFC与RFID**

NFC与RFID一样，NFC信息也是通过频谱中无线频率部分的电磁感应耦合方式传递，但两者之间还是有很大区别：

1.NFC将非接触读卡器、非接触卡和点对点功能整合进一块单芯片，而rfid必须有阅读器和标签组成。NFC是一种提供轻松、安全、迅速的通信的无线连接技术，其传输范围比RFID小，RFID的传输范围可以达到几米、甚至几十米，但由于NFC采取了独特的信号衰减技术，相对于RFID来说NFC具有距离近、带宽高、能耗低等特点；

2.NFC与现有非接触智能卡技术兼容，目前已经成为得到越来越多主要厂商支持的正式标准。再次，NFC还是一种近距离连接协议，提供各种设备间轻松、安全、迅速而自动的通信。与无线世界中的其他连接方式相比，NFC是一种近距离的私密通信方式。

3.RFID更多的被应用在生产、物流、跟踪、资产管理上，而NFC则在门禁、公交、手机支付等领域内发挥着巨大的作用。

**几个通信方式之间对比：**

![这里写图片描述](http://img.blog.csdn.net/20171113103727051?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 
 











---

**参考：**

1.[NFC 学习总结](http://blog.sina.com.cn/s/blog_75ee77500102wls3.html)

2.[[NFC]简单介绍](http://blog.csdn.net/xuwen0306/article/details/44203059)

3.[移动支付芯片的硬件架构](http://www.dzsc.com/data/2015-6-17/108417.html)

4.[NXP_NFC完整方案](https://wenku.baidu.com/view/3c2f83bf1a37f111f1855b4c.html)

5.[基于NFC技术的SWP方案以及在SIM卡中的实现方法](http://blog.csdn.net/tommy_wxie/article/details/9209543)

6.[可信执行环境（TEE）介绍 与应用](http://blog.csdn.net/wed110/article/details/53894927)

7.[NFC详解](http://blog.csdn.net/zhengxiongwei/article/details/52125683)

8.[NFC近距离无线通讯技术](http://blog.csdn.net/guochunyang/article/details/6171261)

9.[蓝牙和NFC傻傻分不清 这篇文章告诉你两者的区别](http://ee.ofweek.com/2016-11/ART-8320315-8300-30066872.html)

 