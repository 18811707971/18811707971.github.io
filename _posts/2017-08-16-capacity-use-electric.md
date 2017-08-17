---
layout: post
title: 电路设计——电容作用
date: 2017-08-16
categories: 电子之路
tags: [电容]
description: 电容作用解析
---

这几天的工作主要是学习一些模块的原理图，看看数据手册，相对轻松一些。在设计电路时，电容在电源转换，控制器供电，信号的滤波等很多地方都不可或缺，就顺便把电路中电容的主要作用及应用总结一下。

**1.容抗计算**

![这里写图片描述](http://img.blog.csdn.net/20170816164117830?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**2.电容的特性**

	（1）隔直流通交流，通高频阻低频；

	（2）电容两端的电压不能够突变；

	（3）大容值电容滤低频噪声，小容值电容滤高频噪声。

	（3）电容的工作的实质是充电和放电的过程，它是一种储能元件：电容在某时刻储存的电场能量只与该时刻其端电压有关。当电压增加时，电容元件从电源吸收能量，储存在电场中的能量增加，这个过程称为电容的充电过程。当电压减小时，电容元件向外释放电场能量，这个过程称为电容的放电过程。电容在充放电过程中并不消耗能量。
	
**3.常见电容的作用**

**主要用于电源滤波、信号滤波、信号耦合、谐振、滤波、补偿、充放电、储能、旁路、隔直等电路中。**

**1.电源滤波：**

![这里写图片描述](http://img.blog.csdn.net/20170816171458929?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

滤波是电容的作用中很重要的一部分。几乎所有的电源电路中都会用到，以滤除直流电源中不需要的交流成分，使直流电变平滑。。从理论上说，电容越大，阻抗越小，通过的频率也越高。但实际上**超过1uF 的电容大多为电解电容**，有很大的电感成份，所以频率高后反而阻抗会增大。有时会看到有一个电容量较大电解电容并联了一个小电容，这时大电容通低频，小电容通高频。电容越大低频越容易通过，电容越小高频越容易通过。

**2.去耦电容和旁路:**

旁路电容是主要是把输入信号的高频噪声滤除，而去耦电容是把输出信号的干扰去除。

通常旁路电容的大小0.1u，0.01u;去耦电容大小为10uF。

![这里写图片描述](http://img.blog.csdn.net/20170816172848734?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**去耦电容**：一般接在电源线和地线之间

1）作为集成电路的蓄能电容;

2）滤除该器件的高频噪声;

3）防止电源携带高频噪声对电路构成干扰;

关于蓄能的理解：

由于电源离芯片电源的引脚有一定距离，为了保证器件需要电流的时候，电流能够即使供给，所以在器件的vcc引脚加上一个电容，提供电流。芯片电源引脚连接的电容尽量要靠近，电源输出时，电容也要尽量靠近电源输出引脚。

**旁路电容**：一般接在信号端与地之间，主要功能是产生一个交流分路，从而消去进入易感区的那些不需要的能量。接在交、直流信号的电路中，将电容并接在电阻两端或由电路的某点跨接到公共电位上，为交流信号或脉冲信号设置一条通路，避免交流信号成分因通过电阻产生压降衰减。

旁路电容一般作为高频旁路器件来减小对电源模块的瞬态电流需求。通常铝电解电容和钽电容比较适合作旁路电容，其电容值取决于PCB 板上的瞬态电流需求，一般在10 至470μF 范围内。若PCB 板上有许多集成电路、高速开关电路和具有长引线的电源，则应选择大容量的电容。旁路电容是为本地器件提供能量的储能器件，它能使稳压器的输出均匀化，降低负载需求。就像小型可充电电池一样，旁路被充电，并向器件进行放电。

**区别：**在电路电路中，去耦电容和旁路电容都是起到抗干扰的作用，电容所处的位置不同，就是称呼的不一样，**旁路是把输入信号中的干扰作为滤除对象**，而**去耦是把输出信号的干扰作为滤除对象**，防止干扰信号返回电源。

**3.耦合**

接在交流信号处理电路中，用于连接信号源和信号处理电路或者作两放大器的级间连接，用以隔断直流，让交流信号或脉冲信号通过，使前后级放大电路的直流工作点互不影响。

![这里写图片描述](http://img.blog.csdn.net/20170816173907049?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**4.谐振**

包括RC、LC振荡器及晶体的负载电容都属于这一范畴。对与频率相关的电路进行系统调谐，比如收音机、电视机。 

![这里写图片描述](http://img.blog.csdn.net/20170816174446985?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**5.储能**

储能型电容器**通过整流器收集电荷**，并将存储的能量通过变换器引线传送至电源的输出端。电压额定值为 40 ～ 450VDC 、电容值在 220 ～ 150 000μF 之间的铝电解电容器为较常见的规格。根据不同的电源要求，器件有时会采用串联、并联或其组合的形式， 对于功率级超过 10KW 的电源，通常采用体积较大的罐形螺旋端子电容器。 

![这里写图片描述](http://img.blog.csdn.net/20170816175733609?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**6.信号滤波**

常和运放，电感构成滤波器（高通、低通、带通、带阻）

![这里写图片描述](http://img.blog.csdn.net/20170816180136135?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

并联的电容器C在输入电压升高时，给电容器充电，可把部分能量存储在电容器中。而当输入电压降低时，电容两端电压以指数规律放电，就可以把存储的能量释放出来。经过滤波电路向负载放电，负载上得到的输出电压就比较平滑，起到了平波作用。若采用电感滤波，当输入电压增高时，与负载串联的电感L中的电流增加，因此电感L将存储部分磁场能量，当电流减小时，又将能量释放出来，使负载电流变得平滑，因此，电感L也有平波作用。 

利用储能元件电感器L的电流不能突变的特点，在整流电路的负载回路中串联一个电感，使输出电流波形较为平滑。因为电感对直流的阻抗小，交流的阻抗大，因此能够得到较好的滤波效果而直流损失小。电感滤波缺点是体积大,成本高。

运放设计的滤波器（有源）：

![这里写图片描述](http://img.blog.csdn.net/20170816180745135?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**7.时间常数** 

这就是常见的 R、C 串联构成的积分电路。当输入信号电压加在输入端时，电容(C)上的电压逐渐上升。而其充电电流则随着电压的上升而减小。

![这里写图片描述](http://img.blog.csdn.net/20170816181535834?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**4.极性电容**

**1.钽电容的正负极**

![这里写图片描述](http://img.blog.csdn.net/20170816182237245?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

有条纹一端为**正极**

**2.电解电容的正负极**

![这里写图片描述](http://img.blog.csdn.net/20170816182432884?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

黑色标识一端为**负极**。


**参考：**

1.[(笔记)电路设计（四）之电容的应用 ](http://www.cnblogs.com/tdyizhen1314/archive/2012/10/23/2736087.html)

2.[ 电容器在电路中的作用(很全)](http://blog.csdn.net/qq_29350001/article/details/51142105)

3.[电容在电路中的作用及电容滤波原理](http://blog.csdn.net/uncle_guo/article/details/42574981)

