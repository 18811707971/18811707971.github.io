---
layout: post
title: 基于itop4412在Linux最小系统下的 ffmpeg 的移植和测试
date: 2017-01-15
categories: 2016 Project
tags: [Linux,ffmpeg]
description: about Linux ffmpeg compile

---

近期在做一个视频监控的项目，在网上了解到如下的方案：

	用的V4L2的视频驱动，然后配合ffmpeg、x264的软件编解码，通过udp上传至pc显示，配合开源的编解码库，实现h.264的流编码与传输。

前面我们已经实现了x264编码库的移植，并且做了测试。下面就进行ffmpeg库的编译。了解到程序运行时还需要libz.so这个库支持，所以下载zlib-1.2.5.tar.gz 源文件交叉编译。

1、编译zlib

[下载地址] (http://down1.chinaunix.net/distfiles/zlib-1.2.1.tar.bz2)

解压zlib-1.2.5.tar.gz

	#tar -vxf zlib-1.2.5.tar.gz

进入源文件目录

	#cd zlib-1.2.5

运行configure配置makefile

	#./configure --prefix=/opt/wecam/ffmpeg

--prefix=/opt/wecam/ffmpeg:

是最后执行 make install 时，将文件安装在 /opt/webcam/ffmpeg ，不然会默认安装在 /usr/local/lib 中，其实这里不写这个参数，最后一起在              makefile 中修改也可以的。

配置后会在当前目录下生成 Makefile

修改 Makefile如下：

![这里写图片描述](http://img.blog.csdn.net/20170115094931479?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

然后make，make install

	#make
	
	#make install

zlib的编译结束后，在/opt/wecam/ffmpeg中可以看到编译好的文件，在子文件夹lib中的.so文件最后要复制到4412开发板上。

2.编译ffmpeg

下载地址(http://down1.chinaunix.net/distfiles/ffmpeg-1.0.tar.bz2)

解压ffmpeg-0.10.1.tar.gz

	#tar -vxf ffmpeg-0.10.1.tar.gz
	
	#cd ffmpeg-0.10.1
	
	#./configure --enable-cross-compile --target-os=linux --cross-prefix=arm-linux- --cc=arm-linux-gcc --enable-shared --arch=armv6 --enable-gpl --enable-libx264 --prefix=/opt/wecam/ffmpeg --extra-cflags=-I/opt/wecam/x264/include --extra-ldflags=-L/opt/wecam/x264/lib/

配置需要注意的是--extra-cflags=-I/opt/wecam/ffmpeg/include --extra-ldflags=-L/opt/wecam/ffmpeg/lib/ 指定x264库的位置

config.mak 文件配置如下：

![这里写图片描述](http://img.blog.csdn.net/20170115095918929?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

	#make
	
	#make install

编译完成后在/opt/wecam/ffmpeg目录下可以找到相应的文件：

库文件：

![这里写图片描述](http://img.blog.csdn.net/20170115100130933?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

可运行命令：

![这里写图片描述](http://img.blog.csdn.net/20170115100226528?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

头文件：

![这里写图片描述](http://img.blog.csdn.net/20170115100316107?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

3.测试

我首先打算使用 U 盘将相关的库文件拷贝到开发板，但是有些文件在拷贝的过程中出错（看了属性是符号链接），然后我就把编译出来的库文件直接先拷贝到 ubuntu 上的最小系统的 /lib/ 目录下，再用：
生成linux最小系统：

		make_ext4fs -s -l 314572800 -a root -L linux system.img system
		
重新得到Linux最小系统的镜像，烧写到开发板，这样就将所有的库文件放到了运行的开发板上。

插上摄像头，开始测试

我将 ffmpeg 等相关命令通过 tftp 下载到如下目录：

![这里写图片描述](http://img.blog.csdn.net/20170115101914068?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

执行命令

	# ./ffmpeg -f video4linux2 -s 320x240 -i /dev/video4 /mnt/test.avi
	
采集过程如下：

注：迅为的 4412 开发板的 USB 摄像头的设备节点为/dev/video4

![这里写图片描述](http://img.blog.csdn.net/20170115102123856?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
	录像过程输入q，即可停止录像。生成的test.avi文件在/mnt/目录下。这个拷贝到 PC ，用普通的播放器就可以观看。
	
![这里写图片描述](http://img.blog.csdn.net/20170115102257980?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

播放;

![这里写图片描述](http://img.blog.csdn.net/20170115103007457?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

参考：

(1)[成功在mini2440上移植ffmpeg](http://blog.csdn.net/mashang123456789/article/details/8673426)

(2)[基于H.264的远程视频监控](http://blog.csdn.net/jinhongdu/article/details/10632535)

(3)[arm mini2440 基于v4l2 ffmpeg x264的视频远程监控](http://blog.csdn.net/ghostyu/article/details/7371310)
