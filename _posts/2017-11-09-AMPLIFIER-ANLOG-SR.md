---
layout: post
title: 运放参数——压摆率(SR)
date: 2017-11-09
categories: 模拟电路
tags: [压摆率,运放,电路设计]
description: 放大器的参数压摆率介绍
---

## **1.定义**

slew rate 就是电压转换速率（Slew Rate），简称压摆率。其定义是在1微秒时间里电压升高的幅度，就是**方波来测量时就是电压由波谷升到波峰所需时间**，单位通常有V/s，V/ms和V/μs三种。如果电压转换速率不高，在信号来了时不能准确及时跟上，信号消失后放大器只能跟上了原信号电平的一半或更低，令信号的幅度比信号缩小，分析力也就差了。

## **2.解释**

压摆率可认为，当输入运放一个阶跃信号时，运放输出信号的最大变化速度，如下图：

![这里写图片描述](http://img.blog.csdn.net/20171109181412128?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20171109181944144?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 
**计算表达式：**

![这里写图片描述](http://img.blog.csdn.net/20171109182710487?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**压摆率SR的来源：**

![这里写图片描述](http://img.blog.csdn.net/20171109182942455?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**运放的SR主要限制在内部第二级的Cc电容上。这个电容同时也决定着运放的带宽**。那运放的压摆率，主要是由于对**第二级的密勒电容充电过程的快慢**所决定的。这个电容的大小会影响到运放的压摆率，同时充电电流的大小也会影响到充电的快慢。这也就解释了，为什么**一般超低功耗的运放压摆率都不会太高**。好比水流流速小，池子又大。只能花更长的时间充满池子。

SR对放大电路的影响了。它的直接影响，就是使输出信号的上升时间或下降时间过慢，从而引起失真。

![这里写图片描述](http://img.blog.csdn.net/20171109183647558?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

上图是测试的OPA333增益G=10时波形。由于OPA333的增益带宽积为350kHz，理论上增益为10的时候的带宽为35kHz。但下图是24kHz时测试的结果。显然输出波形已经失真，原因就是压摆率不够了。带宽也变成了27kHz左右。

压摆是产生建立时间原因，也是限制运放速率的重要因素！
 

 
 ___

**参考：**

1.[运放的转换速率（压摆率）SR的意义和如何选取](http://blog.csdn.net/dxshappy/article/details/8065798)

2.[运放参数的详细解释和分析-part18，压摆率(SR)](http://www.deyisupport.com/question_answer/analog/amplifiers/f/52/t/21086.aspx)

3.[运放压摆率-2](https://wenku.baidu.com/view/daf20dcd6edb6f1afe001f9a.html)

4.[运放的速率、建立时间和压摆率的关系 ](http://blog.163.com/seven_ye_love@126/blog/static/42007707201552693536292/)