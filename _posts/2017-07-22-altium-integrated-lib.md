---
layout: post
title: Altium Designer 集成库的创建
date: 2017-07-22
categories: 电子之路
tags: [集成库,PCB]
description: PCB设计
---


集成库的创建主要为了避免原理图库，封装库被修改，在团队设计一个项目时，提高了安全性。下面记录一下创建的方法。

1.分别创建集成库文件、原理图库、封装库三个文件，保存到同一个文件夹目录下。

![这里写图片描述](http://img.blog.csdn.net/20170722211443895?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

2.先打开原理图库，绘制器件原理图文件，也可以从其他的库文件中拷贝。

![这里写图片描述](http://img.blog.csdn.net/20170722211952501?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

这里原理图库中有两个文件，保存。

3.同样，打开封装库，创建器件封装库文件，也可直接复制。

![这里写图片描述](http://img.blog.csdn.net/20170722212428973?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

保存。

注：SCH 和 PCB 的库文件可以不互相对应。

4.建立原理图和封装库的对应关系

回到原理图库编辑界面，在 Tools 的菜单下，选择models manage

![这里写图片描述](http://img.blog.csdn.net/20170722213750541?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

跳转到如下界面，分别是器件原理图和模型选择

![这里写图片描述](http://img.blog.csdn.net/20170722214237339?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

点击封装库，添加对应的封装（也可以添加其他模型）

![这里写图片描述](http://img.blog.csdn.net/20170722214341512?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

在封装库选择的时候，我们就可以定位到刚才建立的库，添加对应封装。

5.添加完成后，保存，编译集成库文件

![这里写图片描述](http://img.blog.csdn.net/20170722214651487?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

编译完成后，就可以在 library 面板下找到刚才新建的集成库。到此，一个集成库就建立完成了。

![这里写图片描述](http://img.blog.csdn.net/20170722214907684?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


_____

我们也可以在集成库下，仅仅建立原理图文件，从其他路径添加封装，这样也可以建立集成库。一般为了方便，还是同时建立原理图库和封装库。

