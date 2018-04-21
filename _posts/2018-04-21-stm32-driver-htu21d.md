---
layout: post
title: STM32驱动温湿度传感器HTU21D
date: 2018-04-21
categories: STM32
tags: [IIC,HTU21D,STM32]
description: STM32 driver htu21d
---

## **1.简介**

法国Humirel公司新一代HTU21D温度和湿度传感器在尺寸与智能方面建立了新的标准：它嵌入了适于回流焊的双列扁平无引脚DFN 封装， 底面3x3mm ，高度1.1mm。**传感器输出经过标定的数字信号，标准 I2C 格式**。

![这里写图片描述](https://img-blog.csdn.net/20180421235247304?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

HTU21D温度和湿度传感器为OEM应用提供一个准确可靠的温湿度测量数据。通过一个微控
制器的接口和模块连接达到温度和湿度数字输出。HTU21D 的分辨率可以通过输入命令进行改变（8/12bit 乃至12/14bit 的RH/T），传感器可以检测到电池低电量状态，并且输出校验和，有助于提高通信的可靠性。

____


## **2.规格参数**

![这里写图片描述](https://img-blog.csdn.net/20180421215527857?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**25摄氏度，3.3V供电时的电气特性如下：**

![这里写图片描述](https://img-blog.csdn.net/20180421215722410?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

数据手册下载地址如下：

[温湿度敏感芯片传感器HTU21D](https://download.csdn.net/download/wwt18811707971/10365530)

**印刷板设计注意：**

如果SCL 和SDA 信号线相互平行并且非常接近，有可能导致信号串扰和通讯失败。解决方法是在两个信号线之间放置VDD 或GND，将信号线隔开，或使用屏蔽电缆。此外，降低SCL 频率也可能提高信号传输的完整性。须在电源引脚（VDD， GND）之间加一个100nF 的去藕电容，用于滤波。此电容应尽量靠近传感器。

![这里写图片描述](https://img-blog.csdn.net/2018042122053083?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**引脚定义：**

![这里写图片描述](https://img-blog.csdn.net/20180421220659734?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

本实验采用的为成品HTU21D模块，直接与 STM32 开发板连接即可。

|引脚|说明|
|--|:---|
|电源引脚 (VDD, GND)|HTU21 的供电范围为 1.8VDC - 3.6VDC，推荐电压为 3.0V。电源（VDD）和接地（VSS）之间须连接一个0.1uF的去耦电容，且电容的位置应尽可能靠近传感器。|
|串行时钟输入(SCK)|SCK 用于微处理器与 HTU21 之间的通讯同步。由于接口包含了完全静态逻辑，因而不存在最小 SCK 频率。|
|串行数据 (DATA)|DATA 引脚为三态结构，用于读取传感器数据。当向传感器发送命令时, DATA 在 SCK 上升沿有效且在 SCK 高电平时必须保持稳定。 DATA 在 SCK 下降沿之后改变。当从传感器读取数据时, DATA 在 SCK 变低以后有效，且维持到下一个 SCK 的下降沿。为避免信号冲突，微处理器应驱动 DATA 在低电平。需要一个外部的上拉电阻（例如： 10kΩ）将信号提拉至高电平。上拉电阻通常已包含在微处理器的 I/O 电路中。|

___

## **3.通讯过程**

Htu21d遵循标准的 IIC 进行通信，关于 IIC 的介绍请看--->[IIC专题（一）——基础知识准备](http://wentao1213.com/2017/08/15/iic-driver-sum/)。本文设计也多参考此篇文章-->[STM32F10x_模拟I2C读写EEPROM](https://blog.csdn.net/ybhuangfugui/article/details/52151835)。

#### **3.1 IIC启动信号**

启动传输，发送一位数据时，包括 DATA 线在 SCK 线高电平期间一个向低电平的跳变。
![这里写图片描述](https://img-blog.csdn.net/20180421222352349?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![这里写图片描述](https://img-blog.csdn.net/20180421222527286?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#### **3.2 IIC停止信号**

终止传输，停止发送数据时，包括 DATA 线在 SCK 线高电平期间一个向高电平的跳变。

![这里写图片描述](https://img-blog.csdn.net/20180421222627320?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![这里写图片描述](https://img-blog.csdn.net/20180421222712808?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#### **3.2 IIC数据传输**

SCL时钟电平为低， 可以改换SDA数据线的电平，在SCL上升沿的过程将SDA数据发送出去。SCL为高电平时，SDA上的数据保持稳定。

![这里写图片描述](https://img-blog.csdn.net/20180421223128270?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

I2C 是以字节（8位）的方式进行传输，总线上每传输完1字节之后会有一个应答信号，应答信号总是由接收方来产生。通信过程的时钟由主器件(主机)提供。

**IIC 写一字节:**

![这里写图片描述](https://img-blog.csdn.net/20180421223534906?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![这里写图片描述](https://img-blog.csdn.net/20180421223759350?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

数据发送结束，进行应答操作。
0：表示从机应答，可以继续下一步操作；
1：表示从机非应答，不能进行下一步操作。

**IIC读一字节：**

IIC读取操作类似于发送，只是传输数据方向相反。

![这里写图片描述](https://img-blog.csdn.net/20180421224254915?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![这里写图片描述](https://img-blog.csdn.net/20180421224753698?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#### **3.3 IIC应答信号处理**

**等待应答：**

![这里写图片描述](https://img-blog.csdn.net/20180421225028720?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**产生应答：**

![这里写图片描述](https://img-blog.csdn.net/20180421225126143?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**产生非应答：**

![这里写图片描述](https://img-blog.csdn.net/20180421225205776?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

___

## **4.HTU21D数据测量**

测量命令如下表：

![这里写图片描述](https://img-blog.csdn.net/20180421225428235?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

本文采用非保持主机：
//非主机模式
```c
#define HTU_TEMP    0xf3
#define HTU_HUMI    0Xf5
```

在非主机模式下， MCU 需要对传感器状态进行查询。此过程通过发送一个启动传输时序，之后紧接着是如图所示的I2C 首字节（1000’0001）来完成。如果内部处理工作完成，单片机查询到传感器发出的确认信号后，相关数据就可以通过MCU 进行读取。如果测量处理工作没有完成，传感器无确认位（ACK）输出，此时必须重新发送启动传输时序。

![这里写图片描述](https://img-blog.csdn.net/20180421225813951?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**传感器初始化：**

![这里写图片描述](https://img-blog.csdn.net/20180421230047879?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**传感器数据读取与转换：**

传感器内部设置的默认分辨率为相对**湿度12位和温度14 位**。 SDA 的输出数据被转换成两个字节的数据包，高字节MSB 在前（左对齐）。每个字节后面都跟随一个应答位。两个状态位，即LSB 的后两位在进行物理计算前须置‘0’。

转换计算如下图：

![这里写图片描述](https://img-blog.csdn.net/20180421230241346?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

源代码实现：

![这里写图片描述](https://img-blog.csdn.net/20180421230342349?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

显示方式：

本实验通过串口打印和 TFTLCD 显示，串口打印有助于程序的调试。

![这里写图片描述](https://img-blog.csdn.net/20180421230934496?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

对于测量出来float的数值如何直接显示到LCD上，代码中简要的介绍了两种方式：
LCD显示方式1：sprintf函数将结果打印到test数组里，转换成字符串显示(LCD_ShowString)。
LCD显示方式2:将得到的数值拆分成整数和小数直接显示在液晶(LCD_ShowxNum)。
液晶显示直接参考正点原子的驱动。

____

源代码下载请转到---->[I2C_Software_Htu21d ](https://download.csdn.net/download/wwt18811707971/10365660)

 不足之处请大家一起交流。

____

## 参考：

1.[STM32F10x_模拟I2C读写EEPROM](https://blog.csdn.net/ybhuangfugui/article/details/52151835)
2.正点原子库函数


