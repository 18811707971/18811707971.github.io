---
layout: post
title: 51 软件模拟SPI驱动OLED
date: 2017-11-14
categories: 51单片机
tags: [SPI,OLED,C51]
description: SPI STUDY AND APPLICATION
---

## **1.OLED简介**

OLED，即有机发光二极管（Organic Light-Emitting Diode），又称为有机电激光显示（Organic Electroluminesence Display， OELD）。OLED由于同时具备**自发光**，不需背光源、对比度高、厚度薄、视角广、反应速度快、可用于挠曲性面板、使用温度范围广、构造及制程较简单等优异之特性，被认为是下一代的平面显示器新兴应用技术。

OLED显示技术具有自发光的特性，采用非常薄的有**机材料涂层和玻璃基板**，当有电流通过时，这些有机材料就会发光，而且OLED显示屏幕可视角度大，并且能够节省电能。

## **2.OLED模块简介**

本文采用中景园的 0.96 寸的OLED显示模块。原理图如下:

![这里写图片描述](http://img.blog.csdn.net/20171113215056393?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### **2.1模块特点：**

1）0.96 寸 OLED 有黄蓝，白，蓝三种颜色可选；其中黄蓝是屏上 1/4 部分为黄光，下 3/4 为蓝；而且是固定区域显示固定颜色，颜色和显示区域均不能修改；白光则为纯白，也就是黑底白字；蓝色则为纯蓝，也就是黑底蓝字。

2）分辨率为 128*64；

3）多种接口方式；OLED 裸屏总共种接口包括：6800、8080 两种并行接口方式、3 线或 4 线的串行 SPI 接口方式、 IIC 接口方式（只需要 2 根线就可以控制 OLED 了），这五种接口是通过屏上的 BS0~BS2 来配置的。

#### **2.2模块接口：**

|接口|功能|
|----|----|
|GND|电源地|
|VCC|电源正（3～5.5V）|
|D0|OLED 的 D0 脚，在 SPI 和 IIC 通信中为时钟管脚|
|D1|OLED 的 D1 脚，在 SPI 和 IIC 通信中为数据管脚|
|RES|OLED 的 RES#脚，用来复位（低电平复位）|
|DC|OLED 的 D/C#E 脚，数据和命令控制管脚|
|CS|OLED 的 CS#脚，片选管脚|


#### **2.3 OLED 驱动 IC：**

本屏所用的驱动 IC 为 **SSD1306**；其具有内部升压功能；所以在设计的时候不需要再专一设计升压电路；屏也可以选用外部升压，。SSD1306 的**每页包含了128 个字节，总共 8 页**，这样刚好是128*64 的点阵大小。

![这里写图片描述](http://img.blog.csdn.net/20171113220830905?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### **2.4 OLED 驱动方式：**

此模块支持四线 SPI、三线 SPI、IIC 接口和 6800、8080 并口方式，模块的通信接口是通过 BS0,BS1,BS2 三个管脚来配置。

![这里写图片描述](http://img.blog.csdn.net/20171113221538431?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### **2.5 SSD1306的命令**

![这里写图片描述](http://img.blog.csdn.net/20171114211415449?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**0X81：**设置对比度。包含两个字节，第一个0X81为命令，随后发送的一个字节为要设置的对比度的值。这个值设置得越大屏幕就越亮。

**0XAE,0XAF：** 0XAE为关闭显示命令；0XAF为开启显示命令。

**0X8D**：包含2个字节，第一个为命令字，第二个为设置值，第二个字节的BIT2表示电荷泵的开关状态，该位为1，则开启电荷泵，为0则关闭。在模块初始化的时候，这个必须要开启，否则是看不到屏幕显示的。
命令0XB0~B7：用于设置页地址，其低三位的值对应着GRAM的页地址。

**0X00~0X0F：**用于设置显示时的起始列地址低四位。
**0X10~0X1F：**用于设置显示时的起始列地址高四位。

## **3.实现程序**

#### **3.1 模拟SPI**

**时序：**

![这里写图片描述](http://img.blog.csdn.net/20171114211901879?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

对应时序图实现程序：

**程序：**

```c
sbit OLED_SCL=P3^0;//时钟 D0(SCLK)
sbit OLED_SDIN=P3^1;//D1（MOSI） 数据
sbit OLED_RST =P3^2;//复位
sbit OLED_DC =P3^3;//数据/命令控制
sbit OLED_CS=P3^4; //片选

//Function:向OLED设备写入一个字节
//Input:dat:写入的数据/命令;cmd:写入的是数据或命令判断
//cmd=0:写入命令;cmd=1:写入数据(DC为高:数据;DC为低:命令)
//Output:无
//Return:无
//Others:无
void Spi_Write_Byte(uchar dat,uchar cmd)
{
	uchar len;
	
	if(cmd)
		OLED_DC = 1;
	else
		OLED_DC = 0;
	OLED_CS = 0;
	for(len=0;len<8;len++)
	{
		OLED_SCL = 0;//低电平开始采样数据
		if(dat&0x80)
			OLED_SDIN = 1;
		else
			OLED_SDIN = 0;
		OLED_SCL = 1;
		dat = dat<<1;
	}
	OLED_CS = 1;
	OLED_DC = 1;
}
```

**显示部分实现：**

```c
#include "oled_display.h"
#include "oled_font.h"


//Function:设置显示坐标
//Input:x:横坐标;y:纵坐标
//Output:无
//Return:无
//Others:无
void OLED_Set_Pos(uchar x,uchar y)
{
	Spi_Write_Byte(0xb0+y,OLED_CMD);//列偏移
	Spi_Write_Byte(((x&0xf0)>>4)|0x10,OLED_CMD);//行高四位地址
	Spi_Write_Byte((x&0x0f)|0X01,OLED_CMD);//行低四位地址
}

//Function:开启OLED显示
//Input:无
//Output:无
//Return:无
//Others:无
void OLED_Display_On(void)
{
	Spi_Write_Byte(0X8D,OLED_CMD);//设置DCDC
	Spi_Write_Byte(0X14,OLED_CMD);//打开DCDC
	Spi_Write_Byte(0XAF,OLED_CMD);//打开OLED
}

//Function:关闭OLED显示
//Input:无
//Output:无
//Return:无
//Others:无
void OLED_Display_Off(void)
{
	Spi_Write_Byte(0X8D,OLED_CMD);//设置DCDC
	Spi_Write_Byte(0X10,OLED_CMD);//关闭DCDC
	Spi_Write_Byte(0XAE,OLED_CMD);//关闭OLED
}

//Function:清屏
//Input:无
//Output:无
//Return:无
//Others:无
void OLED_Display_Clear(void)
{
	uchar page,len;
	
	for(page=0;page<8;page++)
	{
		Spi_Write_Byte(0xB0+page,OLED_CMD);//设置页地址(0--7)
		Spi_Write_Byte(0X00,OLED_CMD);//设置显示位置—列低地址
		Spi_Write_Byte(0X10,OLED_CMD); //设置显示位置—列高地址
	
		for(len=0;len<128;len++)
			Spi_Write_Byte(0,OLED_DATA);//写入0;屏熄灭
	}
}
//Function:在指定位置显示一个字符,包括部分字符
	//x:0~127;y:0~63;size:选择字体 16/12 
//Input:x：横坐标;y:纵坐标;str:显示的字符
//Output:无
//Return:无
//Others:无				 
void OLED_Display_Onechar(uchar x,uchar y,uchar str)
{
	uchar i=0,ret=0;
	//ret = str -32;
	ret = str - ' ';//得到偏移后的值,对ASCLL码进行一个减法.即在二维数组里找它的位置  
	if(x>Max_Column-1)
	{
		x = 0;
		y = y + 2;//针对16号的字符
	}
	if(SIZE == 16 )
	{
		OLED_Set_Pos(x,y);
		//16的字体分成两部分写入
		for(i=0;i<8;i++)
			Spi_Write_Byte(F8X16[ret*16+i],OLED_DATA);
		OLED_Set_Pos(x,y+1);
		for(i=0;i<8;i++)
			Spi_Write_Byte(F8X16[ret*16+i+8],OLED_DATA);
	}
	else{
		OLED_Set_Pos(x,y+1);
		for(i=0;i<6;i++)
			Spi_Write_Byte(F6x8[ret][i],OLED_DATA);
	}
}

//Function:显示字符串
//Input:x：横坐标;y:纵坐标;str:显示的字符串
//Output:无
//Return:无
//Others:无
void OLED_Display_String(uchar x,uchar y,uchar *str)
{
	uchar i=0;
	
	while(str[i]!='\0')
	{
		OLED_Display_Onechar(x,y,str[i]);
		x += 8;
		if(x>120)
		{
			x = 0;
			y += 2;
		}
		i++;
	}
}

//Function:显示中文
//Input:x：横坐标;y:纵坐标;no:显示的字的序号
//Output:无
//Return:无
//Others:无
void OLED_Display_Chinese(uchar x,uchar y,uchar no)
{
	uchar ch,addr=0;
	
	OLED_Set_Pos(x,y);
	for(ch=0;ch<16;ch++)//数组行列寻址
	{
		Spi_Write_Byte(TEST[2*no][ch],OLED_DATA);
		addr += 1;
	}
	OLED_Set_Pos(x,y+1);
	for(ch=0;ch<16;ch++)
	{
		Spi_Write_Byte(TEST[2*no+1][ch],OLED_DATA);
		addr += 1;
	}
	
}

//Function:显示图片
//Input:x0,x1：横坐标;y0,y1:纵坐标;BMP:显示的图片
//注意：y1<8,页寻址模式
//Output:无
//Return:无
//Others:无
void OLED_Display_Picture(uchar x0,uchar y0,uchar x1,uchar y1,uchar BMP[])
{
	uchar x,y;
	uint i=0;
	
	if(y1%8==0)
		y = y1 / 8;
	else
		y = y1 /8 + 1;
	for(y=y0;y<y1;y++)
	{
		OLED_Set_Pos(x0,y);
		for(x=x0;x<x1;x++)
		{
			Spi_Write_Byte(BMP[i++],OLED_DATA);
		}
	}
}

//计算m的n次方
uint OLED_Pow(uchar m,uchar n)
{
	uint ret = 1;
	while(n--)
		ret *= m;
	return ret;
}

//Function:显示数字
//Input:x：横坐标;1:纵坐标;num:显示的数字：len：数字长度;size_num:数字字体
//Output:无
//Return:无
//Others:无
void OLED_Display_Num(uchar x,uchar y,uint num,uchar len,uchar size_num)
{
	uchar t,temp;
	uchar enshow = 0;
	
	for(t=0;t<len;t++)
	{
		temp = (num/OLED_Pow(10,len-t-1))%10;//把显示的数字一位一位取出来
		if(enshow==0&&t<(len-1))
		{
			if(temp==0)
			{
				OLED_Display_Onechar(x+(size_num/2)*t,y,' ');
				continue;
			}
			else
				enshow = 1;
		}
		OLED_Display_Onechar(x+(size_num/2)*t,y,temp+'0');
	}
}

//初始化SSD1306
void OLED_Init(void)
{
	OLED_RST = 1;
	delay_ms(200);
	OLED_RST = 0;
	delay_ms(200);
	OLED_RST = 1; 

	Spi_Write_Byte(0xAE,OLED_CMD);//关闭OLED
	Spi_Write_Byte(0x00,OLED_CMD);//设置列低位地址
	Spi_Write_Byte(0x10,OLED_CMD);//设置列高位地址
	Spi_Write_Byte(0x40,OLED_CMD);//设置起始行地址及映射RAM显示起始行 (0x00~0x3F)
	Spi_Write_Byte(0x81,OLED_CMD);//对比度设置
	Spi_Write_Byte(0xCF,OLED_CMD); // Set SEG Output Current Brightness
	Spi_Write_Byte(0xA1,OLED_CMD);//--Set SEG/Column Mapping     0xa0左右反置 0xa1正常
	Spi_Write_Byte(0xC8,OLED_CMD);//Set COM/Row Scan Direction   0xc0上下反置 0xc8正常
	Spi_Write_Byte(0xA6,OLED_CMD);//设置显示方式;bit0:1,反相显示;0,正常显示
	Spi_Write_Byte(0xA8,OLED_CMD);//设置驱动路数(1 to 64)
	Spi_Write_Byte(0x3f,OLED_CMD);//--1/64 duty
	Spi_Write_Byte(0xD3,OLED_CMD);//-设置显示偏移(0x00~0x3F)
	Spi_Write_Byte(0x00,OLED_CMD);//-not offset
	Spi_Write_Byte(0xd5,OLED_CMD);//--set display clock divide ratio/oscillator frequency
	Spi_Write_Byte(0x80,OLED_CMD);//--set divide ratio, Set Clock as 100 Frames/Sec
	Spi_Write_Byte(0xD9,OLED_CMD);//--set pre-charge period
	Spi_Write_Byte(0xF1,OLED_CMD);//Set Pre-Charge as 15 Clocks & Discharge as 1 Clock
	Spi_Write_Byte(0xDA,OLED_CMD);//--set com pins hardware configuration
	Spi_Write_Byte(0x12,OLED_CMD);
	Spi_Write_Byte(0xDB,OLED_CMD);//--set vcomh
	Spi_Write_Byte(0x40,OLED_CMD);//Set VCOM Deselect Level
	Spi_Write_Byte(0x20,OLED_CMD);//设置页地址模式(0x00/0x01/0x02)
	Spi_Write_Byte(0x02,OLED_CMD);//
	Spi_Write_Byte(0x8D,OLED_CMD);//--set Charge Pump enable/disable
	Spi_Write_Byte(0x14,OLED_CMD);//--set(0x10) disable
	Spi_Write_Byte(0xA4,OLED_CMD);//禁用显示开启;bit0:1,开启;0,关闭;(白屏/黑屏)(0xa4/0xa5)
	Spi_Write_Byte(0xA6,OLED_CMD);// 不使用反向显示 (0xa6/a7) 
	Spi_Write_Byte(0xAF,OLED_CMD);//打开显示
	
	Spi_Write_Byte(0xAF,OLED_CMD); /*display ON*/
	OLED_Display_Clear();
	OLED_Set_Pos(0,0); 	
}  
```
____

断断续续总算写完了，欢迎大家积极讨论，互相学习。




