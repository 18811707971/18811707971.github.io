---
layout: post
title: STM32学习笔记————DMA传输
date: 2018-03-26
categories: STM32
tags: [STM32,DMA]
description: The data transmit of stm32 dma 
---


## **1.简介**

**DMA：**全称为： Direct Memory Access，即直接存储器访问。 DMA 传输方式无需 CPU 直接控制传输，也没有中断处理方式那样保留现场和恢复现场的过程，通过硬件为 RAM 与 I/O 设备开辟一条直接传送数据的通路， 能使 CPU 的效率大为提高。

STM32 最多有 2 个 DMA 控制器（DMA2 仅存在大容量产品中）， DMA1 有 7 个通道。 DMA2 有 5个通道。每个通道专门用来管理来自于一个或多个外设对存储器访问的请求。还有一个仲裁起来协调各个 DMA 请求的优先权。

____

## **2.STM32 DMA特性**

●每个通道都直接连接专用的硬件 DMA 请求，每个通道都同样支持软件触发。这些功能通过软件来配置。

●在七个请求间的优先权可以通过软件编程设置(共有四级：很高、高、中等和低)，假如在相等优先权时由硬件决定(请求 0 优先于请求 1，依此类推) 。

●独立的源和目标数据区的传输宽度(字节、半字、全字)，模拟打包和拆包的过程，源和目标地址必须按数据传输宽度对齐。

●支持循环的缓冲器管理

●每个通道都有 3 个事件标志(DMA 半传输， DMA 传输完成和 DMA 传输出错)，这 3 个事件标志逻辑或成为一个单独的中断请求。

●存储器和存储器间的传输

●外设和存储器，存储器和外设的传输

●闪存、 SRAM、外设的 SRAM、 APB1 APB2 和 AHB 外设均可作为访问的源和目标。

●可编程的数据传输数目：最大为 65536

从外设（TIMx、 ADC、 SPIx、 I2Cx 和 USARTx）产生的 DMA 请求，通过**逻辑或**输入到
DMA 控制器，这就意味着同时只能有一个请求有效。外设的 DMA 请求，可以通过设置相应的
外设寄存器中的控制位，被独立地开启或关闭。

![这里写图片描述](https://img-blog.csdn.net/2018032622055419?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## **3.相关寄存器**

**DMA 中断状态寄存器（DMA_ISR）**

![这里写图片描述](https://img-blog.csdn.net/20180326222310403?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
![这里写图片描述](https://img-blog.csdn.net/20180326222342536?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

如果开启了 DMA_ISR 中这些中断，在达到条件后就会跳到中断服务函数里面去，即使没开启，我们也可以通过查询这些位来获得当前 DMA 传输的状态。这里我们常用的是 TCIFx，即通道 DMA 传输完成与否的标志。注意**此寄存器为只读寄存器**，所以在这些位被置位之后，只能通过其他的操作来清除。

**DMA 中断标志清除寄存器（DMA_IFCR）**

该寄存器的各位描述如下图：

![这里写图片描述](https://img-blog.csdn.net/20180326223349985?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

DMA_IFCR 的各位就是用来清除 DMA_ISR 的对应位的，通过写 1 清除。在 DMA_ISR 被置位后，我们必须通过向该位寄存器对应的位写入 1 来清除。

**DMA 通道 x 配置寄存器（DMA_CCRx）（x=1~7）**

该寄存器控制着 DMA 的很多相关信息，包括**数据宽度、外设及存储器的宽度、通道优先级、增量模式、传输方向、中断允许、使能**等都是通过该寄存器来设置的。所以 DMA_CCRx 是 DMA 传输的核心控制寄存器。

![这里写图片描述](https://img-blog.csdn.net/20180326224218700?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![这里写图片描述](https://img-blog.csdn.net/20180326224302391?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![这里写图片描述](https://img-blog.csdn.net/20180326224341420?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![这里写图片描述](https://img-blog.csdn.net/20180326224408471?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**DMA 通道 x 传输数据量寄存器（DMA_CNDTRx）**

这个寄存器控制 DMA 通道 x 的每次传输所要传输的数据量。其设置范围为 0~65535。并且该寄存器的值会随着传输的进行而减少，当该寄存器的值为 0 的时候就代表此次数据传输已经全部发送完成了。所以可以通过这个寄存器的值来知道当前 DMA 传输的进度。

![这里写图片描述](https://img-blog.csdn.net/20180326231155279?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d3dDE4ODExNzA3OTcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**DMA 通道 x 的外设地址寄存器（DMA_CPARx）**

该寄存器用来存储 STM32 外设的地址，比如我们使用串口 1，那么该寄存器必须写入 0x40013804（其实就是&USART1_DR）。如果使用其他外设，就修改成相应外设的地址就行了。

 **DMA 通道 x 的存储器地址寄存器（DMA_CMARx）**
该寄存器和 DMA_CPARx 差不多，但是是用来放存储器的地址的。比如我们使用SendBuf[5200]数组来做存储器，那么我们在DMA_CMARx 中写入&SendBuff 就可以了。

_____

## **4.库函数下 DMA1 通道 4 的配置步骤**

（1）使能 DMA 时钟
```c
RCC_AHBPeriphClockCmd(RCC_AHBPeriph_DMA1, ENABLE); //使能 DMA 时钟
```
（2） 初始化 DMA 通道 4 参数

DMA 通道配置参数种类比较繁多，包括**内存地址，外设地址，传输数据长度，数据宽度，通道优先级**等等。这些参数的配置在库函数中都是在函数 DMA_Init 中完成.

函数定义：
```c
void DMA_Init(DMA_Channel_TypeDef* DMAy_Channelx,DMA_InitTypeDef* DMA_InitStruct)
```
第一个参数:指定初始化的 DMA 通道号;
第二个参数: 跟其他外设一样，同样是通过初始化结构体成员变量值来达到初始化的目的。

 DMA_InitTypeDef 结构体的定义：
```c
typedef struct
		{
		uint32_t DMA_PeripheralBaseAddr;
		uint32_t DMA_MemoryBaseAddr;
		uint32_t DMA_DIR;
		uint32_t DMA_BufferSize;
		uint32_t DMA_PeripheralInc;
		uint32_t DMA_MemoryInc;
		uint32_t DMA_PeripheralDataSize;
		uint32_t DMA_MemoryDataSize;
		uint32_t DMA_Mode;
		uint32_t DMA_Priority;
		uint32_t DMA_M2M;
		}DMA_InitTypeDef;
```

第一个参数 ：DMA_PeripheralBaseAddr 用来设置 **DMA 传输的外设基地址**，比如要进行串口 DMA 传输，那么外设基地址为串口接受发送数据存储器 USART1->DR 的地址，表示方法为
&USART1->DR。

第二个参数 ：DMA_MemoryBaseAddr为**内存基地址**，也就是我们存放 DMA传输数据的内存地址。

第三个参数： DMA_DIR 设置**数据传输方向**，决定是从外设读取数据到内存还送从内存读取数据发送到外设，也就是外设是源地还是目的地，这里我们设置为从内存读取数据发送到串口，
所以外设自然就是目的地了，所以选择值为 DMA_DIR_PeripheralDST。

第四个参数： DMA_BufferSize 设置**一次传输数据量的大小**。

第五个参数 ：DMA_PeripheralInc 设置**传输数据的时候外设地址是不变还是递增**。如果设置
为递增，那么下一次传输的时候地址加 1，这里因为我们是一直往固定外设地址&USART1->DR
发送数据，所以地址不递增，值为 DMA_PeripheralInc_Disable；

第六个参数 ：DMA_MemoryInc 设置**传输数据时候内存地址是否递增**。 这个参数和
DMA_PeripheralInc 意思接近，只不过针对的是内存。这里我们的场景是将内存中连续存储单
元的数据发送到串口，毫无疑问内存地址是需要递增的，所以值为 DMA_MemoryInc_Enable。

第七个参数 ：DMA_PeripheralDataSize 用来设置外设的的**数据长度是为字节传输（8bits），半字传输 (16bits)还是字传输 (32bits)**，这里我们是 8 位字节传输，所以值设置为DMA_PeripheralDataSize_Byte。

第八个参数 ：DMA_MemoryDataSize 是用来设置**内存的数据长度**，和第七个参数意思接近，这里我们同样设置为字节传输 DMA_MemoryDataSize_Byte。

第九个参数： DMA_Mode 用来**设置 DMA 模式是否循环采集**，也就是说，比如我们要从内存中采集 64 个字节发送到串口，如果设置为重复采集，那么它会在 64 个字节采集完成之后继续从内存的第一个地址采集，如此循环。这里我们设置为一次连续采集完成之后不循环。所以设置值为 DMA_Mode_Normal。在我们下面的实验中，如果设置此参数为循环采集，那么你会看到串口不停的打印数据，不会中断。

第十个参数：是**设置 DMA 通道的优先级**，有低，中，高，超高三种模式，这里设置优先级别为中级，所以值为 DMA_Priority_Medium。如果要开启多个通道，那么这个值就非常有意义。

第 十 一 个 参 数 ：DMA_M2M **设 置 是 否 是 存 储 器 到 存 储 器 模 式 传 输** ， 这 里 我 们 选 择DMA_M2M_Disable。



（3）使能串口 DMA 发送

进行 DMA 配置之后，我们就要开启串口的 DMA 发送功能，使用的函数是：
```c
USART_DMACmd(USART1,USART_DMAReq_Tx,ENABLE);
```
如果是要使能串口 DMA 接受，那么第二个参数修改为 USART_DMAReq_Rx 即可。

（4）使能 DMA1 通道 4，启动传输。

使能串口 DMA 发送之后，我们接着就要使能 DMA 传输通道：
```c
DMA_Cmd(DMA_CHx, ENABLE);
```
通过以上 3 步设置，我们就可以启动一次 USART1 的 DMA 传输了。

（5）查询 DMA 传输状态

在 DMA 传输过程中，我们要查询 DMA 传输通道的状态，使用的函数是：
```c
FlagStatus DMA_GetFlagStatus(uint32_t DMAy_FLAG)
```
比如我们要查询 DMA 通道 4 传输是否完成，方法是：
```c
DMA_GetFlagStatus(DMA2_FLAG_TC4);
```
这里还有一个比较重要的函数就是获取当前剩余数据量大小的函数：
```c
uint16_t DMA_GetCurrDataCounter(DMA_Channel_TypeDef* DMAy_Channelx)
```
比如我们要获取 DMA 通道 4 还有多少个数据没有传输，方法是：
```c
DMA_GetCurrDataCounter(DMA1_Channel4);
```

___

## **5.软件实现**

（1）DMA实现

```c
#include "dma.h"

DMA_InitTypeDef DMA_InitStructure;
u16 DMA1_MEM_LEN;//保存 DMA 每次数据传送的长度

//DMA1 的各通道配置
//这里的传输形式是固定的,这点要根据不同的情况来修改
//从存储器->外设模式/8 位数据宽度/存储器增量模式
//DMA_CHx:DMA 通道 CHx
//cpar:外设地址
//cmar:存储器地址
//cndtr:数据传输量

void MYDMA_Config(DMA_Channel_TypeDef* DMA_CHx,u32 cpar,u32 cmar,u16 cndtr)
{
	RCC_AHBPeriphClockCmd(RCC_AHBPeriph_DMA1, ENABLE); //使能 DMA 时钟
	DMA_DeInit(DMA_CHx); //将 DMA 的通道 1 寄存器重设为缺省值
	DMA1_MEM_LEN=cndtr;
	DMA_InitStructure.DMA_PeripheralBaseAddr = cpar; //DMA 外设 ADC 基地址
	DMA_InitStructure.DMA_MemoryBaseAddr = cmar; //DMA 内存基地址
	DMA_InitStructure.DMA_DIR = DMA_DIR_PeripheralDST; //数据传输方向内存到外设
	DMA_InitStructure.DMA_BufferSize = cndtr; //DMA 通道的 DMA 缓存的大小
	DMA_InitStructure.DMA_PeripheralInc = DMA_PeripheralInc_Disable; //外设地址不变
	DMA_InitStructure.DMA_MemoryInc = DMA_MemoryInc_Enable;//内存地址寄存器递增
	DMA_InitStructure.DMA_PeripheralDataSize =   DMA_PeripheralDataSize_Byte;
//数据宽度为 8 位
	DMA_InitStructure.DMA_MemoryDataSize = DMA_MemoryDataSize_Byte; //数据宽度为 8 位
	DMA_InitStructure.DMA_Mode = DMA_Mode_Normal; //工作在正常缓存模式
	DMA_InitStructure.DMA_Priority = DMA_Priority_Medium; //DM 通道拥有中优先级
	DMA_InitStructure.DMA_M2M = DMA_M2M_Disable; //非内存到内存传输
	DMA_Init(DMA_CHx, &DMA_InitStructure); //初始化 DMA 的通道
}

//开启一次 DMA 传输
void MYDMA_Enable(DMA_Channel_TypeDef*DMA_CHx)
{
	DMA_Cmd(DMA_CHx, DISABLE ); //关闭 USART1 TX DMA1 所指示的通道
	DMA_SetCurrDataCounter(DMA1_Channel4,DMA1_MEM_LEN);//设置 DMA 缓存的大小
	DMA_Cmd(DMA_CHx, ENABLE); //使能 USART1 TX DMA1 所指示的通道
}
```

（2）主函数实现

```c
const u8 TEXT_TO_SEND[]={"ALIENTEK Mini STM32 DMA 串口实验"};
#define TEXT_LENTH  sizeof(TEXT_TO_SEND)-1			//TEXT_TO_SEND字符串长度(不包含结束符)
u8 SendBuff[(TEXT_LENTH+2)*100];

 int main(void)
 { 
	u16 i;
	u8 t=0; 
	float pro=0;			//进度 
	delay_init();	    	 //延时函数初始化	  
	uart_init(9600);	 	//串口初始化为9600
	LED_Init();		  		//初始化与LED连接的硬件接口
	LCD_Init();			   	//初始化LCD 
	KEY_Init();				//按键初始化		 	
 	MYDMA_Config(DMA1_Channel4,(u32)&USART1->DR,(u32)SendBuff,(TEXT_LENTH+2)*100);//DMA1通道4,外设为串口1,存储器为SendBuff,长(TEXT_LENTH+2)*100.
 	POINT_COLOR=BLUE;//设置字体为红色 
	LCD_ShowString(60,50,200,16,16,"Mini STM32");	
	LCD_ShowString(60,70,200,16,16,"DMA TEST");	
	LCD_ShowString(60,90,200,16,16,"ATOM@ALIENTEK");
	LCD_ShowString(60,110,200,16,16,"2018/3/26");	
 	LCD_ShowString(60,130,200,16,16,"KEY0:Start");
	//显示提示信息	   
	for(i=0;i<(TEXT_LENTH+2)*100;i++)//填充ASCII字符集数据
    {
		if(t>=TEXT_LENTH)//加入换行符
		{ 
			SendBuff[i++]=0x0d; 
			SendBuff[i]=0x0a; 
			t=0;
		}else 
			SendBuff[i]=TEXT_TO_SEND[t++];//复制TEXT_TO_SEND语句    
    }		 
	POINT_COLOR=BLACK;//设置字体为蓝色	  
	i=0;
	while(1)
	{
		t=KEY_Scan(0);
		if(t==KEY0_PRES)//KEY0按下
		{
			LCD_ShowString(60,150,200,16,16,"Start Transimit....");
			LCD_ShowString(60,170,200,16,16,"   %");//显示百分号
			printf("\r\nDMA DATA:\r\n "); 	    
		    USART_DMACmd(USART1,USART_DMAReq_Tx,ENABLE); //????1?DMA??        
			MYDMA_Enable(DMA1_Channel4);//开始一次DMA传输！	  
		    //等待DMA传输完成，此时我们来做另外一些事，点灯
		    //实际应用中，传输数据期间，可以执行另外的任务
		    while(1)
		    {
				if(DMA_GetFlagStatus(DMA1_FLAG_TC4)!=RESET)//等待通道4传输完成
				{
					DMA_ClearFlag(DMA1_FLAG_TC4);//清除通道4传输完成标志
					break; 
		        }
				pro=DMA_GetCurrDataCounter(DMA1_Channel4);//得到当前还剩余多少个数据
				pro=1-pro/((TEXT_LENTH+2)*100);//得到百分比	  
				pro*=100;      //扩大100倍
				LCD_ShowNum(60,170,pro,3,16);	  
		    }			    
			LCD_ShowNum(60,170,100,3,16);//显示100%	  
			LCD_ShowString(60,150,200,16,16,"Transimit Finished!");//提示传送完成
		}
		i++;
		delay_ms(10);
		if(i==20)
		{
			LED0=!LED0;//提示系统正在运行	
			i=0;
		}		   
	}
}
```

____

**参考**：

1.正点原子库函数教程


