---
layout: post
title: STM32学习笔记一一USART
date: 2017-05-21
categories: STM32
tags: [GPIO,USART,EXIT]
description: STM32 LEARN
---

1.串口的基本概念

通用同步异步收发器(USART)提供了一种灵活的方法与使用工业标准NRZ异步串行数据格式的外部设备之间进行全双工数据交换。 USART利用分数波特率发生器提供宽范围的波特率选择。它支持同步单向通信和半双工单线通信，也支持LIN(局部互连网)，智能卡协议和IrDA(红外数据组织)SIR ENDEC规范，以及调制解调器(CTS/RTS)操作。它还允许多处理器通信。使用多缓冲器配置的DMA方式，可以实现高速数据通信。

2.串口通信连接

接口通过三个引脚与其他设备连接在一起。任何USART双向通信至少需要两个脚：接收数据输入(RX)和发送数据输出(TX)。
RX：接收数据串行输。通过过采样技术来区别数据和噪音，从而恢复数据。
TX：发送数据输出。当发送器被禁止时，输出引脚恢复到它的I/O端口配置。当发送器被激活，并且不发送数据时， TX引脚处于高电平。在单线和智能卡模式里，此I/O口被同时用于数据的发送和接收。

3.串口设置的一般步骤

对于复用功能的 IO，我们首先要使能 GPIO 时钟，然后使能复用功能时钟，同时要把 GPIO 模式设置为复用功能对应的模式，串口参数的初始化设置，包括波特率，停止位等等参数。在设置完成后就是使能串口。同时，如果开启了串口的中断，当然要初始化 NVIC 设置中断优先级别，最后编写中断服务函数。

　　串口设置的一般步骤可以总结为如下几个步骤：

　　　　1) 串口时钟使能，GPIO 时钟使能

　　　　2) 串口复位

　　　　3) GPIO 端口模式设置

　　　　4) 串口参数初始化

　　　　5) 开启中断并且初始化 NVIC（如果开启中断才需要这个步骤）

　　　　6) 使能串口

　　　　7) 编写中断处理函数

　　与串口基本配置直接相关定义主要分布在 stm32f10x_usart.h 和 stm32f10x_usart.c 文件中。

4.主要库函数功能介绍

|函数名|功能概要|解释|
|-------|:------:|--------:|
|RCC_APB2PeriphClockCmd(RCC_APB2Periph_USART1)|串口时钟使能|串口挂载在 APB2 下|
|USART_DeInit(USART_TypeDef* USARTx)|串口复位|当外设出现异常时,通过复位设置，使其重新工作|
|USART_Init(USART_TypeDef* USARTx, USART_InitTypeDef* USART_InitStruct)|串口参数初始化|初始化需要设置的参数为：波特率，字长，停止位，奇偶校验位，硬件数据流控制，模式|
|USART_SendData(USART_TypeDef* USARTx, uint16_t Data)|数据发送|向串口寄存器 USART_DR 写入一个数据|
|USART_ReceiveData(USART_TypeDef* USARTx)|数据接收|操作 USART_DR 寄存器读取串口接收到的数据|
|FlagStatus USART_GetFlagStatus(USART_TypeDef* USARTx, uint16_t USART_FLAG)|读取串口状态|
|USART_GetFlagStatus(USART1, USART_FLAG_RXNE)|判断读寄存器是否非空(RXNE)|
|USART_GetFlagStatus(USART1, USART_FLAG_TC)|判断发送是否完成(TC)|
|USART_Cmd(USART1, ENABLE)|串口使能|
|USART_ITConfig(USART_TypeDef* USARTx, uint16_t USART_IT,FunctionalState NewState)|开启串口响应中断|
|ITStatus USART_GetITStatus(USART_TypeDef* USARTx, uint16_t USART_IT)|获取相应中断状态|中断发生，会设置状态寄存器中的某个标志位，返回值是SET，说明是串口发送完成中断发生|

说明：串口的状态可以通过状态寄存器 USART_SR 读取。寄存器如下：

![这里写图片描述](http://img.blog.csdn.net/20170521173940982?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

RXNE（读数据寄存器非空），当该位被置 1 的时候，就是提示已经有数据被接收到了，并且可以读出来了。这时候我们要做的就是尽快去读取 USART_DR，通过读 USART_DR 可以将该位清零，也可以向该位写 0，直接清除。

TC（发送完成），当该位被置位的时候，表示 USART_DR 内的数据已经被发送完成了。如果设置了这个位的中断，则会产生中断。该位也有两种清零方式： 1）读 USART_SR，写USART_DR。 2）直接向该位写 0。

5.配置全双工的串口 1(参看STM32中文参考手册_V10)

TX(PA9)管脚需要配置为推挽复用输出；
RX(PA10)管脚配置为浮空输入或者带上拉输入。

![这里写图片描述](http://img.blog.csdn.net/20170521174851670?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

6.例程——USART1为例
(1) 初始化：
```c
void uart_init(u32 bound){
  //GPIO端口设置
	GPIO_InitTypeDef GPIO_InitStructure;
	USART_InitTypeDef USART_InitStructure;
	NVIC_InitTypeDef NVIC_InitStructure;
	 
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_USART1|RCC_APB2Periph_GPIOA, ENABLE);	//使能USART1，GPIOA时钟
  
	//USART1_TX   GPIOA.9
  GPIO_InitStructure.GPIO_Pin = GPIO_Pin_9; //PA.9
  GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
  GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;	//复用推挽输出
  GPIO_Init(GPIOA, &GPIO_InitStructure);//初始化GPIOA.9
   
  //USART1_RX	  GPIOA.10初始化
  GPIO_InitStructure.GPIO_Pin = GPIO_Pin_10;//PA10
  GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IN_FLOATING;//浮空输入
  GPIO_Init(GPIOA, &GPIO_InitStructure);//初始化GPIOA.10  

  //Usart1 NVIC 配置
  NVIC_InitStructure.NVIC_IRQChannel = USART1_IRQn;
	NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority=3 ;//抢占优先级3
	NVIC_InitStructure.NVIC_IRQChannelSubPriority = 3;		//子优先级3
	NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;			//IRQ通道使能
	NVIC_Init(&NVIC_InitStructure);	//根据指定的参数初始化NVIC寄存器
  
   //USART 初始化设置
	USART_InitStructure.USART_BaudRate = bound;//串口波特率
	USART_InitStructure.USART_WordLength = USART_WordLength_8b;//字长为8位数据格式
	USART_InitStructure.USART_StopBits = USART_StopBits_1;//一个停止位
	USART_InitStructure.USART_Parity = USART_Parity_No;//无奇偶校验位
	USART_InitStructure.USART_HardwareFlowControl = USART_HardwareFlowControl_None;//无硬件数据流控制
	USART_InitStructure.USART_Mode = USART_Mode_Rx | USART_Mode_Tx;	//收发模式

  USART_Init(USART1, &USART_InitStructure); //初始化串口1
  USART_ITConfig(USART1, USART_IT_RXNE, ENABLE);//开启串口接受中断
  USART_Cmd(USART1, ENABLE);                    //使能串口1 

}
```

(2) 中断：
```c
void USART1_IRQHandler(void)                	//串口1中断服务程序
	{
	u8 Res;
#if SYSTEM_SUPPORT_OS 		//如果SYSTEM_SUPPORT_OS为真，则需要支持OS.
	OSIntEnter();    
#endif
	if(USART_GetITStatus(USART1, USART_IT_RXNE) != RESET)  //接收中断(接收到的数据必须是0x0d 0x0a结尾)
		{
		Res =USART_ReceiveData(USART1);	//读取接收到的数据
		
		if((USART_RX_STA&0x8000)==0)//接收未完成
			{
			if(USART_RX_STA&0x4000)//接收到了0x0d
				{
				if(Res!=0x0a)USART_RX_STA=0;//接收错误,重新开始
				else USART_RX_STA|=0x8000;	//接收完成了 
				}
			else //还没收到0X0D
				{	
				if(Res==0x0d)USART_RX_STA|=0x4000;
				else
					{
					USART_RX_BUF[USART_RX_STA&0X3FFF]=Res ;
					USART_RX_STA++;
					if(USART_RX_STA>(USART_REC_LEN-1))USART_RX_STA=0;//接收数据错误,重新开始接收	  
					}		 
				}
			}   		 
     } 
#if SYSTEM_SUPPORT_OS 	//如果SYSTEM_SUPPORT_OS为真，则需要支持OS.
	OSIntExit();  											 
#endif
}
```
注：

1) USART_RX_STA是一个接收状态标记。长度16位：
bit15：	接收完成标志
bit14：接收到0x0d 
bit13~0：接收到的有效字节数目

2) 中断注意事项： 
中断发生后必须清除中断位，否则会出现死循环不断发生这个中断。然后需要对中断类型进行判断再执行代码。 

使用EXTI的I/O中断，在完成RCC与GPIO硬件设置之后需要做三件事：初始化EXTI、NVIC开中断、编写中断执行代码

3）修改 printf 指向的串口
```c
//修改此函数即可
int fputc(int ch, FILE *f)
{      
	while((USART2->SR&0X40)==0);//循环发送,直到发送完毕   
    USART2->DR = (u8) ch;      
	return ch;
}
```
  
  参考：
1.[STM32学习笔记——USART串口 ](http://www.cnblogs.com/wupengda/p/4094259.html)

2.[STM32串口USART1的使用方法和程序 ](http://www.cnblogs.com/zitech/p/4989001.html)

3.[ STM32串口设定流程总结](http://blog.csdn.net/zhangzhang114pm/article/details/51331211)

4.[STM32的串口通信 ](http://www.cnblogs.com/hnrainll/archive/2011/01/12/1933400.html)

5.原子库函数指导手册







