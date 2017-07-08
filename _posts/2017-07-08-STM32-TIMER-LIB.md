---
layout: post
title: STM32学习笔记一一定时器中断
date: 2017-07-08
categories: STM32
tags: [SYSCLK,LED,TIMER]
description: STM32 LEARN
---

1.STM32 通用定时器简介

STM32 的通用定时器是一个通过可编程预分频器（ PSC）驱动的 16 位自动装载计数器（ CNT）构成。使用定时器预分频器和 RCC 时钟控制器预分频器，脉冲长度和波形周期可以在几个微秒到几个毫秒间调整。 STM32 的每个通用定时器都是完全独立的，没有互相共享的任何资源。

TIMER主要是由三部分组成：时基单元， 输入捕获， 输出比较。


2.ST32 的通用定时器功能

通用定时器有TIMx (TIM2、 TIM3、 TIM4 和 TIM5)，具体功能如下：

1)16 位向上、向下、向上/向下自动装载计数器（ TIMx_CNT）；

2)16 位可编程(可以实时修改)预分频器(TIMx_PSC)，计数器时钟频率的分频系数为 1～65535 之间的任意数值。

3） 4 个独立通道（ TIMx_CH1~4），这些通道可以用来作为：

  A．输入捕获;
  
  B．输出比较;
  
  C． PWM 生成(边缘或中间对齐模式);
  
  D．单脉冲模式输出;
  
4）可使用外部信号（ TIMx_ETR）控制定时器和定时器互连（可以用 1 个定时器控制另外一个定时器）的同步电路。

5）如下事件发生时产生中断/DMA：

  A．更新：计数器向上溢出/向下溢出，计数器初始化(通过软件或者内部/外部触发);
  
  B．触发事件(计数器启动、停止、初始化或者由内部/外部触发计数);
  
  C．输入捕获;
  
  D．输出比较;
  
  E．支持针对定位的增量(正交)编码器和霍尔传感器电路;
  
  F．触发输入作为外部时钟或者按周期的电流管理.
  
3.定时器相关的寄存器

（1）控制寄存器 1（ TIMx_CR1），各个位介绍如下:

![这里写图片描述](http://img.blog.csdn.net/20170708092100879?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170708092146724?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170708092227500?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

（2）DMA/中断使能寄存器（ TIMx_DIER）：16位如下图

![这里写图片描述](http://img.blog.csdn.net/20170708092301969?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

它的第 0 位，该位是更新中断允许位，设置为 1，来允许由于更新事件所产生的中断。

（3）预分频寄存器（ TIMx_PSC）

![这里写图片描述](http://img.blog.csdn.net/20170708092337503?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

定时器的时钟来源有 4 个：

1） 内部时钟（ CK_INT）

2） 外部时钟模式 1：外部输入脚（ TIx）

3） 外部时钟模式 2：外部触发输入（ ETR）

4） 内部触发输入（ ITRx）：使用 A 定时器作为 B 定时器的预分频器（ A 为 B 提供时钟）。

注：

	a. 具体选择通过 TIMx_SMCR 寄存器的相关位来设置。CK_INT时钟是从 APB1 倍频的来的，STM32 中除非 APB1 的时钟分频数设置为 1，否则通用定时器  TIMx 的时钟是 APB1 时钟的 2 倍，当 APB1 的时钟不分频的时候，通用定时器 TIMx 的时钟就等于APB1 的时钟。
	
	b.高级定时器的时钟来自 APB2 。

（3）TIMx_CNT 寄存器

该寄存器是定时器的计数器，该寄存器存储了当前定时器的计数值。

3.1计数模式

a.向上计数模式：

  在向上计数模式中，计数器从0计数到自动加载值(TIMx_ARR计数器的内容)，然后重新从0开始计数并且产生一个计数器溢出事件。

b.向下计数模式：

  在向下模式中，计数器从自动装入的值(TIMx_ARR计数器的值)开始向下计数到0，然后从自动装入的值重新开始并且产生一个计数器向下溢出事件。

c.中央对齐模式：

  在中央对齐模式，计数器从0开始计数到自动加载的值(TIMx_ARR寄存器)-1，产生一个计数器溢出事件，然后向下计数到1并且产生一个计数器下溢事件；然后再从0开始重新计数。

d.重复计数器功能

  如果使用了重复计数器功能，在向上计数达到设置的重复计数次数(TIMx_RCR)时，产生更新事件(UEV)；否则每次计数器溢出时才产生更新事件。

（4）自动重装载寄存器（ TIMx_ARR），各位如下图：

![这里写图片描述](http://img.blog.csdn.net/20170708092438717?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

此寄存器在物理上实际对应着 2 个寄存器。一个是程序员可以直接操作的，另外一个是程序员看不到的，这个看不到的寄存器被叫做影子寄存器。事实上，真正起作用的是影子寄存器。 根据 TIMx_CR1 寄存器中 APRE 位的设置： APRE=0 时，预装载寄存器的内容可以随时传送到影子寄存器，此时 2者是连通的；而 APRE=1 时，在每一次更新事件（ UEV）时，才把预装在寄存器的内容传送到影子寄存器。

（6）状态寄存器（ TIMx_SR）

该寄存器用来标记当前与定时器相关的各种事件/中断是否发生。

![这里写图片描述](http://img.blog.csdn.net/20170708092514770?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

4.定时器中断的配置步骤

1） TIM3 时钟使能。

TIM3 是挂载在 APB1 之下，所以我们通过 APB1 总线下的时钟使能函数来使能 TIM3。调用的函数是：

RCC_APB1PeriphClockCmd(RCC_APB1Periph_TIM3, ENABLE); //时钟使能

2）初始化定时器参数,设置自动重装值， 分频系数，计数方式等。

在库函数中，定时器的初始化参数是通过初始化函数 TIM_TimeBaseInit 实现的：

voidTIM_TimeBaseInit(TIM_TypeDef*TIMx,TIM_TimeBaseInitTypeDef* TIM_TimeBaseInitStruct);

第一个参数：确定是哪个定时器

第二个参数：定时器初始化参数结构体指针，结构体类型为 TIM_TimeBaseInitTypeDef。

结构体的定义：

```c
 typedef struct
 {
  uint16_t TIM_Prescaler;
  uint16_t TIM_CounterMode;
  uint16_t TIM_Period;
  uint16_t TIM_ClockDivision;
  uint8_t TIM_RepetitionCounter;
} TIM_TimeBaseInitTypeDef;
```

注：对于通用定时器只有前面四个参数有用，最后一个参数 TIM_RepetitionCounter 是高级定时器才有用的。

第一个参数 TIM_Prescaler：设置分频系数。

第二个参数 TIM_CounterMode：设置计数方式，可以设置为向上计数，向下计数方式还有中央对齐计数方式，比较常用的是向上计数模式 TIM_CounterMode_Up 和向下计数模式 TIM_CounterMode_Down。

第三个参数TIM_Period：设置自动重载计数周期值。

第四个参数TIM_ClockDivision：设置时钟分频因子。

TIM3 初始化：

```c
TIM_TimeBaseInitTypeDef TIM_TimeBaseStructure;
TIM_TimeBaseStructure.TIM_Period = 5000;
TIM_TimeBaseStructure.TIM_Prescaler =7199;
TIM_TimeBaseStructure.TIM_ClockDivision = TIM_CKD_DIV1;
TIM_TimeBaseStructure.TIM_CounterMode = TIM_CounterMode_Up;
TIM_TimeBaseInit(TIM3, &TIM_TimeBaseStructure);
```

3）设置 TIM3_DIER 允许更新中断。

因为我们要使用 TIM3 的更新中断，寄存器的相应位便可使能更新中断。在库函数里面定时器中断使能是通过 TIM_ITConfig 函数来实现的：

void TIM_ITConfig(TIM_TypeDef* TIMx, uint16_t TIM_IT, FunctionalState NewState)；

第一个参数：选择定时器号，取值为 TIM1~TIM17。

第二个参数：用来指明我们使能的定时器中断的类型，定时器中断的类型有很

多种，包括更新中断 TIM_IT_Update，触发中断 TIM_IT_Trigger，以及输入捕获中断等等。

第三个参数：失能还是使能。

使能 TIM3 的更新中断，格式为：TIM_ITConfig(TIM3,TIM_IT_Update,ENABLE );

4） TIM3 中断优先级设置。

在定时器中断使能之后，因为要产生中断，必不可少的要设置 NVIC 相关寄存器，设置中断优先级。

5）允许 TIM3 工作，也就是使能 TIM3。

在配置好定时器后边要开启定时器，通过 TIM3_CR1 的 CEN 位来设置。在固件库里面使能定时器的函数是通过 TIM_Cmd 函数来实现的：

void TIM_Cmd(TIM_TypeDef* TIMx, FunctionalState NewState)

使能定时器 3，方法为：TIM_Cmd(TIM3, ENABLE); //使能 TIMx 外设

6） 编写中断服务函数。

最后，编写定时器中断服务函数，通过该函数来处理定时器产生的相关中断。在中断产生后，通过状态寄存器的值来判断此次产生的中断属于什么类型。然后执行相关的操作，这里使用的是更新（溢出）中断，所以在状态寄存器 SR 的最低位。在处理完中断之后应该向 TIM3_SR 的最低位写 0，来清除该中断标志。

a. 在固件库函数里面，用来读取中断状态寄存器的值判断中断类型的函数是：

ITStatus TIM_GetITStatus(TIM_TypeDef* TIMx, uint16_t)

该函数的作用：判断定时器 TIMx 的中断类型 TIM_IT 是否发生中断。比如，我们要判断定时器 3 是否发生更新（溢出）中断，

方法为：
if (TIM_GetITStatus(TIM3, TIM_IT_Update) != RESET){}

b. 固件库中清除中断标志位的函数是：

void TIM_ClearITPendingBit(TIM_TypeDef* TIMx, uint16_t TIM_IT)

该函数的作用：清除定时器 TIMx 的中断 TIM_IT 标志位。比如在
TIM3 的溢出中断发生后，我们要清除中断标志位，

方法是：TIM_ClearITPendingBit(TIM3, TIM_IT_Update );

注：固件库还提供了两个函数用来判断定时器状态以及清除定时器状态标志位的函数 TIM_GetFlagStatus 和 TIM_ClearFlag，他们的作用和前面两个函数的作用类似。只是在 TIM_GetITStatus 函数中会先判断这种中断是否使能，使能了才去判断中断标志位，而TIM_GetFlagStatus 直接用来判断状态标志位。

5.实例学习

  功能; 通过 TIM3 的中断来控制 DS1 的亮灭.
  

**********************定时器基本配置*****************************

```c
#include "timer.h"
#include "led.h"
//通用定时器 3 中断初始化
//这里时钟选择为 APB1 的 2 倍，而 APB1 为 36M
//arr：自动重装值。
//psc：时钟预分频数
//这里使用的是定时器 3!
void TIM3_Int_Init(u16 arr,u16 psc)
{
TIM_TimeBaseInitTypeDef TIM_TimeBaseStructure;
NVIC_InitTypeDef NVIC_InitStructure;
RCC_APB1PeriphClockCmd(RCC_APB1Periph_TIM3, ENABLE); //①时钟 TIM3 使能
//定时器 TIM3 初始化
TIM_TimeBaseStructure.TIM_Period = arr; //设置自动重装载寄存器周期的值
TIM_TimeBaseStructure.TIM_Prescaler =psc; //设置时钟频率除数的预分频值
TIM_TimeBaseStructure.TIM_ClockDivision = TIM_CKD_DIV1; //设置时钟分割
TIM_TimeBaseStructure.TIM_CounterMode = TIM_CounterMode_Up; //TIM 向上计数
TIM_TimeBaseInit(TIM3, &TIM_TimeBaseStructure); //②初始化 TIM3
TIM_ITConfig(TIM3,TIM_IT_Update,ENABLE ); //③允许更新中断
//中断优先级 NVIC 设置
NVIC_InitStructure.NVIC_IRQChannel = TIM3_IRQn; //TIM3 中断
NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 0; //先占优先级 0 级
NVIC_InitStructure.NVIC_IRQChannelSubPriority = 3; //从优先级 3 级
NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE; //IRQ 通道被使能
NVIC_Init(&NVIC_InitStructure); //④初始化 NVIC 寄存器
TIM_Cmd(TIM3, ENABLE); //⑤使能 TIM3
}
```

//定时器 3 中断服务程序⑥


***********************定时器中断服务函数的实现***************************

```c
void TIM3_IRQHandler(void) //TIM3 中断
{
if (TIM_GetITStatus(TIM3, TIM_IT_Update) != RESET) //检查 TIM3 更新中断发生与否
{
TIM_ClearITPendingBit(TIM3, TIM_IT_Update ); //清除 TIM3 更新中断标志
LED1=!LED1;
}
}
```

时钟系统初始化的时候在默认的系统初始化函数 SystemInit 函数里面已经初始化 APB1 的时钟为 2 分频，所以 APB1 的时钟为 36M，而从 STM32 的内部时钟树图得知：当 APB1 的时钟分频数为 1 的时候， TIM2~7 的时钟为 APB1 的时钟，而如果 APB1 的时钟分频数不为 1，那么 TIM2~7 的时钟频率将为 APB1 时钟的两倍。因此， TIM3 的时钟为 72M，再根据我们设计的 arr 和 psc 的值，就可以计算中断时间了。

计算公式如下：
Tout= ((arr+1)*(psc+1))/Tclk；

其中：

Tclk： TIM3 的输入时钟频率（单位为 Mhz）。

Tout： TIM3 溢出时间（单位为 us）。


*****************主函数实现****************

```c
int main(void)
 {	
	delay_init();	    	 //延时函数初始化
	NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);// 设置中断优先级分组2
	LED_Init();		  	//初始化与LED连接的硬件接口
	TIM3_Int_Init(4999,7199);//10Khz的计数频率，计数到5000为500ms  
   	while(1)
	{
		LED0=!LED0;
		delay_ms(200);		   
	}
}
```



参考：
1.正点原子STM32库函数手册

2.[STM32通用定时器](http://www.cnblogs.com/wyuzl/p/6114140.html)

3.[STM32之定时器](http://blog.csdn.net/linzhihan7410/article/details/52166412)

4.[ STM32 定时器浅谈_1](http://blog.csdn.net/xiaoleiacmer/article/details/23684617)
 