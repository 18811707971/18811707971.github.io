---
layout: post
title: Altium 2层板PCB设计学习加强笔记
date: 2017-07-15
categories: 电子之路
tags: [总结,PCB,技术]
description: PCB设计
---


## **1.原理图编译与检查**

设计完原理图后，常常需要检查错误。首先先设定相关的规则，然后可以在工程下点击编译，发现修改错误。设置菜单见下图l;

![这里写图片描述](http://img.blog.csdn.net/20170715182808843?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## **2.器件封装完整性检查**

（1）封装对应的是器件的实际形状，设计完原理图后需要对封装进行修改和检查，具体可以从封装管理器来看。

![这里写图片描述](http://img.blog.csdn.net/20170715181022740?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

（2）对于封装路径，可以通过一下三种方法设置，对应不同的封装库所在位置，一般可以直接设为 Any。

![这里写图片描述](http://img.blog.csdn.net/20170715181257590?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

（3）对于没有现成封装的器件，可以通过其手册（规格书）创建。方法有：Altium 自带的 IPC 设计；也可以直接通过尺寸在 PCB 库文件里放置焊盘等进行设置。

快速放置焊盘可以用特殊粘贴:Edit-->paste special

设计完封装后还需设置参考点：Edit-->Set Reference(PIN1)

## **3.导入网表**

（1）针对导入发生的错误，再次修改确认

（2）相同器件 ID 的修改，常由于直接复制粘贴导致，方法如下：

![这里写图片描述](http://img.blog.csdn.net/20170715183855508?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## **4.板框、机械结构的创建**

（1） CAD 绘图工具创建，导入到 AD，顶层、底层合并；

（2）直接用 AD keep-out 层创建。

## **5.布局**

（1）模块化布局：

a.先大后小，固定位置器件的放置，锁定器件

b.可以关闭飞线（快捷键：N-->show / hide）

c.器件排列对齐：快键键：A---->(left,right,top,bottom 等）

d.文本位置，只要是丝印（A + P）

（2）交互式布局

a.分屏显示，SCH 与 PCB 窗口互相交替，如下图设置：

![这里写图片描述](http://img.blog.csdn.net/20170715185228298?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

b.交互设置：Tools --->cross select mode

c.器件离散：同一部分的器件放置到一个位置，便于分开。如图：

![这里写图片描述](http://img.blog.csdn.net/20170715185602960?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

（3）整体布局

a.根据飞线走向，网络连接，放置器件

b.局部模块化

c.电源入口，信号走向，滤波电容尽量靠近 IC 的管脚

## **6.层叠管理**

（1）确定板的层数

（2）便于加工，减少成本，一般过孔不超过两种（外围一般设为孔心的 x 2 ± 2）

（3） DRC 规则设置

（4）相关规则设置：如安全间距、覆铜、线宽、丝印

## **7.class 创建**

Design--->Class （电源类，差分类等），右下角的 PCB 选项可以对如差分类走线的属性进行设置。

## **8.PCB扇孔处理**

（1）短线直接连接，顺、逆时针放置过孔

（2）多根拉线（T+T+M）

## **9.走线**

（1）直接走线，覆铜走线

（2）加大载流，增加过孔

## **10.整板修改，覆铜**

（1）走线的环路面积尽量小

（2）特殊信号包地处理

（3）走线时可先忽略 DRC ，走完再调整

（4）覆铜处理：割除焊盘之间的铜皮

（5）地焊盘就近扇孔

## **11.整体修改、调整**

（1）丝印摆放：字母一般在左、下方向，到焊盘，到丝印的间距一般设为 2mil.

（2）输出装配图：Top Layer/solder/keep-out （底层同样）

（3）锁定器件

## **12.gerber文件的输出：单位、过孔、钻孔、网表、坐标**

## **13.设计复杂的电路时，原理图文件常由多个部分组成，有两种设计的方法：**

平坦式：多个原理图平级，通过端口或者网络标号连接（NET / PORT）

等级式：分层设计（自顶向下；自下向上），顶层由 sheet symbol 和 sheet Entry 组成。如图：

![这里写图片描述](http://img.blog.csdn.net/20170722170254998?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

注：电源有全局属性，不需要连接；页的入口只和自己的子图连接；各个模块连接时，端口的输入还是输出的设置；子图由 sheet symbol 进行创建，也可由子图倒回去创建顶层。

各个子图模块的文件名不可省略，否则编译工程后子图将不能和总图形成层次结构。
