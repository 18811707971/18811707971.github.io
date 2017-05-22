---
layout: post
title: STM32学习笔记一一时钟系统
date: 2017-05-22
categories: STM32
tags: [SYSCLK,CLOCK,TIME]
description: STM32 LEARN
---

一、系统架构：

![这里写图片描述](http://img.blog.csdn.net/20170522161543064?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

二、时钟树：

![这里写图片描述](http://img.blog.csdn.net/20170522161218982?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170522160705100?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


1. STM32 有5个时钟源:HSI、HSE、LSI、LSE、PLL。

  ①、HSI是高速内部时钟，RC振荡器，频率为8MHz，精度不高。
  
  ②、HSE是高速外部时钟，可接石英/陶瓷谐振器，或者接外部时钟源，频率范围为4MHz~16MHz。　　

 ③、LSI是低速内部时钟，RC振荡器，频率为40kHz，提供低功耗时钟。

 ④、LSE是低速外部时钟，接频率为32.768kHz的石英晶体。

 ⑤、PLL为锁相环倍频输出，其时钟输入源可选择为HSI/2、HSE或者HSE/2。 倍频可选择为2~16倍，但是其输出频率最大不得超过72MHz。

2. 系统时钟SYSCLK可来源于三个时钟源：    
   
 ①、HSI振荡器时钟
 
 ②、HSE振荡器时钟
  
 ③、PLL时钟
 
3.STM32可以选择一个时钟信号输出到MCO脚(PA8)上，可以选择为PLL 输出的2分频、HSI、HSE、或者系统时钟。这个时钟可以用来给外部其他系统提供时钟源。
  
用户可通过多个预分频器配置AHB总线、高速APB2总线和低速APB1总线的频率。AHB和APB2域的最大频率是72MHZ。APB1域的最大允许频率是36MHZ。SDIO接口的时钟频率固定为HCLK/2。

4.系统时钟SYSCLK

它是供STM32中绝大部分部件工作的时钟源，它可选择为PLL输出、HSI或者HSE，（一般程序中采用PLL倍频到72Mhz）在选择时钟源前注意要判断目标时钟源是否已经稳定振荡。Max=72MHz，它分为2路，1路送给I2S2、I2S3使用的I2S2CLK,I2S3CLK；另外1路通过AHB分频器分频（1/2/4/8/16/64/128/256/512）分频后送给以下8大模块使用：

①送给SDIO使用的SDIOCLK时钟。

②送给FSMC使用的FSMCCLK时钟。

③送给AHB总线、内核、内存和DMA使用的HCLK时钟。

④通过8分频后送给Cortex的系统定时器时钟（SysTick）。

⑤直接送给Cortex的空闲运行时钟FCLK。

⑥送给APB1分频器。APB1分频器可选择1、2、4、8、16分频，其输出一路供APB1外设使用(PCLK1，最大频率36MHz)，另一路送给定时器(Timer2-7)2、3、4倍频器使用。该倍频器可选择1或者2倍频，时钟输出供定时器2、3、4、5、6、7使用。

⑦送给APB2分频器。APB2分频器可选择1、2、4、8、16分频，其输出一路供APB2外设使用(PCLK2，最大频率72MHz)，另一路送给定时器(Timer1、Timer8)1、2倍频器使用。该倍频器可选择1或者2倍频，时钟输出供定时器1和定时器8使用。另外，APB2分频器还有一路输出供ADC分频器使用，分频后得到ADCCLK时钟送给ADC模块使用。ADC分频器可选择为2、4、6、8分频。

⑧2分频后送给SDIO AHB接口使用（HCLK/2）。

5.时钟输出的使能控制

在以上的时钟输出中有很多是带使能控制的，如AHB总线时钟、内核时钟、各种APB1外设、APB2外设等。当需要使用某模块时，必需先使能对应的时钟。需要注意的是定时器的倍频器，当APB的分频为1时，它的倍频值为1，否则它的倍频值就为2。

连接在APB1(低速外设)上的设备有：电源接口、备份接口、CAN、USB、I2C1、I2C2、UART2、UART3、SPI2、窗口看门狗、 Timer2、Timer3、Timer4。注意USB模块虽然需要一个单独的48MHz时钟信号，但它应该不是供USB模块工作的时钟，而只是提供给串行接口引擎(SIE)使用的时钟。USB模块工作的时钟应该是由APB1提供的。

连接在APB2(高速外设)上的设备有：GPIO_A-E、USART1、ADC1、ADC2、ADC3、TIM1、TIM8、SPI1、AFIO；

6.重新配置系统时钟源以及时钟

```c
 void RCC_PLL_Configuration(void)
{
   RCC_DeInit(); /*将外设RCC寄存器重设为缺省值 */
   RCC_HSEConfig(RCC_HSE_ON); 	 /*设置外部高速晶振（HSE） HSE晶振打开(ON)*/

   if(RCC_WaitForHSEStartUp() == SUCCESS) {  /*等待HSE起振,  SUCCESS：HSE晶振稳定且就绪*/ 
   RCC_HCLKConfig(RCC_SYSCLK_Div1);   /*设置AHB时钟（HCLK） RCC_SYSCLK_Div1——AHB时钟 = 系统时*/ 
   RCC_PCLK2Config(RCC_HCLK_Div1);   /* 设置高速AHB时钟（PCLK2)RCC_HCLK_Div1——APB2时钟 = HCLK*/    
   RCC_PCLK1Config(RCC_HCLK_Div2); /*设置低速AHB时钟（PCLK1）RCC_HCLK_Div2——APB1时钟 = HCLK / 2*/     
   FLASH_SetLatency(FLASH_Latency_2);   /*设置FLASH存储器延时时钟周期数FLASH_Latency_2  2延时周期*/  
   FLASH_PrefetchBufferCmd(FLASH_PrefetchBuffer_Enable);  /*选择FLASH预取指缓存的模,预取指缓存使能*/
   RCC_PLLConfig(RCC_PLLSource_HSE_Div2, RCC_PLLMul_3);/*设置PLL时钟源及倍频系数*/   
   RCC_PLLCmd(ENABLE); 	 /*使能PLL */
   while(RCC_GetFlagStatus(RCC_FLAG_PLLRDY) == RESET) ; /*检查指定的RCC标志位(PLL准备好标志)设置与否*/   
   RCC_SYSCLKConfig(RCC_SYSCLKSource_PLLCLK);  /*设置系统时钟（SYSCLK） */ 
   while(RCC_GetSYSCLKSource() != 0x08);     /*0x08：PLL作为系统时钟 */	   
 }
}
```




参考：

1.STM32中文参考手册_V10

2.[STM32的时钟系统 ](http://www.cnblogs.com/wangh0802PositiveANDupward/archive/2012/12/24/2831535.html)

3.[关于STM32时钟系统](http://www.cnblogs.com/beyonne/p/5690013.html)

4.[ STM32 时钟系统 ](http://blog.chinaunix.net/uid-24219701-id-4081961.html)

