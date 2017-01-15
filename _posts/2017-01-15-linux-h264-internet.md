---
layout: post
title: 基于itop4412在Linux最小系统下的 RTP传输H264视频VLC播放
date: 2017-01-15
categories: Linux,RTP,VLC
tags: [Linux,V4L2]
description: about Linux H264 transmission

---

前面已经将 USB 摄像头采集的yuv(4:2:2)的视频通过 x264 编码库编码为 H264 文件，接下来我们就将编码出来的视频通过 RTP 协议以 UDP 的方式传送到 PC 用VLC 播放器播放。

1.RTP传输h264码流例子 

下载地址：(http://download.csdn.net/detail/shenxingdeliulu/8607533)

需要修改两个地方:

rtp.h:ip修改为 PC 的IP

![这里写图片描述](http://img.blog.csdn.net/20170115172659438?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

trp.c : 修改打开的文件(注意路径)

![这里写图片描述](http://img.blog.csdn.net/20170115172846363?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

拷贝到 ubuntu 编译：

	#arm-none-linux-gnueabi-gcc -o rtp rtp.c -static

编译完成后拷贝到开发板上：

![这里写图片描述](http://img.blog.csdn.net/20170115173159321?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

这样开发板端的工作就准备完成了，下面是 PC 端的 VLC 的配置。

2.VLV 需要配置一个 .sdp 文件 ，如下：

			m=video 1234 RTP/AVP 96
			a=rtpmap:96 H264
			a=framerate:25
			c=IN IP4 192.168.1.105

参数解释：

1. m= ：媒体级会话的开始处，video：媒体类型 ； 1234：端口号  ；RTP/AVP：传输协议 ； 96：rtp头中的payload格式

2. a=rtpmap：证明是动态绑定的进一步说明；96：rtp头中的payload格式； H264：编码名 ；缺省了时钟速率，应该为90000,这里的90000是指1s采集90000个字节

3. a=framerate:15    指1s播放几个rtp包，单位帧每秒，倒数为一个rtp包承载的数据播放的时间，单位s 90000/15   表示每个时间戳增量值

4. c=：媒体链接信息； IN：网络类型一般为IN； IP4：地址类型一般为IP4；后面是IP地址（注意是发送方的IP:即开发板的IP地址）

打开 .sdp 文件的方式：
 
 (1)打开流菜单：

![这里写图片描述](http://img.blog.csdn.net/20170115174344572?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

(2)加入 .sdp 文件

![这里写图片描述](http://img.blog.csdn.net/20170115174621388?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

至此，配置完成。我们在 4412 开发板上运行 ./rtp 的同时播放 VLC ，即可看到视频 (有点延时)。

3.测试

开发板和 PC 在同一网段，通过网线连接。

开发板：执行 rtp 文件：

![这里写图片描述](http://img.blog.csdn.net/20170115175229658?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170115175311118?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

注: 黄色框中的信息为网线连接时的自带信息。

PC 端：

打开 .sdp 文件后，播放，在执行 rtp 文件后可以在 VLC 看到编码的 h264 视频。

![这里写图片描述](http://img.blog.csdn.net/20170115175526480?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

至此，在局域网直接通过网线连接的视频传输测试完成，但是还有许多的问题有待解决，代码的细节处还要努力搞懂，后面还要继续用 WIFI  , 4G 网络传输，还有硬件编解码，也许这才是刚刚开始。

此外，我参考下面这位兄弟的方式，直接通过 ffmpeg 命令执行传输，也可以在 VLC 上看到实时采集的视频，但是延时很大，基本要隔一会才能看到摄像头采集的视频。

[【NanoPi NEO试用体验】结项 基于H264 RTP的图传系统 ](http://bbs.elecfans.com/forum.php?mod=viewthread&tid=1105897)

现象：

录制：

![这里写图片描述](http://img.blog.csdn.net/20170115181511522?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

VLC 播放：

![这里写图片描述](http://img.blog.csdn.net/20170115181534216?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

参考：

(1)[s5pv210+v4l2+h264硬件编码+RTP协议传输+SDP文件的嵌入式视频监控](http://blog.csdn.net/shenxingdeliulu/article/details/45081747)

(2)[arm mini2440 基于v4l2 ffmpeg x264的视频远程监控](http://blog.csdn.net/ghostyu/article/details/7371310)

(3)[Linux下H.264码流实时RTP打包与发送](http://blog.csdn.net/sdvch/article/details/46137311)

(4)[vlc播放264 rtp流媒体sdp文件及其参数介绍](http://blog.csdn.net/zhangjikuan/article/details/27378237)

(5)[实现RTP协议的H.264视频传输系统](http://2343338.blog.51cto.com/2333338/455056)

其中，(1)这个博主写的很详细，我基本是按照他的博客实现的，非常感谢大家。





