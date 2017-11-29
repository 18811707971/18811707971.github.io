---
layout: post
title:  简易智能手环制作教程
date: 2017-11-25
categories: 干货铺
tags: [BLE,WIRELESS,Smart_Hardware]
description: Smart_Hardware 
---


转载地址：[简易智能手环制作教程](http://www.cnblogs.com/zjutlitao/p/4690228.html)

 ___

## **1.智能手环简介**

　　智能手环是一种穿戴式智能设备。通过该设备，用户可以记录日常生活中的锻炼、睡眠等实时数据，并将这些数据与手机、平板同步，起到通过数据指导健康生活的作用。另外，智能手环还具有社交功能，能够将锻炼情况和睡眠质量发送到社交网络进行分享。

![这里写图片描述](http://img.blog.csdn.net/20171125162556346?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

一个智能手环最小系统一般包括：**可充电的电源模块**、**控制模块**（下图中左边芯片）、**蓝牙模块**（右边芯片）、**存储模块**和**加速计模块**（上面芯片）。其中加速计是为了获得佩戴者在运动或睡眠过程中的加速度数据，通过分析这些数据则能够判断佩戴者的运动情况和睡眠质量；存储模块主要负责将实时数据暂存，接着在适当的时刻借助蓝牙模块将数据同步到手机端。方便起见本次要自制的记步手环将不采用存储器暂存，而是将数据实时地传送到手机端。同时为了便于大家对记步算法的理解，客户端将采用一个折线图的形式实时展示记步手环收集的数据。

![这里写图片描述](http://img.blog.csdn.net/20171125162333957?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

___

## **2. 如何实现记步**

　　看了上面的分析大家可能会疑惑——仅仅用一个加速计怎么能实现记步和睡眠质量检测呢？其实确实可以！因为加速计可以实时获取自身的X\Y\Z三个轴向的加速度。**当其静止时合加速度会在重力加速度附近波动**；当佩戴者处于深度睡眠过程中时，其合加速度将呈现出长时间的稳定于重力加速度附近；**当其随着运动的佩戴者手臂而做周期性摆动时，其数据也是有一定规律可循的**。这样，设计时只要通过分析从加速计获的数据就能实现对运动或睡眠质量的记录。

____

## **3.预期效果构思**

　　上面已经提到：为了方便，我们并未采用存储器实现记步手环的离线记录，而是**实时地将数据发送到客户端由一个可视化的折线图动态绘制结果**。如下图所示系统中记步手环部分包含**单片机模块、蓝牙模块、加速计模块和电源模块**，这样通过单片机的协调可以实现将加速计模块的数据通过蓝牙实时地传送给客户端程序。在客户端部分则负责将收集到的实时数据以折线图的形式动态地展示出来，此外客户端中也加入一个滑动条来控制记步阈值来真正让大家明白其设计思想（真正商业化的**智能手环多数采用的是先将有效数据保存在手环的小型存储器中，上位机周期性地将数据收集并同步到服务器端**）。

![这里写图片描述](http://img.blog.csdn.net/20171125162955127?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

____

## **4.硬件整体设计**

　　如下图,相比于上一个无线小风扇该硬件构成反而比较简单：蓝牙模块依然采用我们比较熟悉的**HC-06模块**，对于加速度的测量采用四周飞行器上常采用的**MPU6050**模块。该模块不仅含有加速计的功能，还具有陀螺仪的功能，其在**汽车防侧翻、相机云台稳定、机器人平衡、空中鼠标、姿态识别**等众多领域都有应用，这里我们只是利用了它的加速计功能。此外要注意：下图所示的单片机模块的电源引脚被隐藏了，在真正设计连接时一定不要忽略这两个引脚！

![这里写图片描述](http://img.blog.csdn.net/20171125164338000?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

____

## **5.MPU6050介绍**

　　MPU-60X0是全球首例9轴运动处理器。它集成了3轴MEMS陀螺仪，3轴MEMS加速计，以及1个可扩展的**数字运动处理器DMP(Digital Motion Processor)**。如下图所示轴向是相对于加速计说的，**当芯片水平静止放置时x轴和y轴的加速度分量几乎为0，z轴的加速度分量约为当地的重力加速度；而旋转极性则是对陀螺仪来说的**。

![这里写图片描述](http://img.blog.csdn.net/20171125164754097?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

为何上面说9轴信号呢？因为 MPU-60X0 可用 I2C 接口连接一个第三方的数字传感器，比如磁力计。扩展之后就可以通过其I2C或SPI接口输出一个9轴的信号。也可以通过其I2C接口连接非惯性的数字传感器，比如压力传感器。（为什么特别提磁力计和压力传感器呢？因为在飞控方面，利用陀螺仪和加速计可以计算飞行器的倾角，从而调节飞行器平衡。但是只是调节平衡对方向没有概念也不能执行复杂任务，因此需要配备磁力计（也即电子罗盘传感器）。此外，由于飞行器在不同高度作业时，其周围的重力加速度也不同，这样会影响倾角的准确性，因此通过气压计计算所处高度然后计算实时加速度达到精确控制的效果。）

![这里写图片描述](http://img.blog.csdn.net/20171125165201191?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

　MPU-60X0对陀螺仪和加速计分别用了**三个16位的ADC**，将其测量的模拟量转化为可输出的数字量。为了精确跟踪快速和慢速运动，传感器的测量范围是可控的，**陀螺仪可测范围为±250，±500，±1000，±2000°/秒(dps)，加速计可测范围为±2，±4，±8，±16g(重力加速度)**。如图是直接从16位ADC中读出的6轴的数据（从左到右依次为加速计X轴数据、Y轴数据、Z轴数据、陀螺仪X极数据、Y极数据、Z极数据）：

![这里写图片描述](http://img.blog.csdn.net/20171125165338222?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

但是这里的输出值并不是真正的加速度和角速度的值，上面说过，MPU是一个16位AD量程可程控的设备，这里设置的加速度传感器的测量量程为正负2g(这里的g为重力加速度），陀螺仪的量程为正负2000°/s。所以要用下面的公式进行转化：

![这里写图片描述](http://img.blog.csdn.net/20171125165525471?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

___

## **6.一个简单的记步算法设计**

当MPU6050随着运动的佩戴者手臂而做周期性摆动时，其数据也是有一定规律可循的。简单起见我们只分析合加速度：**一个摆臂周期其合加速度会在重力加速度上下波动**，如图只要选取合适的阈值（黑线代表阈值），每次检测出合加速度大于该阈值则认为是一次摆臂，从而可以实现记步的功能。这里要特别说明下：如果想把你的手环推向市场，就要通过大量分析摆臂数据建立一套更好的记步算法。

![这里写图片描述](http://img.blog.csdn.net/20171125165818892?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

___

## **7. I2C总线介绍**

由于51系列单片机将串口通信很多细节都封装到芯片内部，所以我们即使设计了串口驱动模块，也并没有真正了解串口通信的核心思想。其实**串口协议的出现是为了构成一个总线线路，这样单片机只要使用比较少的引脚就能和比较多的设备进行通信**了，这里要用到的I2C总线也具有相同的效果但又有些不同。

![这里写图片描述](http://img.blog.csdn.net/20171125170035935?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

I2C（Inter－Integrated Circuit）总线是由PHILIPS公司开发的两线式串行总线，用于连接微控制器及其外围设备。是微电子通信控制领域广泛采用的一种总线标准。它是**同步通信**的一种特殊形式，具有接口线少，控制方式简单，器件封装形式小，通信速率较高等优点。如上图采用I2C总线后CPU只要使用2个引脚便可和多个设备进行通信（其实每个采用I2C通信方式的设备都具有唯一的地址码，这样在总线中便能够被唯一识别），从而大大减少了引脚的使用。

 在I2C总线中使用的两线为时钟线SCL和数据线SDA。所有的I2C主从设备都是只被这两根线连接起来的。每一个设备既可以作为发送方，也可以作为接收方，或者既可以作为发送发也可以作为接收方。在总线中的主设备一般起产生时钟信号和初始化通信的作用，从设备则负责响应主设备发出的命令。为了在总线上区分每一个设备，每一个从设备必须有一个唯一的地址。主设备一般不需要地址（一般为微处理器），因为从设备不能发送命令给主设备。

![这里写图片描述](http://img.blog.csdn.net/20171125170541394?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

这里要先介绍I2C总线中几个专有名词：

l  发送者：将数据发送到总线的设备 
l  接收者：从总线接收数据的设备 
l  主设备：产生时钟信号、启动通信、发送I2C命令和终止通信的设备 
l  从设备：监听总线、能被主设备寻址的设备 
l  多主设备：I2C能够拥有多个主设备，而且每个主设备都能够发送命令 
l  仲裁：当多个主设备请求使用总线时，决定哪一个主设备可以占用的一个过程 
l  同步：同步多个设备时钟信号的一个过程

上面是从宏观上对I2C总线介绍了下，接下来将深入细节研究其通信过程：

#### **7.1 串行数据传送：**

在总线备用时SDA和SCL都必须保持高电平状态，只有关闭I2C总线时才能使SCL钳位在低电平。**在I2C总线数据传输时，在时钟线高电平期间，数据线上必须保持有稳定的逻辑电平（也就是说在数据传输期间只有时钟线低电平期间，才允许数据线上的电平发生变化）。**

![这里写图片描述](http://img.blog.csdn.net/20171125171614902?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

因此在如上图中**对于每一个时钟脉冲期间一比特的数据将会被传送，SDA只能在时钟信号为低电平时才能改变**。下面是代码中发送一字节的函数：在循环体内每次将dat内的最高位移出到CY中，进而赋值给SDA(这时SCL为低，SDA可改变)。接着拉高SCL并保持5us，最后再拉低SCL实现一个时钟脉冲将dat中最高位送出。依此循环8次实现将dat全部传出。

```c
//------------------------------------------------
//向I2C总线发送一个字节数据
//------------------------------------------------
void I2C_SendByte(uchar dat)
{
    uchar i;
    for (i=0; i<8; i++)         //8位计数器
    {
        dat <<= 1;              //移出数据的最高位
        SDA = CY;               //送数据口
        SCL = 1;                //拉高时钟线
        Delay5us();             //延时
        SCL = 0;                //拉低时钟线
        Delay5us();             //延时
    }
    I2C_RecvACK();
}
```

#### **7.2 开始和结束条件**

命令不会没有任何预兆直接发送的，每一个I2C命令的发送总是开始于开始条件并结束于终止条件。这里所谓的开始条件和终止条件起始也是由SCL和SDA组合形成的（如下图）。

![这里写图片描述](http://img.blog.csdn.net/20171125172323133?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

如果时钟线保持高电平期间，数据线出现由高到低的电平变化，则会启动I2C总线，此时为I2C的起始信号：
```c
//------------------------------------------------
//I2C起始信号
//------------------------------------------------
void I2C_Start()
{
    SDA = 1;                    //拉高数据线
    SCL = 1;                    //拉高时钟线
    Delay5us();                 //延时
    SDA = 0;                    //产生下降沿
    Delay5us();                 //延时
    SCL = 0;                    //拉低时钟线
}
```
若在时钟线保持高电平期间，数据线出现由低到高的电平变化，则会停止I2C总线的数据传输，此时为I2C的终止信号：
```c
//------------------------------------------------
//I2C停止信号
//------------------------------------------------
void I2C_Stop()
{
    SDA = 0;                    //拉低数据线
    SCL = 1;                    //拉高时钟线
    Delay5us();                 //延时
    SDA = 1;                    //产生上升沿
    Delay5us();                 //延时
}
```
开始条件之后I2C总线被认为是忙状态，只有当停止信号之后其他主设备才能使用该总线。此外，当开始条件之后主设备能够多次发出开始信号。这些开始信号和第一次发出的开始信号类似，他们后面经常会跟从设备的地址。这样可以方便实现在I2C总线忙期间，当前占线的主设备可以和不同的从设备进行通信。

####  **7.3 I2C数据传送**

　　I2C总线上传送的每一个字节均为8位，但是每启动一次I2C总线，其后的数据传送字节数是没有限制的。同时**每传送一字节的数据后面都要跟随一个接收者回应的应答位**（低电平为应答信号，高电平为非应答信号），当全部数据发送完毕后主设备发送终止信号。

![这里写图片描述](http://img.blog.csdn.net/20171125210451842?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

所以在上面**向I2C总线发送一字节的数据的代码的最后有一个I2C_RecvACK()函数**。（如下）该函数负责接收接收者发送过来的应答信号，也即上图中的第9个时钟脉冲的期间的相应操作。
```c
//------------------------------------------------
//I2C接收应答信号
//------------------------------------------------
bit I2C_RecvACK()
{
    SCL = 1;                    //拉高时钟线
    Delay5us();                 //延时
    CY = SDA;                   //读应答信号
    SCL = 0;                    //拉低时钟线
    Delay5us();                 //延时
    return CY;
}
```
 
 注：所有的数据位包括应答位都需要主设备产生时钟脉冲。如果从设备没有应答意味着将没有更多的数据要传送或者设备没有准备好传送。这时，主设备要么产生停止信号，要么重新发出开始条件。

![这里写图片描述](http://img.blog.csdn.net/20171125210753137?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### **7.4 I2C的7-bit地址**

每一个从设备都应该具有唯一的地址，这样主设备才能准确的寻址到每一个设备，而这些地址被统一规定为7比特。但是上面讲过I2C总线传输数据都是8比特传送，地址7比特岂不是少一位！其实紧跟地址还有一位用来表示是读操作还是写操作的标志位。如果该位为0表示主设备将要向从设备写数据，否则表示主设备将要从从设备读数据。在这8比特被发送后主设备能够持续地进行读或者写。如果主设备想和其他从设备进行通信，只要再次发送一个新的开始信号就可以而不必发送终止信号。

![这里写图片描述](http://img.blog.csdn.net/20171125210942275?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

____

## **8. MPU6050驱动设计**

下面将结合MPU6050的驱动进一步讲解其原理（该部分的代码参见工程的mpu6050.c部分）。我们首先来看一下它的头文件mpu6050.h：从第6到25行上来就是一大串内部地址的定义，对于初学者可能一头雾水！如果楼主再引入寄存器等数字电路的知识可能又要说几页了，于是这里准备只用一个简单的例子阐述下这些地址的作用。

```c
#include"i2c.h"

//-----------------------------------------
// 定义MPU6050内部地址
//-----------------------------------------
#define    SMPLRT_DIV        0x19    //陀螺仪采样率，典型值：0x07(125Hz)
#define    CONFIG            0x1A    //低通滤波频率，典型值：0x06(5Hz)
#define    GYRO_CONFIG        0x1B    //陀螺仪自检及测量范围，典型值：0x18(不自检，2000deg/s)
#define    ACCEL_CONFIG    0x1C    //加速计自检、测量范围及高通滤波频率，典型值：0x01(不自检，2G，5Hz)
#define    ACCEL_XOUT_H    0x3B
#define    ACCEL_XOUT_L    0x3C
#define    ACCEL_YOUT_H    0x3D
#define    ACCEL_YOUT_L    0x3E
#define    ACCEL_ZOUT_H    0x3F
#define    ACCEL_ZOUT_L    0x40
#define    TEMP_OUT_H        0x41
#define    TEMP_OUT_L        0x42
#define    GYRO_XOUT_H        0x43
#define    GYRO_XOUT_L        0x44    
#define    GYRO_YOUT_H        0x45
#define    GYRO_YOUT_L        0x46
#define    GYRO_ZOUT_H        0x47
#define    GYRO_ZOUT_L        0x48
#define    PWR_MGMT_1        0x6B    //电源管理，典型值：0x00(正常启用)
#define    WHO_AM_I        0x75    //IIC地址寄存器(默认数值0x68，只读)
#define    SlaveAddress    0xD0    //IIC写入时的地址字节数据，+1为读取

//-----------------------------------------
// 通过I2C和MPU6050通信的函数
//-----------------------------------------
void  Single_WriteI2C(uchar REG_Address,uchar REG_data);//向I2C设备写入一个字节数据
uchar Single_ReadI2C(uchar REG_Address);                //从I2C设备读取一个字节数据
void  InitMPU6050();                                    //初始化MPU6050
int   GetData(uchar REG_Address);                         //合成数据
```

上面讲到在I2C总线中主设备可以通过固定的7-bit地址寻找到相应的从设备（这里的7-bit地址为第26行的SlaveAddress，想必大家也能够理解后面注释的意义了吧~不加1表示紧跟着地址的一位为0，表示向该设备写数据；加1则表示紧跟着的一位为1，表示主设备从从设备读数据）。虽然采用这种方式能够准确找到从设备，但是从设备里面又有比较多的寄存器。这就好比你知道了某个要找的东西在具体的某个大柜子里，但是来到大柜子前又发现有许多小抽屉。这里的7-bit地址就好像指明了哪个柜子，而从第6到25行的内部地址就像柜子上的抽屉编号，而不一样之处是位于mpu6050内的“小抽屉”一部分存放着其采集的实时数据，另一部分等着外部放一些数据来设置其采样属性。

　　这样，如上面的第6行的SMPLRT_DIV（0x19）是用来设置陀螺仪采样率的寄存器地址，只要向该地址所指的寄存器写入相应的值则可以设置陀螺仪采样率。因此下面MPU6050初始化函数就是调用封装的I2C写函数向相应的小抽屉内写属性数据，设置MPU6050采样属性。
```c
//------------------------------------------------
//初始化MPU6050
//------------------------------------------------
void InitMPU6050()
{
    Single_WriteI2C(PWR_MGMT_1, 0x00);    //解除休眠状态
    Single_WriteI2C(SMPLRT_DIV, 0x07);
    Single_WriteI2C(CONFIG, 0x06);
    Single_WriteI2C(GYRO_CONFIG, 0x18);
    Single_WriteI2C(ACCEL_CONFIG, 0x01);
}
```
再如第10~11行的ACCEL_XOUT_H、ACCEL_XOUT_L是用来存放最新的陀螺仪X极的数值，因为采用16位ADC所以这里需要用两个寄存器。所以下面合成数据函数负责连续读取REG_Address开始的两字节数据组成一个16位数据。当函数的参数为ACCEL_XOUT_H时，则获取的是实时的陀螺仪X极的数值，同样地可以获得实时的6轴数据。
```c
//------------------------------------------------
//合成数据
//------------------------------------------------
int GetData(uchar REG_Address)
{
    uchar H,L;
    H=Single_ReadI2C(REG_Address);
    L=Single_ReadI2C(REG_Address+1);
    return (H<<8)+L;                       //合成数据
}
```
**注：**关于MPU6050内部的“小抽屉”的地址和功能需要阅读其官方的MPU6050寄存器手册。
____

## **9.硬件工程整体介绍**

9.1、打开Keil uVision2，点击Project下的Open Project，打开记步手环.Uv2加载工程。

![这里写图片描述](http://img.blog.csdn.net/20171125213913165?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

9.2、待工程加载完毕，大家会在工程窗口中看到图9_2所示文件结构。其中FUNC组下面包含数i2c驱动、mpu6050和串口驱动文件， USER组下是最上层应用程序文件。

![这里写图片描述](http://img.blog.csdn.net/20171125213844791?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

9.3、上一章已经把uart.c讲解了，前几节也把i2c.c和mpu6050,c介绍了。这里直接从main.c对整个工程的流程进行分析：主函数中先初始化串口和MPU6050，接着进入无限循环。循环中每隔一定的时间发送一帧的数据——该帧以‘#’开始以‘$’结束，中间依次是X轴加速度值、Y轴加速度值和Z轴加速度值。

```c
//------------------------------------------------
//主函数
//------------------------------------------------
void main (void)
{
    delay(500);            //上电延时    
    InitUART();            //初始化串口
    InitMPU6050();        //初始化MPU6050

    while (1)             //主循环
    {
        SendByte('#');                //起始标志
        SendData(GetData(0x3B));    //X轴加速度
        SendData(GetData(0x3D));    //Y轴加速度
        SendData(GetData(0x3F));    //Z轴加速度
        SendByte('$');                 //标志
        delay(20);
    }
}

```

其中调用了串口驱动中的void InitUART(void)串口初始化函数、 void SendByte(unsigned char dat)串口发送一字节函数和 void SendStr(unsigned char *s)串口发送一个字符串函数，以及调用了mpu6050驱动中的void InitMPU6050()初始化函数和int GetData(uchar REG_Address)获取6轴数据函数。
```c
 //外部函数
 extern void InitUART(void);
 extern void SendByte(unsigned char dat);
 extern void SendStr(unsigned char *s);
 extern void InitMPU6050();
 extern int  GetData(uchar REG_Address);
```
这里唯一要特别说明的函数是：void SendData(int value)函数。我们知道直接**调用MPU6050的函数int GetData(uchar REG_Address)返回的是int类型的数据，而串口每次只能发送一个8bit的数据，于是这里的SendData则是负责将该int类型的数值转换为串口容易发送的数据再进行发送。**
```c
//-----------------------------------------
//整数转字符串
//-----------------------------------------
void enCode(uchar *s,int temp_data)
{
    if(temp_data<0)
    {
        temp_data=-temp_data;
        *s='-';
    }
    else *s=' ';
    *++s =temp_data/10000+0x30;
    temp_data=temp_data%10000;      //取余运算
    *++s =temp_data/1000+0x30;
    temp_data=temp_data%1000;       //取余运算
    *++s =temp_data/100+0x30;
    temp_data=temp_data%100;        //取余运算
    *++s =temp_data/10+0x30;
    temp_data=temp_data%10;         //取余运算
    *++s =temp_data+0x30;
    *++s ='\0';                        //字符串结束标志     
}

//-----------------------------------------
//编码+发送到串口
//-----------------------------------------
void SendData(int value)
{ 
    enCode(temp, value);            //转换数据显示
    SendStr(temp);
}
```
上面的enCode函数是将输入的int类型的数据转换为第一位为符号（正用空格代替，负用负号代替），后5位为数值的字符串，即使不足五位数前面也要填充0。这样便不难理解SendData的功能：将value编码并通过串口发送。

这样整个工程的作用则是周期性读取MPU6050三轴的加速度并用下面的帧格式通过蓝牙发送出去：

![这里写图片描述](http://img.blog.csdn.net/20171125214608084?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


___

## **10.客户端软件构成模块**

10.1、打开Eclipse点击File菜单栏下的Import按钮准备导入second_test工程（如图10_1所示）。

![这里写图片描述](http://img.blog.csdn.net/20171125214739742?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

10.2、接着在弹出的Select窗口中选择Android文件夹下的Existing Android Code Into Workspace点击next（如图10_2所示）。

![这里写图片描述](http://img.blog.csdn.net/20171125214837675?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

10.3、接着在弹出的框中点击右上角的Browse按钮，找到要导入的third_test所在路径，并且需要勾选Copy projects into workspace（如图10_3所示）。

![这里写图片描述](http://img.blog.csdn.net/20171125214920532?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

10.4、最终效果如图10_4所示在src文件夹下有四个包：其中第一个是和蓝牙相关的类（从下到上依次为蓝牙设备搜索相关类、蓝牙通信连接相关类和蓝牙通信相关类）；第二个是绘制折线图表相关的类（这里采用开源图表绘制引擎achartengine，所以在libs里要添加相应的包）；第三个是数据池相关的类，用于实现蓝牙数据实时高速处理；另一个包是UI相关类，也是整个工程最核心的部分。如果读者导入过程中出现错误，也可以采用第三章的方法新建一个工程，然后把src下的文件、layout下的文件和AndroidManifest.xml文件做相应的新建或修改，同时还要注意引入libs的包以及values里的strings.xml。

![这里写图片描述](http://img.blog.csdn.net/20171125215044486?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

____

## **11.软件最终效果预览**

　　上面是从模块构成的角度介绍工程的主要文件，为了更好的方便分析其内部逻辑，笔者准备先带领大家预览下本次应用的最终效果（如图11_1所示）：

n  第一幅图：是初始打开界面，如果本地蓝牙没有打开最左边的按钮将会显示“打开蓝牙设备”； 
n  第二幅图：是点击“连接我的小手环”后进入蓝牙搜索阶段； 
n  第三幅图：是自动搜索到记步手环后进入的连接蓝牙阶段； 
n  第四幅图：是连接完成后，应用把从手环收集的实时数据（X\Y\Z轴加速度以及合加速度）绘制出； 
n  第五幅图：是通过滑动条调大记步阈值，并选择CheckBox只显示合加速度值的实时折线； 
n  第六幅图：是放大折线图，并点击某个点显示具体信息图。

　　其中前三个阶段和上一章中的小风扇的控制很类似，都是点击连接到进入搜索再到进行连接。只不过一个是连接后通过应用向硬件发送命令帧来控制小风扇转速；一个是不断从记步手环读取实时的X\Y\Z三轴的加速度，计算合加速度同时记步，并且将数据实时以折线图的形式展示出来。

![这里写图片描述](http://img.blog.csdn.net/20171125215302514?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

____

## **12.一个高效处理数据的数据池设计**

　　当提到为什么需要高效处理的数据池时，其实要从蓝牙搜索讲起。由于上一章的最后对蓝牙搜索、连接、通信的三个过程做了详细的讲解，本次则只从整体上进行梳理一下。

　　如图12_1，当点击连接小手环按钮后则执行蓝牙搜索类的doDiscovery（）函数进行搜索蓝牙设备，在其搜索过程中搜索的设备名和设备地址分别存储在BlueToothSearch的公有成员变量mNameVector和mAddrVector中，然后在本次搜索结束后会向Activity发送一个类型为0x01的Handler消息，而该消息会被Activity中的handleMessage接收到。

　　当Activity中的handleMessage接收类型为0x01的消息后，程序会遍历本次蓝牙搜索到的周边设备的名称找到符合我们的手环的蓝牙设备。然后调用蓝牙连接的setDevice()函数获取远程蓝牙通信socket，接着在handleMessage内再触发蓝牙连接的线程进行蓝牙连接。当蓝牙连接完毕，则会发送0x02类型的消息反馈给Activity中的handleMessage。

　　同样的当Activity中的handleMessage接收类型为0x02的消息后，程序会调用蓝牙通信类的setSocket()函数来获取标准输入输出流。此后，如果想从软件向硬件发送消息则直接可以调用蓝牙通信类的write（）函数，而接收数据则是采用启动一个接收线程来实现实时接收的。

![这里写图片描述](http://img.blog.csdn.net/20171125215537090?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

现在我们的思维已经跟着转到了上图中最后一个无限轮询收数据阶段。同时我们知道从小手环发来的数据是比较高速的（硬件工程中写的是每次发送完毕delay(20)，应该算是比较短的时间了）。那么问题就来了：如果我们不能及时地将手环传来的数据进行处理，很有可能导致大量的数据滞留在缓冲区。这样进一步会导致每次获得的数据都不是最新的数据，而表现出动态绘制折线图滞后糟糕的效果。

综上由于下位机10ms发送一次20byte的数据，上位机一方面要做好接收工作，**保证数据不拥挤在串口接收缓冲区**；另一方面也要实时获取当前从串口读到的最新数据。如果采用传统多线程+锁的机制是可以的，但是当多线程中加入锁势必会影响程序执行效率，通过综合分析该问题笔者最终抽象出一个特殊的数据模型——自动更新的环形栈：

![这里写图片描述](http://img.blog.csdn.net/20171125215948947?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

如图12_2所谓自动更新的环形栈本质上是一个基于环形数组的特殊数据结构。图中环形代表数据池，也是一个环形数组（普通数组，采用一定技巧将首尾连接），p_write指示当前数据插入位置，每次插入一个数据p_write顺时针移动一格，从而实现新数据覆盖老数据的自动更新功能。而这里最精妙的地方在于每次取数据的方式：从p_write所指的位置逆时针取40个数据（因为有效帧包含的数据长度为20，一次取40保证至少有一个有效帧），然后从这40个数据中找出有效信息，赋值给公有成员X,Y,Z。这样通过适当调节环的容量，保证取数据时该段数据不被覆盖的前提下，又能根据p_write指示获取最新的下位机发来的有效帧，将存和取有效地分离从而完美达到了我们的需求。

　　具体在程序中UI_Main.java的onCreate函数中声明并实例化一个大小为20000的数据池mDataPool = new DataPool(20000)。接着在BlueToothCommunicate的轮询接收数据的线程中（也即图12_1的最后一环节的read中）对于每次新收到的数据调用mDataPool的push_back(buffer, bytes)函数将其存储在数据池中。当每次需要取最新数据时只要先调用mDataPool的ask()函数，接着便可直接通过访问DataPool的公有成员X\Y\Z获取最新三轴加速度的值了。

```java
// 利用线程一直收数据
public void run() {
    byte[] buffer = new byte[1024];
    int bytes;
    // 循环一直接收
    while (state) {
        try {
            // bytes是返回读取的字符数量，其中数据存在buffer中
            bytes = mmInStream.read(buffer);
            String readMessage = new String(buffer, 0, bytes);
            Log.i("beautifulzzzz", "read: " + bytes + "  mes: "
                    + readMessage);
            UI_Main.mDataPool.push_back(buffer, bytes);
        } catch (IOException e) {
            break;
        }
    }
}
```
___

## **13.一个开源的折线图绘制方案**

在第10节客户端软件构成模块中曾提到本项目中采用了**开源图表绘制引擎AChartEngine**。它是一个安卓系统上制作图表的框架，支持折线图、面积图、分区图、对比图、散点图、柱状图、饼图等（如下图所示）。

![这里写图片描述](http://img.blog.csdn.net/20171125220439114?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

此外其所有支持的图表类型，都可以包含多个系列，都支持水平（默认）或垂直方式展示图表。并且支持许多其他的自定义功能。所有图表都可以建立为一个view，也可以建立为一个用于启动activity的intent（显然上面前两幅图是采用view的形式，其他几个是采用intent启动的）。

一般突然提到某某开源包或者调用别的接口初学者可能会头大，而且这里更让多数人头痛的是笔者竟突然亮出了这么多炫酷的UI，岂不是更加难以使用！于是可能会有很多人准备自己DIY折线图了。然而事实却是这个开源的框架用起来十分方便：大家可以把所有的chart都想象成由两层组成，一部分是Renderer（如XYMultipleSeriesRenderer，用于对图表样框架样式的说明），另一部分是Dataset(如XYMultipleSeriesDataset，用于对视图数值的处理)。所以在ChartLine.java类的开始就定义并声明这两种类型的私有成员：【第一步：数据层和显示层定义并实例化】
```java
private XYMultipleSeriesDataset mDataset = new XYMultipleSeriesDataset();
private XYMultipleSeriesRenderer mRenderer = new XYMultipleSeriesRenderer();
```
因为mRenderer用于对图表框架样式的说明，所以在setChartSettings函数里调用了多个其成员函数用来对图表整体样式属性进行设置。例如7、8两行是设置X轴和Y轴的标题，9到12行设置初始X轴和Y轴所表示的范围，22到24行用来设置放大缩小的控件和属性（就像地图控件里的放大缩小按钮）。这样下层的X轴、Y轴等就都设置好了。【第二步：设置显示层显示样式】
```java
public void setChartSettings(String xTitle, String yTitle, double xMin, double xMax, double yMin, double yMax, int axesColor,int labelsColor) {
    // 有关对图表的渲染可参看api文档
    mRenderer.setXTitle(xTitle);// 名字
    mRenderer.setYTitle(yTitle);
    mRenderer.setXAxisMin(xMin);// 最小最大值
    mRenderer.setXAxisMax(xMax);
    mRenderer.setYAxisMin(yMin);
    mRenderer.setYAxisMax(yMax);
    mRenderer.setAxesColor(axesColor);// 坐标轴颜色
    mRenderer.setLabelsColor(labelsColor);// 标号颜色
    mRenderer.setShowGrid(true);// 显示网格
    mRenderer.setGridColor(Color.GRAY);
    mRenderer.setXLabels(16);
    mRenderer.setYLabels(20);
    mRenderer.setYLabelsAlign(Align.RIGHT);// 设置标签居Y轴的方向
    mRenderer.setPointSize((float) 2);
    mRenderer.setShowLegend(true);// 下面的标注
    // mRenderer.setZoomButtonsVisible(true);// 放大缩小按钮
    mRenderer.setZoomEnabled(true, false);// 设置缩放,这边是横向可以缩放,竖向不能缩放
    mRenderer.setPanEnabled(true, false);// 设置滑动,这边是横向可以滑动,竖向不可滑动
}
```
当表格框架设置好之后，接下来就是向框架内填充折线，并且在此过程中把每一条折线的数据层放入总的数据层中。如下setLineSettings函数循环4次，每次首先实例化一个标题为titles[i]的坐标序列，然后将该序列放入总的数据层mDataset中。同样的每次实例化一个XYSeriesRenderer（因为每个折线也有自己的样式），并将其加入总的图标层mRenderer中。这样就能够将4条分别表示X轴加速度、Y轴加速度、Z轴加速度和合加速度的折线图设置好。【第三步：设置4个折线数据序列并加入数据层，设置4个折线层并加入显示层】
```java
public void setLineSettings() {
    for (int i = 0; i < titles.length; i++) {
        // create a new series of data
        mCurrentSeries[i] = new XYSeries(titles[i]);
        mDataset.addSeries(mCurrentSeries[i]);
        // create a new renderer for the new series
        renderer[i] = new XYSeriesRenderer();
        mRenderer.addSeriesRenderer(renderer[i]);
        // set some renderer properties
        renderer[i].setPointStyle(styles[i]);
        renderer[i].setColor(colors[i]);
        renderer[i].setFillPoints(true);// 实心还是空心
        renderer[i].setDisplayChartValues(false);// 不显示值
        renderer[i].setDisplayChartValuesDistance(10);
    }
}
```
此时mDataset里存放着当前要显示的折线的所有XYSeries，每个折线XY序列存放在mCurrentSeries[i]中，如果想在该折线图上增加一个数据只要调用mCurrentSeries[i].add(x, y)即可；如果想显示或隐藏某个折线图只要调用图表层的mRenderer和数据层mDataset移出对应的折线和折线序列即可。【提前一步（5）：如何往对应的折线中增加数据，以及如何显示隐藏某条折线】
```java
// 显示第i个折线图
public void showLine(int i) {
    mDataset.addSeries(mCurrentSeries[i]);
    mRenderer.addSeriesRenderer(renderer[i]);
}

// 隐藏第i个折线图
public void hideLine(int i) {
    mDataset.removeSeries(mCurrentSeries[i]);
    mRenderer.removeSeriesRenderer(renderer[i]);
}

// 向第i个折线图中添加(x,y)数据
public void addData(int i, double x, double y) {
    mCurrentSeries[i].add(x, y);
}
```
上面说过所有图表都可以建立为一个view，也可以建立为一个用于启动activity的intent。这里由于我们需要在ui_main.xml中添加其他控件，所以采用view的方式新建图表。如下setChartViewSetting函数负责当图表没有建立时分别实例化layout和mChartView，并将新建的mChartView加入ui_main.xml中图表所在的layout中，这样我们就可以看到基本的图表了。此外，第10行是给图表加的点击监听，用于显示点击点的详细信息（图11_1软件最终效果的第6张图）。【第四步：将数据层和显示层合成为图表加入UI中】
```java
public void setChartViewSetting(final Activity activity) {
    if (mChartView == null) {
        LinearLayout layout = (LinearLayout) activity
                .findViewById(R.id.chart);
        mChartView = ChartFactory.getLineChartView(activity, mDataset,
                mRenderer);
        // enable the chart click events
        mRenderer.setClickEnabled(true);
        mRenderer.setSelectableBuffer(10);
        mChartView.setOnClickListener(new View.OnClickListener() {
            public void onClick(View v) {
                ……(略)
            }
        });
        layout.addView(mChartView, new LayoutParams(
                LayoutParams.WRAP_CONTENT, LayoutParams.WRAP_CONTENT));
    } else {
        mChartView.repaint();
    }
}
```
___

## **14 整体逻辑梳理** 

其实仔细观察的读者会发现：本次的UI_Main.java和上次的大致相同。前一阶段都是点击按钮来连接远程蓝牙设备。而不同之处在于上一章是通过加减按钮向小风扇发送速度控制命令来控制速度，这一章是不断读取手环的实时数据并用折线图绘制出来。整体业务逻辑还是在控件的点击事件和handleMessage之间有序进行，下面将着重说明数据的实时显示及一些用于优化操作的细节。

在onCreate中首先实例化蓝牙三剑客，接着实例化数据池和折线图表，然后调用折线图类的成员函数对折线图做前期设置，最后启动ChartThread线程。

```java
// 实例化蓝牙三剑客（搜索、连接、通信）
// myHandler是用来反馈信息的
mBlueToothSearch = new BlueToothSearch(this, myHandler);
mBlueToothConnect = new BlueToothConnect(myHandler);
mBlueToothCommunicate = new BlueToothCommunicate(myHandler);

mDataPool = new DataPool(20000);

mChartLine = new ChartLine();
// 设置图标显示的基本属性
mChartLine.setChartSettings("Time", "", 0, 100, -20000, 20000, Color.WHITE, Color.WHITE);
// 设置四个折线图的属性
mChartLine.setLineSettings();

ChartThread.start();// 启动图标更新线程
```

在此之后便是对连接手环按钮做的相关设置，这里和上一章中的连接风扇几乎一样，关键在于理解蓝牙三剑客通过线程启动并通过handler将消息反馈的机制。

![这里写图片描述](http://img.blog.csdn.net/20171125221548180?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

　这样当点击连接手环的按钮之后，然后在handler的沟通下上位机和下位机最终实现可通信。此时下位机一旦有数据传送上来，上位机便快速的将其放入数据池内。那么程序是在成么时候取数据并更新UI的呢？秘密就在于ChartThread.start()！

```java
private Thread ChartThread = new Thread() {
    public void run() {
        while (true) {
            try {
                sleep(100);
                // 周期性发送更新Chart的消息（因为UI不能放在这个里面更新）
                Message msg = new Message();
                msg.what = 0x04;
                myHandler.sendMessage(msg);
            } catch (InterruptedException e) {
            }
        }
    }
};
```

从上面的可以看出ChartThread主要**负责周期性发送类别为0x04的消息**，而在handleMessage的case 0x04中则是负责获取实时数据并更新UI的。之所以这样绕个弯是因为UI更新一旦放在ChartThread中就会导致程序运行异常。这里的数据获取和更新也比较容易理解：**首先调用数据池的ask函数从p_write向后找40个数据寻找并解析有效帧，如果成功则最新的X\Y\Z三轴的加速度已经保存在mDataPool的公有成员X\Y\Z中**。下面第3行是计算合加速度（减去16000是为了方便显示），接着6到9行负责分别将三轴加速度及其合速度值加入折线图。第10到13行便是我们简单的记步算法了，即当合加速值超过设定的记步阈值时记步数加一。第15、16行是控制折线图滚动到最新的位置并刷新ChartView。
```java

case 0x04:
    if (mDataPool.ask() == true) {
        int all = (int) Math.sqrt(mDataPool.X * mDataPool.X
                + mDataPool.Y * mDataPool.Y + mDataPool.Z
                * mDataPool.Z) - 16000;
        mChartLine.addData(0, mTime, mDataPool.X);
        mChartLine.addData(1, mTime, mDataPool.Y);
        mChartLine.addData(2, mTime, mDataPool.Z);
        mChartLine.addData(3, mTime, all);
        if (all > mUpperLimit) {// 记步-和加速度超过设定上限则记步
            mNum++;
            mTextView2.setText("当前记步数为: " + mNum);
        }
        mTime += 1;
        mChartLine.letChartMove(mTime);// 控制图形滚动
        mChartLine.mChartView.repaint();
    }
    break;
```

综上，当建立蓝牙通信后，整个应用程序中主要有三个线程：

①用于不断读取串口数据并将其存入数据池的数据线程；
②用于周期性发送0x04消息的信号线程；
③隐蔽而重要的主线程（UI更新等操作）。

如图14_2所示：一方面数据线程不断读取数据存入数据池，另一方面信号线程周期性发送0x04消息触发handleMessage的case 0x04执行ask读数据函数，当成功解析到有效数据时会在主线程中记步并更新UI。

![这里写图片描述](http://img.blog.csdn.net/20171125222215390?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

此外，还有一些其他的控件用于提高交互性，如表14_1所示：开始/停止按钮用于控制折线图是否动态滚动，当停止折线图动态滚动时折线图的数据增加并未被中止，此时可以方便用户拖动折线图查看历史或观察细节。四个CheckBox用于控制显示哪一个折线图，这样便于单独分析。滚动条是用来动态设置记步阈值的，这样便于大家深入理解我们的简单的记步算法。

![这里写图片描述](http://img.blog.csdn.net/20171125222339392?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

____

**原文出处:** 

 作者：beautifulzzzz

 链接：http://www.cnblogs.com/zjutlitao/p/4690228.html#commentform

 工程：http://git.oschina.net/hyli
 
 注：这个博主有很多干货，很赞！
 
 

 
 
 
 
 
