---
layout: post
title: Altium Designer高级功能初探之——覆铜规则
date: 2017-12-04
categories: 电子之路
tags: [PLOY,PCB]
description: PCB设计
---



**覆铜规则：**

____

（一）要求（What）： 我们的key Client "A"公司最近在做一个较为复杂的设计，根据公司，工厂以及IC设计向导等诸多要求，对于铺铜总结出需要注意的十个部分如下：


1. 与相同网络VIA 直连 
2. 与相同网络SMD 焊盘直连
3. 与相同网络MultiLayer 焊盘花孔 
4. 与相同网络MultiLayer 焊盘，大电流元件直连
5. 与不同网络VIA避让 5mil
6. 与不同网络焊盘避让 8mil
7. 与差分对焊盘，过孔避让 12mil，线避让16mil
8. 与单端CLK 信号焊盘，过孔避让 10mil，线避让15mil
9. 与PWM 电源的脉冲信号（如LG，UG，PHase）焊盘，过孔，线避让15mil
10. 与模拟信号线，过孔，焊盘，铺铜避让 12mil

____

**（二）原因概述（Why）**


1. 保证了良好的信号连接以及较好的电流分布
2. 同1
3. 易于焊接
4. 保证大的连接面积以过大电流
5. 常规安全设定
6. 常规安全设定
7. 常规安全设定
8. 常规安全设定
9. 常规安全设定， PWM信号脉冲信号对外部信号干扰较大，需要至少15mil的安全间距
10. 常规安全设定

____

**（三）如何实现（How）**


Altium Designer中规则的设置极其灵活，对于上诉要求，我们给出如下的规则设定参考方案：

##### 1.和铜皮相同的网络的*via*连接方式——>直接连接， 设置以及效果图如下：

![这里写图片描述](http://img.blog.csdn.net/20171204210739454?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

效果如下：

![这里写图片描述](http://img.blog.csdn.net/20171204210854214?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**注：**

1. 在Polygon Connect Style处单击鼠标右键，选择New Rule；
 
2. 将新添加的规则添加到比较高的优先级别，点击"Increase Priority"或"Decrease Priority"来提高或降低其优先级，否则规则设置无效。

___

#####  2.   和铜皮相同网络的SMD焊盘——>直接连接

![这里写图片描述](http://img.blog.csdn.net/20171204211519693?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


效果如下：

![这里写图片描述](http://img.blog.csdn.net/20171204211424412?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

注：

由于对于过孔和SMD器件都具有相同的规则，那么可以进行合并如下以实现相同的功能：

![这里写图片描述](http://img.blog.csdn.net/20171204211710269?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

____

#####  3.   与铜皮相同网络的通孔焊盘的连接方式——>花孔连接：

![这里写图片描述](http://img.blog.csdn.net/20171204212049212?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

____

#####  4.   与铜皮网络相同，且为大电流的通孔焊盘连接方式——>直接连接

创建需要过大电流的pad class，Design》Classes， 命名为bigpad

![这里写图片描述](http://img.blog.csdn.net/20171204212502267?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20171204212724098?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20171204212903665?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

效果如下：

![这里写图片描述](http://img.blog.csdn.net/20171204213001623?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#####  5.  和铜皮网络不同的via之间间距设置为5mil：

![这里写图片描述](http://img.blog.csdn.net/20171204213339018?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

效果如下：

![这里写图片描述](http://img.blog.csdn.net/20171204213629450?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#####  6. 和铜皮网络不同的焊盘之间的间距设置为8mil：

![这里写图片描述](http://img.blog.csdn.net/20171204214227902?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

效果如下：

![这里写图片描述](http://img.blog.csdn.net/20171204214343098?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

##### 7.  差分对焊盘和过孔与铜皮相隔12mil，差分对与铜皮相隔16mil

![这里写图片描述](http://img.blog.csdn.net/20171204214621363?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20171204214646854?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

效果如下：

![这里写图片描述](http://img.blog.csdn.net/20171204214802166?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

注：PEC为差分对网络，见过孔

#####  8. 单端CLK 信号焊盘，过孔与铜皮避让 10mil，线避让15mil

![这里写图片描述](http://img.blog.csdn.net/20171204215034544?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20171204215056828?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

效果如下：

![这里写图片描述](http://img.blog.csdn.net/20171204215128388?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#####  9. PWM 电源的脉冲信号（如LG，UG，PHase）焊盘，过孔，线与铜皮间隔15mil  

为PWM电源中的几个脉冲信号创建一个类：

![这里写图片描述](http://img.blog.csdn.net/20171204215235166?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

为这组脉冲信号创建一个规则：

![这里写图片描述](http://img.blog.csdn.net/20171204215303092?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

效果如下：

![这里写图片描述](http://img.blog.csdn.net/20171204215318514?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#####  10. 模拟信号线，过孔，焊盘与铺铜间距为12mil

为相应的模拟信号线创建一个网络类：

![这里写图片描述](http://img.blog.csdn.net/20171204215353406?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20171204215409616?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

效果如下：

![这里写图片描述](http://img.blog.csdn.net/20171204215441730?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

____

注：转载自---->[ Altium Designer高级功能初探之：覆铜规则](http://blog.csdn.net/msl369369/article/details/51801590)

部分图片有修改！