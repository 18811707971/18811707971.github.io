---
layout: post
title: 迅为4412开发板学习笔记-TFTP服务器的搭建
date: 2016-08-03
categories: Linux
tags: [Linux,TFTP]
description: 搭建TFTP服务器

---

注：TFTP的搭建

[转自iTOP-4412讨论群] (http://www.topeetboard.com) 

一、TFTP服务器的搭建与测试：
TFTP是用来下载远程文件的最简单网络协议，它其于UDP协议而实现。嵌入式linux的tftp开发环境包括两个方面：一是linux服务器端的tftp-server（服务器）支持，二是嵌入式目标系统的tftp-client（客户端）支持。因为u-boot本身内置支持tftp-client，所以嵌入式目标系统端就不用配置了。

下面就详细介绍一下linux服务器端tftp-server的配置:
实际上我们同样可以在windows上搭建TFTP服务器，之后的话我可能会对这部分内容作一些补充。但这里我主要讲解一下在Linux下的TFTP服务器搭建：

1、搭建TFTP服务器我们需要三个东西：

xinetd、tftp、tftpd，在保证联网的情况下安装它们。

sudo apt-get install xinetd

sudo apt-get install tftp

sudo apt-get install tftpd

2.建立TFTP的配置文件（这一步比较容易出错）：

vi /etc/xinetd.d/tftp

输入以下代码：

		service tftp
		{
		socket_type         = dgram
		protocol            		= udp
		wait                            = yes
		user                            = root
		server              = /usr/sbin/in.tftpd
		server_args         = -s /var/tftpboot/
		disable             = no
		per_source          = 11
		cps                 		= 100 2
		Flags               		= IPv4
		}

代码格式如下图所示（用tab键隔开，不建议使用空格）

 ![这里写图片描述](http://img.blog.csdn.net/20160813224541022)

其中的server_args是tftp 服务器的目录。TFTP 客户端就是从这个目录里面获取服务器上的文件的。上图使用的是/var/tftpboot，用户可以自己定义。

3、创建目录：mkdir  /var/tftpboot

4、改变权限：chmod  777  /var/tftpboot

5、启动xinetd服务：sudo  /etc/init.d/xinetd  restart

6、测试TFTP服务器：

输入命令：netstat -a | grep tftp

![这里写图片描述](http://img.blog.csdn.net/20160813224639684)
 
出现上图的情况说明配置成功

我们可以本地测试一下tftp服务器：

①在tftpboot目录下创建一个文本文件，随便写点什么：

例如vi /var/tftpboot/tftptest

输入“This is TFTPTEST!”，保存退出。

②随便进入一个你常用的目录，作为下载文件的目的目录，输入tftp(也可以写成tftp localhost或者tftp 127.0.0.1)，连接到本地的tftp服务器。

③get test下载文件到目的目录，q退出，ls就可以看到文件已经到了你的当前目录。cat test 就可以看到了文件内容。

7、TFTP操作：

注意tftp命令行下是不支持Linux命令的，它有自己独特的命令，使用?命令可以看到。

        connect：连接到远程tftp服务器        
        mode：文件传输模式
        put：上传文件
        get：下载文件        
        quit：退出
        verbose：显示详细的处理信息
        tarce：显示包路径
        status：显示当前状态信息        
        binary：二进制传输模式
        ascii：ascii传送模式
        rexmt：设置包传输的超时时间
        timeout：设置重传的超时时间		
		?：帮助信息

8.开发板连接tftp客户端

注：在连接开发板和ubuntu的tftp服务器之前，一定要首先保证开发板和ubuntu的ping通，在保证ubuntu和开发板能够ping通的条件下，就可以使用tftp服务器了。

①把需要下载的文件拷贝到/var/tftpboot目录下,

②开发板下载文件：

命令格式tftp [option] ... host [port]

[option]:
-g表示下载文件(get)
        -p表示上传文件(put)
        -l表示本地文件名(local file)
        -r表示远程主机的文件名(remote file)
		
例如：tftp -g -l helloworld -r helloworld 192.168.1.109

5.实验现象：

	IP设置：（我是通过路由器分配的IP）
	宿主机IP：192.168.1.100
	 Ubuntu IP：192.168.110
	 开发板 IP:192.168.105
	子网掩码：255.255.255.0

![这里写图片描述](http://img.blog.csdn.net/20160813224951352)
