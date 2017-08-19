---
layout: post
title: IIC专题（二）——STM32驱动AT24C02
date: 2017-08-19
categories: STM32
tags: [IIC,AT24C02,DRIVER]
description: STM32 LEARN
---

**1.概述**

MiniSTM32 开发板板载的 EEPROM 芯片型号为 24C02。该芯片的总容量是 256个字节，该芯片通过 IIC 总线与外部连接。这里直接采用原子板上的 AT24C02 ，主要是软件编程方面的学习。

**2.硬件连接**

![这里写图片描述](http://img.blog.csdn.net/20170819131622775?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

A2、A1、A0 三个引脚直接接地。供电： (VCC = 2.7V to 5.5V)

器件地址设置：

![这里写图片描述](http://img.blog.csdn.net/20170819132547458?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

对于AT24C02:Addr--->0xA0（写）/ 0xA1（读）。

单字节写入：

![这里写图片描述](http://img.blog.csdn.net/20170819133254612?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

按页写入：

![这里写图片描述](http://img.blog.csdn.net/20170819133400314?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

从当前地址读出数据;

![这里写图片描述](http://img.blog.csdn.net/20170819133537805?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

随机读取：

![这里写图片描述](http://img.blog.csdn.net/20170819133636709?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

顺序读出：

![这里写图片描述](http://img.blog.csdn.net/20170819133759926?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


**3.例程分析**

**（一）IIC 部分实现代码**

包括 IIC 的初始化（IO 口）、 IIC 开始、 IIC 结束、 ACK、 IIC 读写等功能，在其他函数里面，只需要调用相关的 IIC 函数就可以和外部 IIC 器件通信了，这里并不局限于 24C02，该段代码可以用在任何 IIC 设备上。

IIC_SCL 和 IIC_SDA 分别连在 STM32 的 PC12 和 PC11。

**1.IIC 初始化**

```c

//IO操作函数	 
#define IIC_SCL    PCout(12) //SCL
#define IIC_SDA    PCout(11) //SDA	 
#define READ_SDA   PCin(11)  //输入SDA

//初始化 IIC
void IIC_Init(void)
{
	GPIO_InitTypeDef GPIO_InitStructure;
	
	RCC_APB2PeriphClockCmd( RCC_APB2Periph_GPIOC, ENABLE );
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_12|GPIO_Pin_11;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP ; //推挽输出
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(GPIOC, &GPIO_InitStructure);
	
	IIC_SCL=1;
	IIC_SDA=1;
}
```

**2.产生 IIC 开始信号**

```c
//产生 IIC 起始信号
void IIC_Start(void)
{
	SDA_OUT(); //sda 线输出
	
	IIC_SDA=1;
	IIC_SCL=1;
	
	delay_us(4);
	
	IIC_SDA=0;//START:when CLK is high,DATA change form high to low
	
	delay_us(4);
	
	IIC_SCL=0;//钳住 I2C 总线， 准备发送或接收数据
}
```

**3.产生停止IIC信号**

```c
//产生 IIC 停止信号
void IIC_Stop(void)
{
	SDA_OUT();//sda 线输出
	
	IIC_SCL=0;
	IIC_SDA=0;//STOP:when CLK is high , DATA change form low to high
	delay_us(4);
	
	IIC_SCL=1;
	
	IIC_SDA=1;//发送 I2C 总线结束信号
	delay_us(4);
}
```

**4.等待应答信号**

```c
//等待应答信号到来
//返回值： 1，接收应答失败
// 0，接收应答成功
u8 IIC_Wait_Ack(void)
{
	u8 ucErrTime=0;
	
	SDA_IN(); //SDA 设置为输入
	
	IIC_SDA=1;
	delay_us(1);
	IIC_SCL=1;
	delay_us(1);
	
	while(READ_SDA)
	{
	ucErrTime++;
	if(ucErrTime>250)
	{
	IIC_Stop();
	return 1;
	}
	}
	IIC_SCL=0;//时钟输出 0
	return 0;
}
```

**5.应答信号**

```c
//产生 ACK 应答
void IIC_Ack(void)
{
	IIC_SCL=0;
	
	SDA_OUT();
	
	IIC_SDA=0;
	delay_us(2);
	
	IIC_SCL=1;
	
	delay_us(2);
	IIC_SCL=0;
}
```

**6.不产生应答**

```c
//不产生 ACK 应答
void IIC_NAck(void)
{
	IIC_SCL=0;
	SDA_OUT();
	
	IIC_SDA=1;
	
	delay_us(2);
	IIC_SCL=1;
	delay_us(2);
	IIC_SCL=0;
}
```

7.IIC发送一个字节

```c
//IIC 发送一个字节
//返回从机有无应答
//1，有应答
//0，无应答
void IIC_Send_Byte(u8 txd)
{
	u8 t;
	
	SDA_OUT();
	
	IIC_SCL=0;//拉低时钟开始数据传输
	
	for(t=0;t<8;t++)
	{
		IIC_SDA=(txd&0x80)>>7;//高位开始，一位一位的传数据
		txd<<=1;
		delay_us(2); //对 TEA5767 这三个延时都是必须的
		IIC_SCL=1;  //保持数据的稳定
		delay_us(2);
		IIC_SCL=0;   //下一位数据传输开始
		delay_us(2);
	}
}
```

**8.IIC读一个字节**

```c
//读 1 个字节， ack=1 时，发送 ACK， ack=0，发送 nACK
u8 IIC_Read_Byte(unsigned char ack)
{
	unsigned char i,receive=0;
	
	SDA_IN();//SDA 设置为输入
	
	for(i=0;i<8;i++ )
	{
		IIC_SCL=0;
		delay_us(2);
		IIC_SCL=1;
		receive<<=1;
		if(READ_SDA)
			receive++;
		delay_us(1);
	}
	if (!ack)
	IIC_NAck();//发送 nACK
	else
	IIC_Ack(); //发送 ACK
	return receive;
}
```

**（二）AT24C02 操作**

直接通过寄存器操作设置 IO 口的模式为输入还是输出，代码如下：

	#define SDA_IN() {GPIOC->CRH&=0XFFFF0FFF;GPIOC->CRH|=8<<12;}
	
	#define SDA_OUT() {GPIOC->CRH&=0XFFFF0FFF;GPIOC->CRH|=3<<12;}  

**1.初始化 IIC 接口**

```c
//初始化 IIC 接口
void AT24CXX_Init(void)
{
	IIC_Init();
}
```

**2.在 AT24CXX 指定地址读出一个数据**

```c
//在 AT24CXX 指定地址读出一个数据
//ReadAddr:开始读数的地址,24C02大小为2Kbit;ADDR:0x00---0x7FF
//返回值 :读到的数据

u8 AT24CXX_ReadOneByte(u16 ReadAddr)
{
	u8 temp=0;
	
	IIC_Start();
	if(EE_TYPE>AT24C16)
	/*宏定义有个选择芯片类型的，如果这个定义大于AT24C16的话就发送两个8bit地址，否则就发送一个8bit地址*/
	{
	IIC_Send_Byte(0XA0); //发送写命令,0XA0:器件的地址，7位地址+写（0）
	IIC_Wait_Ack();
	IIC_Send_Byte(ReadAddr>>8);//发送高地址
	}else IIC_Send_Byte(0XA0+((ReadAddr/256)<<1)); //发送器件地址 0XA0,写数据
	IIC_Wait_Ack();
	IIC_Send_Byte(ReadAddr%256); //发送低地址
	IIC_Wait_Ack();
	IIC_Start();
	IIC_Send_Byte(0XA1); //进入接收模式,7位地址+读（1）
	IIC_Wait_Ack();
	temp=IIC_Read_Byte(0);
	IIC_Stop();//产生一个停止条件
	return temp;
}
```

**3.在 AT24CXX 指定地址写入一个数据**

```c
//在 AT24CXX 指定地址写入一个数据
//WriteAddr :写入数据的目的地址
//DataToWrite:要写入的数据

void AT24CXX_WriteOneByte(u16 WriteAddr,u8 DataToWrite)
{
	IIC_Start();
	if(EE_TYPE>AT24C16)
	{
	IIC_Send_Byte(0XA0); //发送写命令
	IIC_Wait_Ack();
	IIC_Send_Byte(WriteAddr>>8);//发送高地址
	}else IIC_Send_Byte(0XA0+((WriteAddr/256)<<1)); //发送器件地址 0XA0,写数据
	IIC_Wait_Ack();
	IIC_Send_Byte(WriteAddr%256); //发送低地址
	IIC_Wait_Ack();
	IIC_Send_Byte(DataToWrite); //发送字节
	IIC_Wait_Ack();
	IIC_Stop(); //产生一个停止条件
	delay_ms(10); //对于 EEPROM 器件，每写一次要等待一段时间，否则写失败！
}
```

**4.AT24CXX 里面的指定地址开始写入长度为 Len 的数据**

```c
//在 AT24CXX 里面的指定地址开始写入长度为 Len 的数据
//该函数用于写入 16bit 或者 32bit 的数据.
//WriteAddr :开始写入的地址
//DataToWrite:数据数组首地址
//Len :要写入数据的长度 2,4

void AT24CXX_WriteLenByte(u16 WriteAddr,u32 DataToWrite,u8 Len)
{
	u8 t;
	for(t=0;t<Len;t++) 
		AT24CXX_WriteOneByte(WriteAddr+t,(DataToWrite>>(8*t))&0xff);
}
```

**5.AT24CXX 里面的指定地址开始读出长度为 Len 的数据**

```c
//在 AT24CXX 里面的指定地址开始读出长度为 Len 的数据
//该函数用于读出 16bit 或者 32bit 的数据.
//ReadAddr :开始读出的地址
//返回值 :数据
//Len :要读出数据的长度 2,4

u32 AT24CXX_ReadLenByte(u16 ReadAddr,u8 Len)
{
	u8 t; u32 temp=0;
	for(t=0;t<Len;t++)
	{
		temp<<=8;
		temp+=AT24CXX_ReadOneByte(ReadAddr+Len-t-1);
	}
	return temp;
}
```

**6.检查 AT24CXX 是否正常**

```c
//检查 AT24CXX 是否正常
//这里用了 24XX 的最后一个地址(255)来存储标志字.
//如果用其他 24C 系列,这个地址要修改
//返回 1:检测失败
//返回 0:检测成功
u8 AT24CXX_Check(void)
{
	u8 temp;
	temp=AT24CXX_ReadOneByte(255);//避免每次开机都写 AT24CXX
	if(temp==0X55)
		return 0;
	else//排除第一次初始化的情况
	{
		AT24CXX_WriteOneByte(255,0X55);
		temp=AT24CXX_ReadOneByte(255);
		if(temp==0X55)
			return 0;
	}
	return 1;
}
```

**7.AT24CXX 里面的指定地址开始读出指定个数的数据**

```c
/在 AT24CXX 里面的指定地址开始读出指定个数的数据
//ReadAddr :开始读出的地址 对 24c02 为 0~255
//pBuffer :数据数组首地址
//NumToRead:要读出数据的个数

void AT24CXX_Read(u16 ReadAddr,u8 *pBuffer,u16 NumToRead)
{
	while(NumToRead)
	{
		*pBuffer++=AT24CXX_ReadOneByte(ReadAddr++);
		NumToRead--;
	}
}
```

**8.AT24CXX 里面的指定地址开始写入指定个数的数据**

```c
//在 AT24CXX 里面的指定地址开始写入指定个数的数据
//WriteAddr :开始写入的地址 对 24c02 为 0~255
//pBuffer :数据数组首地址
//NumToWrite:要写入数据的个数

void AT24CXX_Write(u16 WriteAddr,u8 *pBuffer,u16 NumToWrite)
{
	while(NumToWrite--)
	{
		AT24CXX_WriteOneByte(WriteAddr,*pBuffer);
		WriteAddr++;
		pBuffer++;
	}
}
```


参考：

1.原子 STM32 开发库函数版本