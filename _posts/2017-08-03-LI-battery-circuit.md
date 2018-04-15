---
layout: post
title: 锂电池相关知识
date: 2017-08-03
categories: 电子之路
tags: [锂电池,电池电路,了解]
description: 锂电池
---

## **1.基本概念**
 
锂电池是一种以锂金属或锂合金为负极材料，使用非水电解 质溶液的一次电池，与可充电电池锂离子电池跟锂离子聚合物电池是不一样的。锂电池大致可分为两类：锂金属电池和锂离子电池。锂离子电池不含有金属态的锂，并且是可以充电的。

目前锂电池公认的基本原理是所谓的“摇椅理论”。锂电池的冲放电不是通过传统的方式实现电子的转移，而是通过锂离子在层壮物质的晶体中的出入，发生能量变化。在正常充放电情况下，锂离子的出入一般只引起层间距的变化，而不会引起晶体结构的破坏，因此从冲放电反映来讲，锂离子电池是一种理想的可逆电池。在充放电时锂离子在电池正负极往返出入，正像摇椅一样在正负极间摇来摇去，故有人将锂离子电池形象称为摇椅电池。

我们经常说的锂离子电池的优越性是针对于传统的镍镉电池（Ni/Cd）和镍氢电池（Ni/MH）来讲的。 具有工作电压高比能量大循环寿命长自放电率低无记忆效应等优点。

_____

## **2.锂电池充电**
 
(1)锂电池充电过程

![这里写图片描述](http://img.blog.csdn.net/20170803091904552?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

                                        图1-充电过程
                                        
图中横坐标为时间，纵坐标为锂电池电压。由于锂电池的特殊性，**过压或者欠压都会导致电池报废**，所以现在的**锂电池充放电保护电路原理就是测量锂电池电压，再根据电压判断锂电池是否处于正常状态**（非过压、非欠压）。
 
 锂电池的**充电电流如上图紫色线所示**。锂电池的充电分为三个阶段，分别是**恒流预充电、大电流恒流充电与恒压充电**。

![这里写图片描述](http://img.blog.csdn.net/20170803094153139?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

 a.当电压低于 3.0V 时，充电器会采用 100mA 电流对锂电池进行预充电，就是预充阶段，目的是慢慢恢复过放电的锂电池，是一种保护措施来的。合格的充电器都会有这个充电阶段。

b.锂电池电压高于 3.0V 时，就进入到第二阶段，大电流恒流充电阶段。由于锂电池经过第一阶段的预充，其状态已经比较稳定了。在第二阶段，充电电流就可以适当提高，根据不同的电池来说，这个电流的大小可以从 0.1C 到几 C 不等，其中C是指电池容量。eg：2600mAh 的锂电池，0.1C 就是指 260mA 大小的电流。

c.恒压充电阶段，这个阶段就是检测到锂电池电压等于 4.2V 时，充电器则进入恒压充电模式，这个阶段充电电压恒定为 4.2V，充电电流则越来越小（慢慢充满了，电流肯定变小~）。当充电电流小于 100mA 时，就判断电池充满，切断充电电路。

d.充电终止，有两种典型的充电终止方法：采用最小充电电流判断或采用定时器(或者两者的结合)。最小电流法监视恒压充电阶段的充电电流，并在充电电流减小到0.02C至0.07C范围时终止充电。第二种方法从恒压充电阶段开始时计时，持续充电两个小时后终止充电过程。
 
 注：快速充电是指充电电流大于 0.1C 的充电方式，这种充电方式对于单个锂电池来说，对寿命与稳定性等的影响非常小；但是如果对于电瓶车电池组来说的话，快充就是用时间换取电池寿命的一种行为。
 
 ______
 
## **3.过充、过放、短路保护电路**

 锂电池(可充型)之所以需要保护，是由它本身特性决定的。由于锂电池本身的材料决定了它**不能被过充、过放、过流、短路及超高温充放电**，因此锂电池锂电组件总会跟着一块精致的保护板和一片电流保险器出现。

 普通锂电池保护板通常包括**控制IC、MOS开关、电阻、电容及辅助器件FUSE、PTC、NTC、ID、存储器**等。其中控制IC，在一切正常的情况下控制 MOS开关导通，使电芯与外电路导通，而当电芯电压或回路电流超过规定值时，它立刻控制MOS开关关断，保护电芯的安全。

![这里写图片描述](http://img.blog.csdn.net/20170803101711743?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

该电路由锂电池保护专用**集成电路DW01**，充、放电控制MOSFET1(**内含两只N沟道MOSFET**)等部分组成。单体锂电池接在B+和B-之间，电池组从P+和P-输出电压。充电时，充电器输出电压接在P+和P-之间，**电流从P+到单体电池的B+和B-，再经过充电控制MOSFET到P-**。在充电过程中，当单体电池的电压超过4.35V时，专用集成电路DW01的 **OC** 脚输出信号使充电控制MOSFET关断，锂电池立即停止充电，从而防止锂电池因过充电而损坏。放电过程中，当单体电池的电压降到2.30V时，DW01的 **OD** 脚输出信号使放电控制MOSFET关断，锂电池立即停止放电，从而防止锂电池因过放电而损坏，DW01的CS脚为电流检测脚，输出短路时，充放电控制MOSFET的导通压降剧增，CS脚电压迅速升高，DW01输出信号使充放电控制MOSFET迅速关断，从而实现过电流或短路保护。

实例运用：电池保护芯片 + 双 NMOS	

![这里写图片描述](http://img.blog.csdn.net/20170803115237741?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

精工电子S-8261系列的锂电池管理芯片运用

![这里写图片描述](https://img-blog.csdn.net/2018032715462227?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)


**参考：**

 1.[android 电池（一）：锂电池基本原理篇](http://blog.csdn.net/xubin341719/article/details/8497830)

2.[电池快充的原理是什么？](https://www.zhihu.com/question/23831848/answer/25850047?utm_campaign=rss&utm_medium=rss&utm_source=rss&utm_content=title)

3.[锂电池充电的原理解析](http://libattery.ofweek.com/2014-11/ART-36001-11000-28898666.html)

4.[锂电池过充电、过放电、短路保护电路详解](http://www.21ic.com/app/power/201204/117717.htm)

5.[锂电池保护板原理 ](http://linear.eefocus.com/module/forum/thread-594025-1-1.html)

6.[供电电路切换与锂电池充电电路设计 ](http://www.cnblogs.com/LittleTiger/p/4402760.html)

7.[PDF数据手册下载](https://www.ablicinc.com/cn/semicon/products/power-management-ic/lithium-ion-battery-protection-ic/)

8.[锂电池保护板原理](http://m.dzsc.com/data/2015-8-31/108714.html)
