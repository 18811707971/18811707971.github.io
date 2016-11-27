---
layout: post
title: 4412开发板学习之Linux系统网络服务
date: 2016-08-05
categories: Linux
tags: [Linux,NFS]
description: about nfs study

---

注：[本文转自迅为讨论群](http://www.topeetboard.com)

1. 网络的基础知识

在学习网络之前，需要学习一些基础知识，不少用户对于这个概念非常模糊，导致IP地址或者网络环境稍微有点变化，就无法完成基础实验。如果没有接触过这些概念，建议将这一小节好好研究一下。

局域网通信中会经常用到“同一网段”的概念。要弄明白这个概念需要结合IP地址以及子网掩码。

IP地址

	关于IP地址的理论，网络上有大量的文章，这里主要是介绍和实验中需要理解的几个要点知识。
如下图所示，使用ifconfig，可以查看Ubuntu系统下的IP，下图表面Ubuntu的IP地址是192.168.0.226。

![这里写图片描述](http://img.blog.csdn.net/20160813230021828)


再来看一下开发板的IP，如下图所示，在查看最小文件系统的etc文件可以看到默认的IP地址为192.168.1.230。


![这里写图片描述](http://img.blog.csdn.net/20160813230052420)

上图是没有打包编译的最小系统，如下图所示，是运行着的最小linux系统，也是可以查询和修改IP地址的。


![这里写图片描述](http://img.blog.csdn.net/20160813230107797)

最小系统在开发板上运行的时候，也可以使用vi命令来修改IP地址。

子网掩码

子网掩码的查询和IP的查询方式一模一样。

如下图所示，使用ifconfig，可以查看Ubuntu系统下的子网掩码，下图表面Ubuntu的子网掩码是255.255.255.0。


![这里写图片描述](http://img.blog.csdn.net/20160813230122092)


再来看一下开发板的子网掩码，如下图所示，在查看最小文件系统的 etc/eth0-setting文件可以看到默认的子网掩码。

![这里写图片描述](http://img.blog.csdn.net/20160813230153999)


上图是没有打包编译的最小系统，如下图所示，是运行着的最小linux系统，也是可以查询和修改子网掩码。


![这里写图片描述](http://img.blog.csdn.net/20160813230204845)

最小系统在开发板上运行的时候，也可以使用vi命令来修改IP地址。

同一网段

本小节主要介绍如何判断两个IP是不是在同一网段。如果通过计算得出Ubuntu系统的IP和开发板的IP不在同一网段，则需要修改开发板的IP。
单纯的两个IP不能直接拿来确认是不是在同一网段，必须结合子网掩码经过计算才能得出结论。
计算方法是：两个IP分别和相应的子网掩码做与运算，得出的结果相等则表明在同一网段。

Ubuntu系统的IP和子网掩码分别是：192.168.0.226和255.255.255.0

开发板的IP地址和子网掩码分别是：192.168.1.230和255.255.255.0


进行与运算需要转换为二进制

Ubuntu系统：

IP：11000000 10101000 00000000 11100010

MASK：11111111 11111111 11111111 00000000

Result二进制：11000000 10101000 00000000 00000000

Result十六进制：192.168.0.0


开发板：

IP：11000000 10101000 00000001 11100110

MASK：11111111 11111111 11111111 00000000

Result二进制：11000000 10101000 00000001 00000000

Result十六进制：192.168.1.0

对比上面两个十六进制结果，可以发现这两个例子中的IP地址并不在同一个网段。开发板需要重新根据路由器或者交换机来修改IP。
路由器网络参数查询

如下图所示，作者的路由器地址为192.168.0.1，读者根据实际情况登陆路由器，路由器背面一般都有这个地址，如果没有查一下路由器的手册。

![这里写图片描述](http://img.blog.csdn.net/20160813230233202)


注意上图中WAN设置界面中的网关是192.168.1.1，这个是广域网的网关，实验中使用的是局域网的网关，需要选择“LAN口设置”再查找网关。
如下图所示，选择DHCP服务器，这里会有一个分配局域网中的IP区间。也就是说IP可以设置为192.168.0.220到192.168.0.250这个区间。

![这里写图片描述](http://img.blog.csdn.net/20160813230253659)



如下图所示，选择“LAN口设置”再查找，子网掩码为255.255.255.0。
另外在后面的NFS文件系统实验的时候需要上面的192.168.0.1这个地址，可以把这个地址看成局域网的网关，而不能使用WAN中的网关！

![这里写图片描述](http://img.blog.csdn.net/20160813230316940)

了解了IP地址，子网掩码，查看了路由器设置之后，首先可以确定子网掩码为255.255.255.0，然后IP地址可以处于192.168.0.220到192.168.0.250这个区间。大家可以在这个区间取一个地址来计算。
最后根据大家的实际情况，确认了IP地址和MASK地址之后，修改最小linux系统中的etc/eth0-setting文件，将它们的IP设置到同一网段！

2. 组建最小网络

在网络不是很发达的时代，要是想联网玩游戏，会把两台电脑直接用网线连接在一起，通过设置IPV4，这两台机器就组成了局域网。
部分读者，由于没有路由器和交换机，或者部分网络绑定了IP和MAC地址，无法分配IP地址。但是教程中不少实验都需要将开发板和Ubuntu设置在同一网段，也就是处于同一个局域网中。这时用户可以直接将将PC机的网口和开发板的网口用网线直连，它们在物理层上就已经处在局域网中，然后经过简单的网络配置就可以实现和局域网一模一样功能。
另外介绍一点需要交叉网线的知识，交叉网线是古老的技术，现在几乎所有的网络都支持网线的翻转，软件会自动识别和匹配，只要使用一根标准的网线就可以了，不需要关心这个问题。

PC网络配置

进入PC机的网络和共享中心，win7如下图所示，其它系统可以网上查找具体路径，单击进入“网络和共享中心”，单击选择本地连接，拔掉PC机的网线后，网络设置则如下图所示。

![这里写图片描述](http://img.blog.csdn.net/20160813230347769)

找到“本地连接”，单击弹出本地连接对话框之后，然后选择单击“属性”。在弹出本地连接属性对话框。需要配置IPV4，单击IPV4的设置，弹出IPV4设置的对话框。
然后强制设置两台机器在同一网段。

如下图所示，选择“使用下面的IP地址”然后设置IP和子网掩码以及网关。

IP地址：192.168.0.10	子网掩码：255.255.255.0	默认网关：192.168.0.1

![这里写图片描述](http://img.blog.csdn.net/20160813230407598)

单击确定，保存退出。

修改开发板设置并测试

设置好之后需要将开发板的网络配置文件修改一下才能实现tftp传输文件。

IP地址设置为：192.168.0.11

子网掩码Mask设置为：255.255.255.0

网关Gateway设置为：192.168.0.1

修改之后，如下图所示。

![这里写图片描述](http://img.blog.csdn.net/20160813230426895)


修改之后，将开发板和PC的网口用一根网线连接，重启开发板，测试网络是否通畅。

如下图所示，启动最小linux系统，使用ping命令测试。

这里PC机的IP是192.168.0.10，开发板的IP是192.168.0.11

![这里写图片描述](http://img.blog.csdn.net/20160813230450391)

如上图所示，可以看到有数据发送和返回，开发板和PC机的网络已经设置好了。

虚拟机的设置

虚拟机的网络模式需要设置为“启动自动连接”“桥接模式”，设置后启动Ubuntu。

Ubuntu开启之后，如果使用ifconfig查看的ip地址和前面PC的IP不在同一网段，可以手动设置IP地址，如下图所示。

单击右上角的“上传下传的图标”。在弹出框中单击“Edit Connnections...”。单击选择“Edit...”。如下图所示，选择“IPv4 Settings”，Method选择“Manual”，单击Add。

添加IP地址：192.168.0.12子网掩码：255.255.255.0网关：192.168.0.1

![这里写图片描述](http://img.blog.csdn.net/20160813230512375)

保存退出配置界面，完成之后Ubuntu终端中使用命令ifconfig命令查看网络，如下图所示。

![这里写图片描述](http://img.blog.csdn.net/20160813230522099)

测试最小网络

前面分别设置了PC机、开发板、Ubuntu的网络,开启虚拟机，用网线将开发板和PC连接起来。

首先测试虚拟机Ubuntu和PC之间的网络，如下图所示，在Ubuntu终端中输入命令“ping 192.168.0.10”，Ctrl+c可以结束。注意，测试Ubuntu和PC之间的通信，需要用网线将开发板和PC连接起来，否则将无法ping通。

![这里写图片描述](http://img.blog.csdn.net/20160813230532506)

接着测试开发板和Ubuntu之间的网络，使用命令“ping 192.168.0.11”如下图所示。

![这里写图片描述](http://img.blog.csdn.net/20160813230547256)

通过了三者之间的测试，局域网就搭建起来了，可以在这个局域网中进行后面tftp和nfs等网络实验。

3. 搭建TFTP服务器和TFTP传输

在前面的介绍控制led的程序的实现，需要把编译好的led可执行文件拷贝到文件系统里面，用make_ext4fs命令重新制作system.img文件系统，然后再烧写到iTOP-4412开发板，最后再运行led可执行文件。这种方式的缺点是每次修改了应用程序，都需要重新制作文件系统，在重新烧写文件系统，效率很低。

这一节先讲解如何通过TFTP来实现在线传输文件，先在虚拟机Ubuntu上搭建一个服务器，并在虚拟机Ubuntu上做客户端进行测试。
基本知识介绍
 
TFTP（Trivial File Transfer Protocol，简单文件传输协议），是一个基于UDP协议实现的用于在客户机和服务器之间进行简单文件传输的协议，适合于开销不大、不复杂的应用场合。TFTP协议专门为小文件传输而设计，只能从服务器上获取文件，或者向服务器写入文件，不能列出目录，也不能进行认证。
根据上面关于TFTP的介绍，实现TFTP我们需要搭建一个TFTP的服务器，iTOP-4412开发板当做客户端。

使用的虚拟机Ubuntu来当做服务器，下面我们先讲解一下服务器端的配置。

根据上面关于TFTP的介绍，实现TFTP最终需要搭建一个TFTP的服务器，iTOP-4412开发板当做客户端。

搭建服务器

在的虚拟机Ubuntu上打开终端，首先输入命令：sudo apt-get install xinetd，安装xinetd。安装完xinetd，接下来输入命令安装tftp和tftpd：sudo apt-get install tftp tftpd。
然后建立TFTP的配置文件，使用命令：vi /etc/xinetd.d/tftp建立文件，写入下面的代码：

	service tftp
	{
	socket_type	= dgram
	protocol	= udp
	wait	= yes
	user	= root
	server	= /usr/sbin/in.tftpd
	server_args	= -s /var/tftpboot/
	disable	= no
	per_source	= 11
	cps	= 100 2
	flags	= IPv4
	}
	
这个文件在粘贴内容之后，必须按照格式严格对齐。

其中server_args设置的/var/tftpboot目录是tftp服务器的目录，TFTP客户端就是从这个目录里面获取服务器上的文件的。使用命令mkdir /var/tftpboot建立tftp服务器的目录。
然后设置/var/tftpboot的访问权限为777。输入：sudo /etc/init.d/xinetd restart命令重启xinetd服务。

![这里写图片描述](http://img.blog.csdn.net/20160813230616391)

服务器到这一步就搭建好了，后面来进行测试。

服务器测试

本机测试：在/var/tftpboot下面建立一个文件test，在里面输入hello world,然后保存该文件。在启动另外一个终端。然后输入：tftp 127.0.0.1，如下图所示。

![这里写图片描述](http://img.blog.csdn.net/20160813230637289)

输入get test获取test文件，如下图：


![这里写图片描述](http://img.blog.csdn.net/20160813231318722)

在上图我们看到获取到test的信息13字节，然后输入q退出tftp，如下图：

![这里写图片描述](http://img.blog.csdn.net/20160813231305925)

然后我们输入ls查看当前目录下是否有test文件了，通过查看，发现当前目录下应经有test文件了，这就是/var/tftpboot目录下的那个test文件，如下图：

![这里写图片描述](http://img.blog.csdn.net/20160813231253753)

使用cat命令查看一下test文件的内容，如下图：

![这里写图片描述](http://img.blog.csdn.net/20160813231241144)

通过上面的截图，我们看到test文件里面的内容是hello world，与/var/tftpboot目录的test是一样的，至此我们的TFTP服务器端的配置和测试就完成了。

开发板网络配置

iTOP-4412开发板制作的Linux文件系统已经支持TFTP客户端命令了，编译Busybox时默认是支持该命令的，Linux文件系统在启动的时候通过脚本设置了以太网的IP地址是192.168.1.230，这个脚本是在文件系统的etc/eth0-setting文件中。
如果想修改默认的IP、子网掩码、网关、DNS以及MAC，只要编辑这个文件对应的选项就可以了。

客户端的测试

在iTOP-4412开发板上获取TFTP服务器上的文件，首先先看一下TFTP服务器的IP地址。笔者的TFTP虚拟机的IP地址是192.168.1.109，开发板的默认IP是192.168.1.230，它们在同一个网段，如果不在同一个网段需要修改的开发板的IP地址，修改方法就是前面介绍的修改etc目录下的eth0-setting文件。
连接开发板的网口到路由器上，然后输入ping命令确认开发板和TFTP服务器是连通的，如下图所示。

![这里写图片描述](http://img.blog.csdn.net/20160813231443752)

如果返回上面的信息就表示开发板和TFTP服务器是连通的，现在我们获取TFTP上的文件，在开发板的串口输入：tftp -g -l test -r test 192.168.1.109，如下图：

![这里写图片描述](http://img.blog.csdn.net/20160813231459707)

执行完上面的命令，已经把TFTP上的test文件下载到开发板的当前目录下了，使用cat查看当前目录下test文件中的内容，如下图：
![这里写图片描述](http://img.blog.csdn.net/20160813231109752)


通过上面的截图，可以看到文件的内容和TFTP上的文件内容是一样的，至此TFTP搭建完成了，以后再调试程序的时候可以用TFTP，这样就避免了每次都要生成镜像，并烧写镜像了。

TFTP传输应用程序

下面演示一下调试应用程序的方法，在虚拟机的Ubuntu上编写一个输出helloworld的小程序，然后我通过交叉编译器编译这个程序生成了可执行文件helloworld，通过cp命令把这个可执行程序helloworld拷贝到了/var/tftpboot目录下面，接下来我们要在开发板端把hello文件下载到开发板上，并在开发板的串口上执行：tftp -g -l helloworld -r helloworld 192.168.1.109，如下图所示。

![这里写图片描述](http://img.blog.csdn.net/20160813231055295)

然后输入ls命令查看一下当前目录，可以看到已经有helloworld这个文件了。输入“chmod 777 helloworld”命令来修改下helloworld的权限，最后运行helloworld
使用命令“./helloworld”运行helloworld程序，可以看到程序输出：“hello world！”了。通过TFTP这种方式可以提高调试程序的效率。

4. NFS网络文件系统

NFS（网络文件系统）是一种很有用的技术，利用它不仅可以通过网络启动文件系统，也可以利用它来很方便的进行程序调试等工作。


基本环境介绍

使用的软硬件环境是：一台PC，上面运行虚拟机Ubuntu，iTOP-4412开发板一块，PC机和iTOP-4412开发板通过网线连接到路由器上面，路由器分配的网段是192.168.1.x的ip地址，默认网关是路由器的ip地址，即192.168.1.1。PC机的ip地址是192.168.1.103，iTOP-4412开发板的ip地址是192.168.1.230，网段的子网掩码是255.255.255.0。这几个ip地址在后面配置iTOP-4412开发板内核nfs的时候会用到，大家需要根据自己使用的网络环境来设置这几个ip地址，设置的原则是确保PC的ip和iTOP4412开发板的ip在同一个网段。
NFS是Network FileSystem的缩写，是由SUN公司研制的UNIX表示层协议（pressentation layer protocol），NFS是基于UDP/IP协议的应用。它的最大功能就是可以通过网络让不同的机器，不通的操作系统彼此共享文件，可以通过NFS挂载远程主机的目录，访问该目录就像访问本地目录一样，所以也可以简单的将它看做一个文件服务器。通过NFS服务，可以实现在线调试文件系统或应用程序，而不用像传统的方式生成文件系统镜像，然后烧写到iTOP-4412的eMMC里，最后再启动开发板。通过NFS服务可以提高我们的调试效率。

搭建NFS服务器

实现NFS，需要一个主机作为NFS服务器，这里选择我们的虚拟机Ubuntu作为主机。

首先需要在虚拟机的Ubuntu上安装Ubuntu NFS服务，这是一个软件包，可以使用apt命令下载（下载前请确认你的虚拟机可以上网）。打开终端,登陆root用户，输入“apt-get install nfs-kernel-server”命令安装Ubuntu NFS服务，在安装的过程中会提示“是否继续安装”的信息，输入“Y”，然后按回车，继续安装。
安装完成，接下来需要配置/etc/exports，使用vi命令打开/etc/exports，在/etc/export文件的最后一行添加：
/home/topeet/linux/ *(rw,sync,no_root_squash)，如下图：

![这里写图片描述](http://img.blog.csdn.net/20160813231010872)

下面讲解一下上面输入的内容都代表什么意思：

	/home/topeet/linux：是要共享的目录；
	*：代表允许所有的网络段访问；
	rw：是可读写权限；
	sync：是资料同步写入内存和硬盘；
	no_root_squash：是Ubuntu nfs客户端分享目录使用者的权限，如果客户端使用的是root用户，那么对于该共享目录而言，该客户端就具有root权限；
	其他Ubuntu nfs常用的参数有：
	ro：只读访问
	async ：nfs在写入数据前可以相应请求
	secure： nfs通过以下的安全TCP/IP端口发送
	insecure： nfs通过1024以上的端口发送
	wdelay：如果多个用户要写入NFS目录，则归组（默认）
	no_wdelay：如果多个用户要写入NFS目录，则立即写入，当使用async时，无需此设置
	hide：在nfs共享目录中不共享妻子目录
	no_hide：共享nfs目录的子目录
	subtree_check：如果共享/usr/bin之类的子目录时，强制nfs检查父目录的权限（默认
	）
	no_subtree_check：和上面相对，不检查父目录权限
	all_squash：共享文件的UID和GID映射匿名用户anonymous，适合公用目录
	no_all_squash：保留共享文件的UID和GID（默认）
	root_squash root：用户的所有请求映射成如anonymous用户一样的权限（默认）
	no_root_squash root：用户具有根目录的完全管理访问权限
	anonuid=xxx：指定nfs服务器/etc/passwd文件中匿名用户多的UID
	anongid=xxx：指定nfs服务器/etc/passwd文件中匿名用户的GID


接下来重启portmap服务，在控制台输入“/etc/init.d/portmap restart”命令，然后重启nfs服务，输入“/etc/init.d/nfs-kernel-server restart”命令。

现在Ubuntu nfs服务器端的配置就完成了，

测试NFS服务器

完成前面的搭建工作之后，就可以在虚拟机Ubuntu本机上验证一下，例如把/home/minilinux/system挂载到/mnt目录下，需要输入“mount -t nfs localhost:/home/minilinux/system /mnt”命令。
执行完该命令之后，可以使用df命令查看是否挂载成功，如下图所示。

![这里写图片描述](http://img.blog.csdn.net/20160813230934168)

可以看到上图中高亮的那一行就是我们挂载的nfs文件系统，这说明我们的Ubuntu nfs服务器端搭建成功了。
本节用前面章节制作的最小Linux系统为例，通过nfs来挂载它。把Linux最小文件系统放到虚拟机Ubuntu的/home/minilinux/system目录下。
实现nfs文件系统需要修改Linux最小文件系统的etc/init.d/ifconfig-eth0文件，使用vi命令修改第八行，修改为“if grep -q nfs /proc/mounts ; then”，修改完成后保存并退出。

配置光盘内核

下面开始配置iTOP-4412开发板的内核。要实现nfs功能，需要修改内核配置来支持，打开内核源码，使用支持Linux最小文件系统的内核缺省配置文件（对应qt的缺省文件）。
输入make menuconfig命令进入Linux配置界面，进入“Networking support”→“Networking support”→“Networking options”，在Networking options配置界面，选中“IP: kernel level autoconfiguration”。
然后返回到make menuconfig的初始界面，选择“File systems”→“Network File Systems”。进入Network File Systems配置界面选中“NFS client support”，“NFS client support for NFS version 3”，“NFS client support for the NFSv3 ACL protocol extension”，“NFS client support for NFS version 4”，“NFS client support for NFSv4.1”，“Root file system on NFS”。
再次返回到make menuconfig的初始配置界面，在初始配置界面选中“Boot options”。按回车进入Boot options配置界面，然后再Default kernel command里面输入：
root=/dev/nfs rw nfsroot=192.168.1.103:/home/topeet/linux/app/system ip=192.168.1.230:192.168.1.103:192.168.1.1:255.255.255.0:iTOP:eth0:off  rootfstype=ext4 init=/linuxrc console=ttySAC2,115200"。

下面讲解一下上面输入命令的命令里面的几个参数：

root=/dev/nfs rw nfsroot=192.168.1.103:/home/minilinux/system表示挂载的nfs服务器ip是192.168.1.103，挂载的目录是/home/minilinux/system，（注意：/home/minilinux/是前面我们搭建nfs服务器设置的）
ip=192.168.1.230:192.168.1.103:192.168.1.1:255.255.255.0:iTOP:eth0:off这里面，第一个ip192.168.1.230是我们开发板的ip地址，第二个ip192.168.1.103是nfs服务器的ip，第三个ip192.168.1.1是开发板的网关，255.255.255.0是子网掩码，iTOP是开发主机的名字（一般无关紧要，可以随便填写），eth0是网卡设备的名称。

至此，Linux内核的配置就完成了，退出make menuconfig配置，会弹出保存窗口，使用键盘的左右方向键，选中上图的”Yes“，然后输入回车，退出保存配置，如下图：
然后输入make命令开始编译内核，这样Linux内核支持nfs制作完成了。

将新生成的内核烧写到开发板，重启开发板，就可以使用NFS文件系统了。

配置github内核

如果使用的是从github上下载的内核，那么除了需要严格按照前一小节中步骤配置以外，还需要简单的修改一下内核源码，屏蔽其中的调试信息。这部分需要先学习怎么使用github下载和编译，具体参考使用手册的附录。
使用命令“find ./ -name dm9620.c”查找网卡的驱动源码。dm9620网卡驱动路径为“drivers/net/usb/dm9620.c”。使用vim编辑器打开，在文件中查找关键词“LEN_PLOAD”。如下图所示，将框中的代码注释掉。

![这里写图片描述](http://img.blog.csdn.net/20160813230754439)

保存退出，然后重新编译，内核在有线网通信的就不会一直打印调试信息了。

利用NFS调试程序

作者的NFS挂载的目录是“/home/minilinux”，以helloword程序为例来实现NFS调试。在虚拟机的终端将helloworld可执行文件拷贝到/home/minilinux/system/bin目录下。接下来启动开发板，需要使用NFS文件系统启动开发板挂载。
在超级终端中输入“ls /bin/”命令，可以查看根文件系统下的文件，下翻显示窗口，可以看到helloworld。使用命令“chmod 777 /bin/helloworld”修改文件权限,输入”./bin/helloword”命令运行”helloword“，运行结果如下图所示。

![这里写图片描述](http://img.blog.csdn.net/20160813230743970)

通过上面的步骤就可以使用NFS很方便的调试程序了。
