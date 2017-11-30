---
layout: post
title: Altium Designer 建立原理图元器件——Excel
date: 2017-11-30
categories: 电子之路
tags: [Altium Designer,SCH]
description: Altium Designer STUDY AND APPLICATION
---

本文以创建STM32F103RC为例。

___

#### **1.新建一个原理图库文件**

![这里写图片描述](http://img.blog.csdn.net/20171130093702660?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### **2.放置矩形框**

![这里写图片描述](http://img.blog.csdn.net/20171130094034099?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### **3.放置Pin**

![这里写图片描述](http://img.blog.csdn.net/20171130094256877?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

选择刚放置的连线，按 Ctrl+C复制，然后删掉刚放置的连线，再选择Edit->Paste Array选项，如下图：

![这里写图片描述](http://img.blog.csdn.net/20171130095334044?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

Vertical是垂直向的增量，即管脚之间的间距，因坐标的关系，向下为负向，坐标的一列选-10，点OK之后，放置好管脚。完成其他部分管脚，如下图，等标识完成了，最后再进一步调整。

![这里写图片描述](http://img.blog.csdn.net/20171130100030582?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### **4.打开数据手册，查找管脚信息**

这里用的是LQFP64的封装，我们在pdf中查找相应的管脚信息，为了复制信息方便，可先把PDF转为WORD。

![这里写图片描述](http://img.blog.csdn.net/20171130100540009?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### **5.提取引脚信息**

我们新建一个Excel文档，用来存放引脚信息。如下图：

![这里写图片描述](http://img.blog.csdn.net/20171130104014007?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### **6.导入引脚信息**

选中原理图中的所有引脚，如下图：

![这里写图片描述](http://img.blog.csdn.net/20171130111042506?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

在界面的右下角选择的SCH菜单，选择SCHLIB List弹出下图：

![这里写图片描述](http://img.blog.csdn.net/20171130110607972?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

右键并选择第一项，Switch to Edit Mode，进入可编辑模式：

![这里写图片描述](http://img.blog.csdn.net/20171130110816235?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

全选name项目下方的全部内容，回到Excel表格，复制对应管脚标识（name），然后右键选择Paste到SCH List 的Name栏目：

![这里写图片描述](http://img.blog.csdn.net/20171130111135094?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

完成粘贴后，管脚名称如图:

![这里写图片描述](http://img.blog.csdn.net/20171130113334111?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

调整原理图器件大小，管脚对齐等，至此完成。

![这里写图片描述](http://img.blog.csdn.net/20171130113447544?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



