---
layout: post
title: orcad创建元器件方法
date: 2017-10-21
categories: Allegro学习
tags: [Allegro学习,orCAD,嵌入式]
description: 绘图仿真工具cadence学习
---


## **1. orcad创建简单分裂元器件**

这里以flash芯片为例，说明创建简单元器件原理图符号的方法。

（1）新建一个工程，在工程里建立一个库，保存。

![这里写图片描述](http://img.blog.csdn.net/20171021113212193?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

（2）在原理图库文件下新建一个part，保存

![这里写图片描述](http://img.blog.csdn.net/20171021113339656?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

（3）进入建立的part文件，可以看到已经有一个大概的轮廓图（注意是单独一个部分，还是由多个部分组成）

![这里写图片描述](http://img.blog.csdn.net/20171021113620210?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 
 然后放置管脚，配置属性 ，一般配置为passive，电源管脚选择Power

![这里写图片描述](http://img.blog.csdn.net/20171021113849867?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 
 依次放置其他管脚，调整宽度，放置矩形构成外框，保存。

![这里写图片描述](http://img.blog.csdn.net/20171021114024555?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

至此就完成简单器件原理图绘制，然后在place part就可以看到刚创建的库文件。

![这里写图片描述](http://img.blog.csdn.net/20171021114323703?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



**默认常用阻容库路径：** E:\allegro\tools\orcad\library

___
 
## **2. orcad创建homogeneous类型元器件**

以LM358双运放为例

（1）建立Part

![这里写图片描述](http://img.blog.csdn.net/20171021135245129?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

得到：

 
![这里写图片描述](http://img.blog.csdn.net/20171021135337761?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20171021135408920?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

（2）绘制A部分

![这里写图片描述](http://img.blog.csdn.net/20171021135742278?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

（3）绘制B部分

因为A，B是一模一样的，所以完成A部分，B部分也基本完成，只需将管脚标号设置就可。

![这里写图片描述](http://img.blog.csdn.net/20171021135953729?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 
 （4）检验，放置

![这里写图片描述](http://img.blog.csdn.net/20171021140034953?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 
 
 **注：** 两个部分切换，下一个part：Ctrl+N,上一个：Ctrl+B 
 
___

## **3. orcad创建heterogeneous类型元器件**

（1）同样以LM358为例，先绘制A部分

![这里写图片描述](http://img.blog.csdn.net/20171021142708010?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

（2）绘制B部分

查看B部分，heterogeneous绘制时，B部分也是空的，需要重新绘制，而不像homogeneous，绘制好A，B也完成，这就是两种方式的区别。

![这里写图片描述](http://img.blog.csdn.net/20171021143030206?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 
 ____
 
 
## **4. orcad运用excel表格来创建复杂元器件**
 
对于管脚比较多的器件，我们按照常规方法一个一个的添加，效率比较低，也容易出错，这里 将用Excel导入管脚的方法创建复杂器件。

（1）在库文件里添加一个part，然后place pin array放置所有管脚，这里以AD9135为例，共有88个引脚。

![这里写图片描述](http://img.blog.csdn.net/20171021161331529?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

（2）放置好管脚后，选择所有管脚，右键编辑属性。

![这里写图片描述](http://img.blog.csdn.net/20171021161633975?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

主要配置的是number和name两项。点击空白，选中全部，按下Ctrl+insert复制（笔记本为Ctrl+Fn_insert）。新建一个Excel文档，将复制的信息粘贴到里面。
 

（3）打开器件datasheet，一般是PDF，这里为了方便复制管脚，先转为word格式，转换软件-----solidconverterpdf。将管脚的number和name复制到Excel，调整到与orCAD复制出来的一致。

![这里写图片描述](http://img.blog.csdn.net/20171021162233108?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

得到：

![这里写图片描述](http://img.blog.csdn.net/20171021162351719?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


（4）选中Excel里的文件信息，复制。回到绘制界面，同样选中属性表，将信息粘贴进去。

![这里写图片描述](http://img.blog.csdn.net/20171021162623257?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

点击OK，完成配置。调整管脚位置，可以按照datasheet，也可以功能一样的放到一起，并且对于和电源相关的管脚，更改属性为power，否则有警告。保存，到此，一个复杂的器件就绘制完成了。

![这里写图片描述](http://img.blog.csdn.net/20171021162957515?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 
 


 
 

 

 
 
 
 
 
 