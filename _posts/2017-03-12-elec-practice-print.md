---
layout: post
title: 电子工艺实习-------PCB打印设置
date: 2017-03-12
categories: 电子之路
tags: [软件,PCB,技术]
description: 画板画板
---

本文基于福州时创的电路板蚀刻机制作简单PCB，用于实验教学。

1、打开Altium Designer 设计软件

2、打开自己设计好的工程

![这里写图片描述](http://img.blog.csdn.net/20170312142339953?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

三个主要文件：工程文件、原理图、PCB

3、进入到Altium Designer PCB文件

![这里写图片描述](http://img.blog.csdn.net/20170312142533050?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

4、将PCB打印到硫酸纸上参数设置

我们在PCB的当前文件下，左键单击文件(File)菜单--->Page Setup,如下显示：

![这里写图片描述](http://img.blog.csdn.net/20170312142646473?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

	参数设置：
	Size：A4
	Scale Mode:Scaled Print
	Scale:1
	Offset:位置可以自己选定(去掉选择中心的√)
	Color Set:Mono
	Print:直接打印
	Preview:打印预览(打印之前可以先看，是否设置正确)
	Advanced:后面再具体介绍

Advanced菜单设置：

![这里写图片描述](http://img.blog.csdn.net/20170312142755490?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

这个菜单主要选择我们的电路板设计的层数，对于当前的实验教学，均为简单单层板，所以需要删除一些没用的层。

单层板常用：
留下Top Layer或者 Bottom Layer和Keep-Out Layer 即可，其他均可删除。
设置好之后，点击OK保存，再通过打印机打印到硫酸纸。

注：具体的细节由自己设计的电路板决定。

Preview菜单：
主要的作用为打印之前预览自己设定的参数是否正确。(也可以直接通过File--->Print Preview直接进入预览)

![这里写图片描述](http://img.blog.csdn.net/20170312143113637?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

5、线路板的曝光流程：

	1.撕开感光板的保护膜
	2.把感光板放入图纸中对齐
	3.将对好的图纸和感光板放入曝光区
	4.扣上真空夹板手
	5.按下抽真空开关
	6.关上曝光区
	7.曝光状态为开，曝光时间设置为100秒
	8.按运行键进行曝光倒计时
	9.按任意键结束，按抽真空泵开关取消抽真空
	10.打开曝光区取出感光板
	11.用夹子夹住感光板的边缘
	12.把曝光好的感光板放入显影槽中，视频中大约5秒（没有明确要求）
	13.将显影好的感光板放入水中清洗
	14.将清洗好的感光板放入蚀刻槽中，大约6-8分钟
	15.把蚀刻好的线路板放到水中清洗一下
	16.完成，全程约10分钟

注：显影和蚀刻的时间与化学试剂的浓度，温度有很大关系。需要自行灵活调整，不可直接以上面的时间计算！
