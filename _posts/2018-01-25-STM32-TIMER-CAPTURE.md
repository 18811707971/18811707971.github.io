---
layout: post
title: STM32学习笔记一一输入捕获
date: 2018-01-25
categories: STM32
tags: [STM32,TIMER,CAPTURE]
description: STM32 INPUT CAPTURE  STUDY 
---



## **1.概述**

输入捕获模式可以用来**测量脉冲宽度或者测量频率**。STM32 的定时器，除了 TIM6 和 TIM7，其他定时器都有输入捕获功能。 STM32 的输入捕获，简单的说就是通过检测 TIMx_CHx 上的边沿信号，在边沿信号发生跳变（比如上升沿/下降沿）的时候，将当前定时器的值（TIMx_CNT）存放到对应的通道的捕获/比较寄存器（TIMx_CCRx）里面，完成一次捕获。同时还可以配置捕获时是否触发中断/DMA 等。

_____

## **2.思路**

**高电平捕获：**先设置输入捕获为上升沿检测，记录发生上升沿的时候 TIM2_CNT 的值。然后配置捕获信号为下降沿捕获，当下降沿到来时，发生捕获，并记录此时的 TIM2_CNT 值。这样，前后两次 TIM2_CNT 之差，就是高电平的脉宽，同时 TIM2 的计数频率我们是知道的，从而可以计算出高电平脉宽的准确时间。

![这里写图片描述](http://img.blog.csdn.net/20180125204730216?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**工作过程：**通过检测TIMx_CHx上的边沿信号，在边沿信号发生跳变（比如上升沿/下降沿）的时候，将当前定时器的值(TIMx_CNT)存放到对应的捕获/比较寄存器（TIMx_CCRx)里面，完成一次捕获。

![这里写图片描述](http://img.blog.csdn.net/20180125204634387?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

_____

## **3.寄存器介绍**

#### **3.1 TIMx_ARR 和 TIMx_PSC**

这两个寄存器用来设自动重装载值和 TIMx 的时钟分频。
介绍---->[STM32学习笔记一一定时器中断](http://wentao1213.com/2017/07/08/STM32-TIMER-LIB/)

#### **3.2  TIMx_CCMR1**

**捕获/比较模式寄存器:**各位描述如图:

![这里写图片描述](http://img.blog.csdn.net/20180125190347148?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

当在输入捕获模式下使用的时候，对应图 第二行描述，从图中可以看出，TIMx_CCMR1 是针对 2 个通道的配置，低八位[7： 0]用于捕获/比较通道 1 的控制，而高八位[15： 8]则用于捕获/比较通道 2 的控制，因为 TIMx 还有 CCMR2 这个寄存器，可知道CCMR2 是用来控制通道 3 和通道 4。

实验使用TIM2 的捕获/比较通道 1，重点介绍 TIMx_CMMR1 的[7:0]位：

![这里写图片描述](http://img.blog.csdn.net/20180125190917435?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**CC1S[1:0]：**这两个位用于 CCR1 的通道方向配置， 这里我们设置 IC1S[1:0]=01，即
是配置为输入，且 **IC1 映射在 TI1 上**， CC1 即对应 TIMx_CH1。

**IC1PSC[1:0]：**输入捕获 1 预分频器。这里是 1 次边沿就触发 1 次捕获，所以选择 00 就是了。

 **IC1F[3:0]：**输入捕获 1 滤波器。这个用来设置输入采样频率和数字滤波器长度。其中，![这里写图片描述](http://img.blog.csdn.net/20180125205454455?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)是定时器的输入频率（TIMxCLK），一般为 72Mhz，而 ![这里写图片描述](http://img.blog.csdn.net/20180125205537680?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)则是根据 TIMx_CR1 的 CKD[1:0]的设置来确定的，如果 CKD[1:0]设置为 00，那么 ![这里写图片描述](http://img.blog.csdn.net/20180125205615010?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)。 N 值就是滤波长度，举个简单的例子：假设 IC1F[3:0]=0011，并设置 IC1 映射到通道 1 上，且为上升沿触发，那么在捕获到上升沿的时候，再以 ![这里写图片描述](http://img.blog.csdn.net/20180125205715972?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)的频率，连续采样到 8 次通道 1 的电平，如果都是高电平，则说明确实是一个有效的触发，就会触发输入捕获中断（如果开启了的话）。这样可以滤除那些高电平脉宽低于 8 个采样周期的脉冲信号，从而达到滤波的效果。这里，我们不做滤波处理，所以设置 IC1F[3:0]=0000，只要采集到上升沿，就触发捕获。

#### 3.3  **TIMx_CCER：**

捕获/比较使能寄存器，此处使用到这个寄存器的最低 2 位， CC1E 和 CC1P 位。如下图：

![这里写图片描述](http://img.blog.csdn.net/20180125205901558?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### 3.4 **TIMx_DIER**：

DMA/中断使能寄存器。

![这里写图片描述](http://img.blog.csdn.net/20180125210141939?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

我们同样仅关心它的第 0 位， 该位是更新中断允许位， 当定时器的更新中断， 该位要设置为 1，来允许由于更新事件所产生的中断。

#### 3.5 **TIMx_CCR1：**

捕获/比较寄存器 1。该寄存器用来存储捕获发生时， TIMx_CNT的值，我们从 TIMx_CCR1 就可以读出通道 1 捕获发生时刻的 TIMx_CNT 值，通过两次捕获（一次上升沿捕获，一次下降沿捕获）的差值，就可以计算出高电平脉冲的宽度。

_____

## 4. **配置步骤**

#### 4.1 **开启 TIM2 时钟，配置 PA0 为下拉输入。**

要使用 TIM2，我们必须先开启 TIM2 的时钟。这里我们还要配置 PA0 为下拉输入，因为
我们要捕获 TIM2_CH1 上面的高电平脉宽，而 TIM2_CH1 是连接在 PA0 上面的。

```c
RCC_APB1PeriphClockCmd(RCC_APB1Periph_TIM2, ENABLE); //使能 TIM2 时钟
RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE); //使能 GPIOA 时钟

```

#### 4.2 **初始化 TIM2， 设置 TIM2 的 ARR 和 PSC**

在开启了 TIM2 的时钟之后，我们要设置 ARR 和 PSC 两个寄存器的值来设置**输入捕获的自动重装载值和计数频率**。 这在库函数中是通过 TIM_TimeBaseInit 函数实现的。

```c
TIM_TimeBaseInitTypeDef TIM_TimeBaseStructure;

TIM_TimeBaseStructure.TIM_Period = arr; //设定计数器自动重装值
TIM_TimeBaseStructure.TIM_Prescaler =psc; //设置预分频值
TIM_TimeBaseStructure.TIM_ClockDivision = TIM_CKD_DIV1; // TDTS = Tck_tim
TIM_TimeBaseStructure.TIM_CounterMode = TIM_CounterMode_Up; //TIM 向上计数模式
TIM_TimeBaseInit(TIM2, &TIM_TimeBaseStructure); //根据指定的参数初始化 Tim2

```

#### 4.3  **设置 TIM2 的输入比较参数，开启输入捕获**

输入比较参数的设置包括**映射关系，滤波,分频以及捕获方式**等。这里我们需要设置通道 1为输入模式，且 IC1 映射到 TI1(通道 1)上面，并且不使用滤波（提高响应速度）器，上升沿捕获。库函数是通过 TIM_ICInit 函数来初始化输入比较参数的：

```c
void TIM_ICInit(TIM_TypeDef* TIMx, TIM_ICInitTypeDef* TIM_ICInitStruct)；

```
**参数设置结构体 TIM_ICInitTypeDef 的定义：**

```c

typedef struct
{
uint16_t TIM_Channel;
uint16_t TIM_ICPolarity;
uint16_t TIM_ICSelection;
uint16_t TIM_ICPrescaler;
uint16_t TIM_ICFilter;
} TIM_ICInitTypeDef;

```

**参数 TIM_Channel :**用来设置通道。我们设置为通道 1，为 TIM_Channel_1。

**参 数 TIM_ICPolarity :**是 用 来 设 置 输 入 信 号 的 有 效 捕 获 极 性 ， 这 里 我 们 设 置 为
TIM_ICPolarity_Rising，上升沿捕获。同时库函数还提供了单独设置通道 1 捕获极性的函数为：

TIM_OC1PolarityConfig(TIM2,TIM_ICPolarity_Falling)

这表示通道 1 为上升沿捕获，同时对于其他三个通道也有一个类似的函数，使用的时候一定要分清楚使用的是哪个通道该调用哪个函数，格式为 TIM_OCxPolarityConfig()。

**参数 TIM_ICSelection:** 是用来设置映射关系，我们配置 IC1 直接映射在 TI1 上，选择
TIM_ICSelection_DirectTI。

**参 数 TIM_ICPrescaler:** 用 来 设 置 输 入 捕 获 分 频 系 数 ， 我 们 这 里 不 分 频 ， 所 以 选 中TIM_ICPSC_DIV1，此外，还有 2,4,8 分频可选。

**参数 TIM_ICFilter：** 设置滤波器长度，这里我们不使用滤波器，所以设置为 0。

**配置代码：**

```c
TIM_ICInitTypeDef TIM2_ICInitStructure;

TIM5_ICInitStructure.TIM_Channel = TIM_Channel_1; //选择输入端 IC1 映射到 TI1 上
TIM5_ICInitStructure.TIM_ICPolarity = TIM_ICPolarity_Rising; //上升沿捕获
TIM5_ICInitStructure.TIM_ICSelection = TIM_ICSelection_DirectTI; //映射到 TI1 上
TIM5_ICInitStructure.TIM_ICPrescaler = TIM_ICPSC_DIV1; //配置输入分频,不分频
TIM5_ICInitStructure.TIM_ICFilter = 0x00;//IC1F=0000 配置输入滤波器 不滤波
TIM_ICInit(TIM2, &TIM2_ICInitStructure);
```

#### 4.4 **使能捕获和更新中断（设置 TIM2 的 DIER 寄存器）**

因为我们要捕获的是高电平信号的脉宽，所以，第一次捕获是上升沿，第二次捕获时下降
沿，必须在捕获上升沿之后， 设置捕获边沿为下降沿，同时，如果脉宽比较长，那么定时器就会溢出，对溢出必须做处理，否则结果就不准了。这两件事，我们都在中断里面做，所以必须
开启捕获中断和更新中断。这里我们使用定时器的开中断函数 TIM_ITConfig 即可使能捕获和更新中断：

```c
TIM_ITConfig( TIM2,TIM_IT_Update|TIM_IT_CC1,ENABLE);//允许更新中断和捕获中断
```

#### 4.5 **设置中断分组，编写中断服务函数**

设置中断分组主要是通过函数 NVIC_Init()来完成。分组完成后，还需要**在中断函数里面完成数据处理和捕获设置等关键操作**，从而实现高电平脉宽统计。在中断服务函数里面，**在中断开始的时候要进行中断类型判断，在中断结束的时候要清除中断标志位**。使用到的函数分别为 :
TIM_GetITStatus()函数和 TIM_ClearITPendingBit()函数。

```c
if (TIM_GetITStatus(TIM2, TIM_IT_Update) != RESET){}//判断是否为更新中断
if (TIM_GetITStatus(TIM2, TIM_IT_CC1) != RESET){}//判断是否发生捕获事件
TIM_ClearITPendingBit(TIM2, TIM_IT_CC1|TIM_IT_Update);//清除中断和捕获标志位

```

#### 4.6 **使能定时器（设置 TIM2 的 CR1 寄存器）**

最后，必须打开定时器的计数器开关， 启动 TIM5 的计数器，开始输入捕获TIM_Cmd(TIM2,ENABLE ); //使能定时器 2通过以上 6 步设置，定时器 2 的通道 1 就可以开始输入捕获了。

_____

## 5.**实现代码**

#### 5.1 **定时器 2 通道 1 输入捕获配置**：

```c

TIM_ICInitTypeDef TIM2_ICInitStructure;

void TIM2_Cap_Init(u16 arr,u16 psc)
{
GPIO_InitTypeDef GPIO_InitStructure;
TIM_TimeBaseInitTypeDef TIM_TimeBaseStructure;
NVIC_InitTypeDef NVIC_InitStructure;

RCC_APB1PeriphClockCmd(RCC_APB1Periph_TIM2, ENABLE); //使能 TIM2 时钟
RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE); //使能 GPIOA 时钟

GPIO_InitStructure.GPIO_Pin = GPIO_Pin_0; //PA0 清除之前设置
GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPD; //PA0 输入
GPIO_Init(GPIOA, &GPIO_InitStructure);
GPIO_ResetBits(GPIOA,GPIO_Pin_0); //PA0 下拉

//初始化定时器 2 TIM2
TIM_TimeBaseStructure.TIM_Period = arr; //设定计数器自动重装值
TIM_TimeBaseStructure.TIM_Prescaler =psc; //预分频器
TIM_TimeBaseStructure.TIM_ClockDivision = TIM_CKD_DIV1; //设置时钟分割
TIM_TimeBaseStructure.TIM_CounterMode = TIM_CounterMode_Up; //向上计数
TIM_TimeBaseInit(TIM2, &TIM_TimeBaseStructure); //初始化 TIMx 的时间基数单位

//初始化 TIM2 输入捕获参数
TIM2_ICInitStructure.TIM_Channel = TIM_Channel_1; //选择输入端 IC1 映射到 TI1 上
TIM2_ICInitStructure.TIM_ICPolarity = TIM_ICPolarity_Rising; //上升沿捕获
TIM2_ICInitStructure.TIM_ICSelection = TIM_ICSelection_DirectTI; //映射到 TI1 上
TIM2_ICInitStructure.TIM_ICPrescaler = TIM_ICPSC_DIV1; //配置输入分频,不分频
TIM2_ICInitStructure.TIM_ICFilter = 0x00;//IC1F=0000 配置输入滤波器 不滤波
TIM_ICInit(TIM2, &TIM2_ICInitStructure);

//中断分组初始化
NVIC_InitStructure.NVIC_IRQChannel = TIM2_IRQn; //TIM2 中断
NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 2; //先占优先级 2 级
NVIC_InitStructure.NVIC_IRQChannelSubPriority = 0; //从优先级 0 级
NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE; //IRQ 通道被使能
NVIC_Init(&NVIC_InitStructure); //初始化外设 NVIC 寄存器
TIM_ITConfig(TIM2,TIM_IT_Update|TIM_IT_CC1,ENABLE);

//允许更新中断 CC1IE 捕获中断
TIM_Cmd(TIM2,ENABLE ); //使能定时器 2

}

```

#### 5.2 **定时器 5 中断服务程序**

```c

u8 TIM2CH1_CAPTURE_STA=0; //输入捕获状态
u16 TIM2CH1_CAPTURE_VAL;//输入捕获值
//定时器 5 中断服务程序
void TIM2_IRQHandler(void)
{
  if((TIM2CH1_CAPTURE_STA&0X80)==0)//还未成功捕获
   {
     if (TIM_GetITStatus(TIM2, TIM_IT_Update) != RESET)
       {
         if(TIM2CH1_CAPTURE_STA&0X40)//已经捕获到高电平了
           {
             if((TIM2CH1_CAPTURE_STA&0X3F)==0X3F)//高电平太长了
                {
                  TIM2CH1_CAPTURE_STA|=0X80;//标记成功捕获了一次
                  TIM2CH1_CAPTURE_VAL=0XFFFF;
                 }

  else 
       TIM2CH1_CAPTURE_STA++;
          }
     }
 if (TIM_GetITStatus(TIM2, TIM_IT_CC1) != RESET)//捕获 1 发生捕获事件
  {
    if(TIM2CH1_CAPTURE_STA&0X40) //捕获到一个下降沿
     {
	  TIM2CH1_CAPTURE_STA|=0X80; //标记成功捕获到一次上升沿
	  TIM2CH1_CAPTURE_VAL=TIM_GetCapture1(TIM2);
	  TIM_OC1PolarityConfig(TIM2,TIM_ICPolarity_Rising);
	  //CC1P=0 设置为上升沿捕获
       }
  else //还未开始,第一次捕获上升沿
   {
	  TIM2CH1_CAPTURE_STA=0; //清空
	  TIM2CH1_CAPTURE_VAL=0;
	  TIM_SetCounter(TIM2,0);
	  TIM2CH1_CAPTURE_STA|=0X40; //标记捕获到了上升沿
	  TIM_OC1PolarityConfig(TIM2,TIM_ICPolarity_Falling);
        //CC1P=1 设置为下降沿捕获
    }
  }
}
TIM_ClearITPendingBit(TIM2, TIM_IT_CC1|TIM_IT_Update); //清除中断标志位
}

```

注：

**TIM2CH1_CAPTURE_STA：**是用来记录捕获状态，把它当成一个寄存器那样
来使用。 TIM2CH1_CAPTURE_STA 各位描述下表 所示：

![这里写图片描述](http://img.blog.csdn.net/20180125212420336?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**TIM2CH1_CAPTURE_VAL：**则用来记录捕获到下降沿的时候， TIM2_CNT的值。

**捕获高电平脉宽的思路**：

![这里写图片描述](http://img.blog.csdn.net/20180125213256457?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

首先，设置 TIM2_CH1 捕获上升沿，这在TIM2_Cap_Init 函数执行的时候就设置好了，然后等待上升沿中捕获断到来， 当捕获到上升沿中断， 此时如果 TIM2CH1_CAPTURE_STA 的第 6 位为 0， 则表示还没有捕获到新的上升沿，就先把 TIM2CH1_CAPTURE_STA、 TIM2CH1_CAPTURE_VAL 和 TIM2->CNT 等清零，然后再设置 TIM2CH1_CAPTURE_STA 的第 6 位为 1， 标记捕获到高电平， 最后设置为下降沿捕获，等 待 下 降 沿 到 来 。 如 果 等 待 下 降 沿 到 来 期 间 ， 定 时 器 发 生 了 溢 出 ， 就 在TIM2CH1_CAPTURE_STA 里面对溢出次数进行计数，当最大溢出次数来到的时候，就强制标记 捕 获 完 成 （ 虽 然 此 时 还 没 有 捕 获 到 下 降 沿 ）。 当 下 降 沿 到 来 的 时 候 ， 先 设 置TIM2CH1_CAPTURE_STA 的第 7 位为 1，标记成功捕获一次高电平，然后读取此时的定时器的捕获值到 TIM2CH1_CAPTURE_VAL 里面，最后设置为上升沿捕获，回到初始状态。这样，我们就完成一次高电平捕获了，只要 TIM2CH1_CAPTURE_STA 的第 7 位一直为 1，那么就不会进行第二次捕获，我们在 main函数处理完捕获数据后，将 TIM2CH1_CAPTURE_STA置零，就可以开启第二次捕获。

#### 5.3 **主函数实现**

```c

extern u8 TIM2CH1_CAPTURE_STA; //输入捕获状态
extern u16 TIM2CH1_CAPTURE_VAL;//输入捕获值
int main(void)
{
    u32 temp=0;
    NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);//设置 NVIC 中断分组 2:2 位抢占优
先级， 2 位响应优先级
   delay_init(); //延时函数初始化
   uart_init(9600); //串口初始化为 9600
   LED_Init(); //初始化与 LED 连接的硬件接口
   TIM1_PWM_Init(899,0); //不分频。 PWM 频率=72000/(899+1)=80Khz
   TIM2_Cap_Init(0XFFFF,72-1); //以 1Mhz 的频率计数
  while(1)
   {
    delay_ms(10);
    TIM_SetCompare1(TIM1,TIM_GetCapture1(TIM1)+1);
    if(TIM_GetCapture1(TIM1)==300)TIM_SetCompare1(TIM1,0);
    if(TIM2CH1_CAPTURE_STA&0X80)//成功捕获到了一次高电平
     {
      temp=TIM2CH1_CAPTURE_STA&0X3F;
      temp*=65536; //溢出时间总和
      temp+=TIM2CH1_CAPTURE_VAL; //得到总的高电平时间
      printf("HIGH:%d us\r\n",temp); //打印总的高点平时间
      TIM2CH1_CAPTURE_STA=0; //开启下一次捕获
     }
   }
}

```


______


**参考：**

1.正点原子库函数版本实现