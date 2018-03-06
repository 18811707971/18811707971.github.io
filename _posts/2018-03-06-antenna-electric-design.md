---
layout: post
title: 2.4G天线设计指南
date: 2018-03-06
categories: 电子之路
tags: [antenna,PCB]
description: PCB设计
---




转载自------>[非常实用: 2.4G天线设计指南(赛普拉斯工程师力作)](http://mp.weixin.qq.com/s/fv36Ze1BjZtYcn5UKWewoA)
微信公众号：<<射频百花潭>>

______

本文章使用简单的术语介绍了天线的设计情况，并推荐了两款经过赛普拉斯测试的低成本PCB天线。这些PCB天线能够与赛普拉斯PRoC™和PSoC®系列中的低功耗蓝牙(BLE)解决方案配合使用。为了使性能最佳，PRoC BLE和PSoC4 BLE2.4GHz射频必须与其天线正确匹配。本应用笔记中最后部分介绍了如何在最终产品中调试天线。

____

## **1.简介**

　　天线是无线系统中的关键组件，它负责发送和接收来自空中的电磁辐射。为低成本、消费广的应用设计天线，并将其集成到手提产品中是大多数原装设备制造商(OEM)正在面对的挑战。终端客户从某个RF产品(如电量有限的硬币型电池)获得的无线射程主要取决于天线的设计、塑料外壳以及良好的PCB布局。

　　对于芯片和电源相同但布局和天线设计实践不同的系统，它们的RF(射频)范围变化超过50%也是正常的。本应用笔记介绍了最佳实践、布局指南以及天线调试程序，并给出了使用给定电量所获取的最宽波段。
![这里写图片描述](http://img.blog.csdn.net/20180305223950576?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

设计优良的天线可以扩大无线产品的工作范围。从无线模块发送的能量越大，在已给的数据包错误率(PER)以及接收器灵敏度固定的条件下，传输的距离也越大。另外，天线还有其他不太明显的优点，例如：在某个给定的范围内，设计优良的天线能够发射更多的能量，从而可以提高错误容限化(由干扰或噪声引起的)。同样，接收端良好的调试天线和Balun(平衡器)可以在极小的辐射条件下工作。

　　最佳天线可以降低PER，并提高通信质量。PER越低，发生重新传输的次数也越少，从而可以节省电池电量。

____

## **2.天线原理**

**天线一般指的是裸露在空间内的导体**。**该导体的长度与信号波长成特定比例或整数倍时，它可作为天线使用**。因为提供给天线的电能被发射到空间内，所以该条件被称为“谐振”。

![这里写图片描述](http://img.blog.csdn.net/20180305224244188?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

如图2所示，导体的波长为λ/2，其中λ为电信号的波长。信号发生器通过一根传输线(也称为天线馈电)在天线的中心点为其供电。按照这个长度，**将在整个导线上形成电压和电流驻波**，如图2所示。

　　**输入到天线的电能被转换为电磁辐射，并以相应的频率辐射到空中。该天线由天线馈电供电，馈电的特性阻抗为50Ω，并且辐射到特性阻抗为377Ω的空间中。**

　　因此，对于天线的几何形状，有两个非常重要的事项需要注意：

　　*1.天线长度*

　　*2.天线馈电*

　　**长度为λ/2的天线(如图2所示)被称为偶极天线。**但在印刷电路板中，大多作为天线使用的导体长度仅为**λ/4**，但仍具有相同的性能。请参见图3。

　　**通过在导体下方一定距离的位置上放置接地层，可以创建与导体长度相同的镜像(λ/4)**。被组合在一起时，这些引脚作为偶极天线使用。这种天线被称为四分之一波长(λ/4)天线。PCB上几乎所有的天线都按铜制接地层上四分之一波长的尺寸实现。请注意，该信号现在是单端馈电，同时接地层作为返回路径使用。

![这里写图片描述](http://img.blog.csdn.net/20180305224627938?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

对于大多数PCB中使用的四分之一波长天线，需要特别注意：

　　**1. 天线长度**

　　**2. 天线馈电**

　　**3. 接地层和回流路径的形状和尺寸**

_____

## **3.天线类型**

　　如前部分所述，在自由空间中裸露的波长为λ/4的所有导体被放在一个接地层上，并为其提供合适的电压，那么该导体可以作为一个天线使用。根据不同的波长，天线可能与汽车的FM天线一样长，也可能与信号浮标上的走线一样短。对于2.4GHz的应用，大部分PCB天线都属于下面的类型：

#### **3.1.导线天线**

这是在PCB上延长到自由空间中的一段导线，它的长度为λ/4，并被放置在接地层上。这种天线是由**50Ω阻抗的传输线供电的**。通常，该导线天线提供的性能和辐射范围最好。该导线可以是**直线、螺旋或是回路**的。它是一个三维(3D)的结构，其中天线高出PCB4-5mm，并伸出到空间内。

![这里写图片描述](http://img.blog.csdn.net/20180305224929658?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#### **3.2. PCB天线**

它是PCB上的一根PCB走线，并且可以将其画成**直线形走线、反转的F形走线、蛇形或圆形走线**等。在一个PCB天线中，与导线天线不同的是，该天线没有被露到外部空间内，而是在同一个PCB层上以二维(2D)结构形式存在;请参见图5。

　　当裸露到空间外的3D天线被放置到PCB层上作为2D的PCB走线时，必须遵循一定的指南。一般情况下，与导线天线相比，它需要的PCB空间更大，效率也低，但成本低，并且可以给BLE应用提供可接收的无线距离。

![这里写图片描述](http://img.blog.csdn.net/20180305225334531?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#### **3.3.芯片天线**

这是一种带有导体的天线，天线和导体都被组装在小型的IC封装中。当天线被封装在很小的尺寸内时，它会变得很有优势。天线USB的纳米收发器等应用会使用这种天线，当PCB上没有足够的空间来布局PCB天线时。有关芯片天线的信息，请参见下图。想要了解各种天线的尺寸对比，请参见表4。

![这里写图片描述](http://img.blog.csdn.net/20180305225436839?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## **4.天线的选择**

　　天线的选择取决于其应用、可用**电路板的尺寸、成本、辐射范围以及方向性**等因素。

　　蓝牙低功耗(BLE)应用(比如无线鼠标)只需要10英寸的辐射范围和几kbps的数据速率。然而，对于采用语音识别的遥控应用，则需要一个室内设置天线，该天线的辐射范围大概为10-15英寸，并且其数据速率为64kbps。

　　对于无线音频应用，需要分集天线。分集天线是指：**将两根天线放置在同一个PCB上，这样可以保证最少有一根天线始终能够接收某些辐射，而另一根天线则可能会因反射和多路径衰弱而被遮住。**在传输实时音频数据并需要较高的吞吐量而不会丢失数据包的情况下，需要用到分集天线。也可以将它用在信标应用中，进行室内定位。

___

## **5.天线参数**

　　下面部分提供了天线性能的某些关键参数。

　　**§ 回波损耗：**天线的回波损耗表示天线如何与阻抗为50Ω的传输线(TL)实现匹配，将其显示为图7中的信号馈送。通常，这个TL的阻抗值为50Ω，但也可以是其他数值。对于工业标准，商业天线和它的测试设备的电阻为50Ω，因此建议您最好使用该值。

　　回波损耗指出：由于不匹配，天线反射的入射功率大小(公式1)。一个理想的天线会发射全部功率，不会产生任何反射。

　　如果该回波损耗是无限的，则认为天线与TL完全匹配，如图7所示。S11是回波损耗的倒数，其单位为dB。根据经验估计，如果回波损耗≥10dB(既S11≤–10dB)，便足够大。表1显示了天线的回波损耗(dB)与反射功率(%)。回波损耗为10dB时，表示90%的入射功率被传给天线以进行发射。

![这里写图片描述](http://img.blog.csdn.net/20180306135801009?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

　**§ 带宽：**是指天线的频率响应。它表示在采用的整个频带上，即在BLE应用的2.40GHz至2.48GHz的范围内，该天线与50Ω的传输线如何相互匹配。

![这里写图片描述](http://img.blog.csdn.net/20180306140012696?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

如图8所示，在2.33GHz至2.55GHz的带宽上，回波损耗大于10dB。因此，采用的带宽为200MHz左右。

　　**§ 辐射效率：**指的是非反射功耗中的一部分(请参见图7)被消耗为天线中的热量。产生热量是由于FR4基板中的介电损耗以及铜线中的导体损耗造成的。该信息作为辐射效率。辐射效率为100%时，全部非反射的功耗都被发射到空间内。对于小型的PCB外形因素，热耗最小。

　　**§ 辐射图型：**该图型表示辐射的方向性，即表示在哪个方向上的辐射更大，哪个方向上的辐射更小。这有助于在应用中准确地确定天线的方向。

　　无方向性天线可以按与轴线相垂直的平面上所有方向进行等效发射。但大多数天线都达不到这个理想的性能。欲了解详细说明，请参看图9中所示的PCB天线的辐射图。每个数据点都代表RF场强，可以通过接收器中用于接收信号强度的指示器(RSSI)进行测量。正如所料的情况，获得的轮廓图像并不是圆形的，因为该天线不是各向同性的。

![这里写图片描述](http://img.blog.csdn.net/20180306150443638?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**§ 增益：**增益提供了所采用方向的辐射与各向同性天线(即可从所有方向进行发射)进行对比的信息。增益单位为dBi，即表示在与一个理想的无方向性天线进行对比时辐射的场强。

_____

## **6.赛普拉斯PRoC/PSoCBLE的天线**

　　设计BLE的一个受限因素便是需要在一个紧凑的空间中集成天线，并且最多只能使用两个外部组件进行调整。调试过程需要确保在某个频带内进行传输时应尽可能保持传入天线的能量。这便意味着，所需带段中的**回波损耗要大于10dB**。当天线的输入阻抗为50Ω，并且芯片输出阻抗为50Ω时，天线收到的能量最大。天线作为接收端时，也要满足上述条件。对于天线来说，它的调整过程能够确保天线的阻抗等于50Ω。对于芯片来说，Balun(平衡器)调整过程可保证电阻接近50Ω。

　　PRoC/PSoCBLE器件中集成平衡器的阻抗并不等于50Ω，所以可能需要通过两个组件对其进行调整。对于射频范围较小的低数据速率应用，赛普拉斯所推荐的PCB天线不需要通过任何组件来调整天线。

　　对于高数据速率的应用(如:通过遥控器的声音识别应用)，建议至少需要使用四个组件进行匹配网络。其中**两个用于平衡器调整，其余两个用于天线调整**。可使用其中两个进行调整过程，剩下的两个保持待用状态。

　　此外，赛普拉斯PRoC/PSoC还提供了不同的应用，如室内定位、智能家居、智能电器以及传感器集线器。这些应用可能不受空间的限制，因此，可以针对射频范围和射频方向图等因素为这些应用设计更好的天线。导线天线非常适合非穿戴式但位置固定的应用。

　　很多应用直接在其主PCB中嵌入了赛普拉斯的该类模块，用以实现无线连接。这些应用要求通过FCC的低成本小型模块。这时可以使用满足这些要求的芯片天线。

　　虽然使用2.4GHz频段的应用很多，但大多数BLE应用仅使用了下面介绍的双PCB天线。赛普拉斯推荐使用两种专有的PCB天线、**蛇形倒F天线(MIFA)和倒F形天线(IFA)**，它们是针对BLE应用而特性化和广泛模拟的天线。特别是MIFA，可将它用于几乎所有的BLE应用中。

　　但您也可以从本文档中选出任何一款符合您的应用要求的天线。

_____

## **7.赛普拉斯专有的PCB天线**

　　赛普拉斯推荐使用IFA和MIFA这两种PCB天线。BLE应用中的低速率和典型的辐射围范使这两种天线特别有用。这些天线既便宜又容易设计，这是因为它们是PCB的组成部分，并且能够在150至250MHz的频段范围内提供良好的性能。

　　建议将MIFA天线使用在仅需极小的PCB空间的应用中，如无线鼠标、键盘、演示机等等。对于IFA天线，建议将其应用在要求天线一侧的尺寸远小于另一侧的尺寸的应用中，如心率监视器。大多数BLE应用中使用的是MIFA天线。下面各节将详细介绍每种天线的信息。

　　**蛇形倒F天线(MIFA)**

　　MIFA是一种普通的天线，被广泛地使用在各个人机接口设备(HID)中，因为它占用的PCB空间较小。因此赛普拉斯已设计出一种结实的MIFA天线，而它能在较小的波形系数中提供优越的性能。该天线的尺寸为7.2mm×11.1mm(相当于284密耳×437密耳)，因此它很适合于各种HID的应用，例如无线鼠标、键盘或演示机等。图10显示的是所推荐的MIFA天线的详细布局，其中包含了双层PCB的顶层和底层。这种天线的迹线宽度均为20密耳。“W”的值是可改变的主要参数，它取决于PCB堆栈间隔，它表示RF走线(传输线)的宽度。

![这里写图片描述](http://img.blog.csdn.net/20180306151210817?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

注意：有关用于1.6mm厚的FR4PCB上MIFA天线的Gerber和.brd文档，请访问网址www.cypress.com/go/AN91445上的AN91445.zip文档。

____

##  **8.天线馈电的考量**

表2显示的是双层FR4PCB顶层和底层间厚度的“W”值(相应的介电常数为4.3)。顶层包含了天线走线;而底层则是包含了固态RF接地层的下一层。底层的余下PCB空间可以作为信号接地层使用(针对PRoC/PSoC和其他电路)。图11显示的是典型的双层PCB厚度的“W”值。

表2.FR4PCB的“W”值：天线层与相邻射频的接地层间的厚度。

![这里写图片描述](http://img.blog.csdn.net/20180306151854516?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

对于为天线馈电更短的PCB走线，这样的宽度要求是比较宽松的。要确保天线走线的宽度和天线馈电接点的宽度相同。在图12展示的情况中，天线馈电的走线宽度不是表2中所规定的宽度。

![这里写图片描述](http://img.blog.csdn.net/20180306152049164?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

但如果传输线较长(从匹配网络至天线或回到PRoC/PSoC的ANT引脚的线的长约为1cm)，那么赛普拉斯建议使用底层上宽度特定的“W”的传输线(TLine)类型(该线被放置在PCB上)作为馈源。

图13表示的是MIFA的S11。该MIFA的带宽(S11≤–10dB)范围为2.44GHz±230MHz。因此，在2.44GHz±230MHz的范围内，天线的反射小于或等于10%，这样将够用于BLE应用。

![这里写图片描述](http://img.blog.csdn.net/20180306152254621?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

图13.MIFA的S11(回波损耗=–S11)

图14显示的是MIFA在2.44GHz频率时完整的3D辐射增益图。在给自定义应用设置MIFA天线时，该信息非常有用，有助于在需要的方向上得到最大的辐射。在上面的图中：MIFA被放置在XY平面上，Z轴方向与它垂直。

![这里写图片描述](http://img.blog.csdn.net/20180306152419732?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

提供该辐射图，可以知道：最大的辐射出现在与X轴成30°角度的圆锥空间内。这是因为MIFA在XY平面上不能保持正横或正竖的方向。MIFA竖向部分和末梢和均参加了辐射，并形成一个倾斜的辐射图。

**天线长度的考量**

根据PCB的不同厚度，需要调整MIFA天线的长度，这样才能调整天线辐射的阻抗和频率选择。根据不同的电路板厚度，赛普拉斯提供了下面各天线长度。

![这里写图片描述](http://img.blog.csdn.net/20180306152712024?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20180306152759344?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

图15显示的是两种适用于两个不同电路板厚度的MIFA天线。设计人员根据特定的电路板厚度进行调整MIFA天线的长度时，请参考表3。

**倒F天线(IFA)**

对于天线的尺寸有一定限制条件的应用中(例如心率监视器)，推荐您选用这种IFA。图16显示所推荐的IFA的详细布局，其中包含了双层PCB中的顶层和底层。其走线宽度约为24mm。

对于厚度为1.6mm的FR4PCB，IFA的尺寸被设计为4mm×20.5mm(157.5mils×807mils)。与MIFA相比，IFA的宽高比(宽度和高度的比例)更大。

![这里写图片描述](http://img.blog.csdn.net/20180306153043283?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20180306153108031?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

注意：有关1.6mm厚的FR4PCB的Gerber文件(和.brd文件)，请参考www.cypress.com/go/AN91445网页上的AN91445.zip文件。

馈电走线的宽度“W”受产品中PCB堆栈的影响。表4根据顶层(天线层)和底层(相邻射频接地层)间不同的PCB厚度给FR4基板提供了相应的“W”值(相应的介电常数为4.3)。顶层包含天线迹线;而底层则为其紧挨的包含了固态RF接地层的下一层。底层上剩余的PCB空间可以作为信号接地平面使用(对于PRoC/PSoC和其他电路)。图17将典型的双层PCB的“PCB厚度”的概念与“F”值联系起来。

![这里写图片描述](http://img.blog.csdn.net/20180306153423555?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

对于小于3mm的短走线，天线馈电厚度是可以调整的。天线馈电的厚度可以与天线走线厚度相同，请参见图12。

IFA在220MHz的带宽上(S11≤–10dB)的频率约为2.44GHz，如图18中所示。

![这里写图片描述](http://img.blog.csdn.net/20180306153523133?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

图19显示的是IFA在XY平面上的定性辐射图。在为客户应用设置IFA天线时，该信息非常有用，有助于在需要的方向上得到最大的辐射。为了便于观察，图中只显示了定性辐射的方向。

![这里写图片描述](http://img.blog.csdn.net/20180306153640462?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**芯片天线**

对于PCB尺寸非常小的利基应用(例如蓝牙收发器)，芯片天线不失为一种很好的办法(图20)。它们是现成的天线，占用的PCB空间最小，并且能够提供较好的性能。但芯片天线增加了材料清单(BOM)，并需要装配费用。因为它要求订购和装配外部组件。通常，芯片天线的价格约为10-50美分，具体价格取决于尺寸和性能。

![这里写图片描述](http://img.blog.csdn.net/20180306153800735?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

使用芯片天线时，也应考虑另一个关键因素：**它受辐射接地面积的影响**。所以，必须遵循厂家对接地面积的推荐。与PCB天线不同，芯片天线不能通过改变天线长度来调整。另外需要一个匹配网络才能调整该天线，因此会增加更多的材料清单。

赛普拉斯只推荐将芯片天线使用在要求PCB空间极小的特定应用中，例如：Nano蓝牙收发器。对于这样的应用，赛普拉斯建议使用具有约翰森技术的2450AT18B100E芯片天线，其尺寸为63milx126mil。而对于大部分应用，则建议使用PCB天线，如MIFA或IFA。这些小外形(占用空间小)天线不但廉价，而且提供的性能非常卓越。图21和图22显示的是具有约翰森技术的2450AT42B100E芯片天线布局指南。其尺寸为118milx196mil。更多有关这些天线的详细指南，请参考它们相关的网址。

![这里写图片描述](http://img.blog.csdn.net/20180306153939112?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

该布局也显示了50Ω的馈电传输线以及与其相匹配的组件。馈电传输线的宽度取决于电路板的厚度。表4中指定了准确的电路板厚度。

![这里写图片描述](http://img.blog.csdn.net/20180306154017785?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

芯片天线的性能是由接地层决定。一般来说，它们需要更多的接地面积和更大的空间。如上图所示，对于2450AT42B100E的天线，最小的接地距离为0.8mm。该间距为2-3mm时，观察到的s11会更加明显。

芯片天线不一定是严格等向性的。辐射存在某些优先的方向。根据Gnd间距和塑料配件，辐射最大的方向也不一样。有关约翰森技术的芯片天线(2450AT42B100E)的常见辐射方向，请参见图23。

![这里写图片描述](http://img.blog.csdn.net/20180306154323172?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

赛普拉斯只推荐将**芯片天线使用在要求PCB空间极小的特定应用中**，例如：Nano蓝牙收发器或超小的模块。对于这些应用，赛普拉斯也建议使用含有约翰森技术的2450AT42B100E芯片天线，因为与2450AT18B100E相比，它的尺寸更大，射频性能更好，并且需要较小的Gnd间距。介绍翰森技术天线的内容仅供参考。更多有关2.4GHz芯片天线的信息，请向各供应商索取，如Murata、Vishay等。

对于大部分应用，建议使用PCB天线，如MIFA或IFA。这些外形小(占用空间小)的天线虽然廉价，但提供的性能非常卓越。

**导线天线**

导线天线是传统的旧式天线，将一条铝线或一根四分之一波长的回形针固定在PCB上面便构成了这种天线，该**铝线或回形针按螺旋形状**安装在PCB上，然后在距离PCB5-6mm的位置与该层并行。

不用再做介绍，由于它们作为3D天线裸露在空气中，所以它们的射频性能非常好。这种天线具有最好的信号范围和最等向的辐射方向图。导线天线的无线覆盖范围可超过100英尺。

对于要求小外形天线的BLE应用，不建议使用这种天线，因为它会占用较大空间和垂直高度。但如果有足够的空间，那么这种天线可实现最佳的射频范围、方向性以及辐射方向图等性能。

![这里写图片描述](http://img.blog.csdn.net/20180306154556689?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

导线天线具有最佳的射频性能。与其他天线相比，**导线天线的天线增益和辐射性能是最好的**。请参考图25，了解导线天线的定性辐射方向图。

![这里写图片描述](http://img.blog.csdn.net/20180306154651845?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**各种天线的比较**

![这里写图片描述](http://img.blog.csdn.net/20180306154832764?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

____

## **9.环境对天线性能的影响**

　　**通常消费类产品中所使用的天线对PCB射频接地层的大小和产品的塑料外壳非常敏感**。可将天线模拟为一个LC谐振器，当L(电感)或C(电容)增加时，该LC谐振器的谐振频率会下降。更大的射频接地层和塑料外壳会增大有效电容，从而降低谐振频率。

　　**接地层的影响**

　　赛普拉斯已经广泛地研究了射频接地层的大小和附近塑料外壳对天线谐振频率产生的影响。通过实验和测量证明，赛普拉斯可以确定天线的灵敏度并提供一个既简单强大，又有效的解决方法，以便调试天线。

　　要想评估天线对射频接地层大小的灵敏度，可以通过在各种可能尺寸的PCB上安装天线进行实验。图26显示的是MIFA被放置在接地层大小不同的PCB上的示例。PCB的尺寸范围为20mm×20mm至50mm×50mm。

　　通过该曲线可以了解到，**射频接地层的面积越大，那么谐振频率越低，并且接地层也越好，因此回波损耗也会越小。**这便是好的PCB布局中的关键条件。**给四分之一波长的天线提供的接地层越好，它与理论性能的关系也会越好**。这是进行天线设计中的关键概念，可以解决没有足够空间提供给接地小型模块天线的困难。

![这里写图片描述](http://img.blog.csdn.net/20180306155121974?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**塑料外壳的影响**

　　同样，为确定产品的塑料外壳对天线的影响，要使用一个无线鼠标进行实验，如图27所示。将赛普拉斯MIFA放置在无线鼠标的塑料外壳中，然后测量该天线的谐振频率。

![这里写图片描述](http://img.blog.csdn.net/20180306155317591?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**通过图26和图27，可了解以下主要内容：**

　　§ 将天线放置在靠近塑料外壳的地方时，谐振频率会降低。

　　§ 谐振频率的变化范围为100MHz至200MHz。必须重新调试天线才能获得所需频带。

　　总之，**加大接地层大小和塑料外壳是为了使天线的谐振频率降低到100MHz至200MHz的范围内。**

　　**产品外壳和接地层指南**

　　§ 必须确保在天线末梢或天线长度范围附近不能有任何组件、固定螺钉或接地层。

　　§ 电池线或音频线不能穿过天线或PCB上天线布线的同一侧面。

　　§ 不能将金属外壳完全覆盖天线。如果产品外壳是金属的或是一个保护罩，请不要将外壳完全覆盖掉天线。

　　§ **天线的方向应该符合最终产品的方向，这样使天线在所需的方向上具有最大的辐射。**

　　§ 应该具有足够的空间：接地层越大，MIFA、IFA、芯片天线和导线天线的S11参数值(回波损耗)会越高。

　　§ **天线正下方不应该存在任何接地层。请参考图14。这个设置适用于所有天线。**

　　§ 从天线到接地层要有足够的空间(间隙)，该接地层的宽度应该最小。请参考图10、图15和图21。

_____

## **10.天线调试**

　　天线调试过程确保在所需频带中，**天线的回波损耗(从芯片输出的方向来看)大于10dB**。同样，对于芯片(Balun)，要执行相同的程序，用以确保在接受模式下，Balun的阻抗为50Ω。这时天线调试和Balun调试均被称为天线调试。

![这里写图片描述](http://img.blog.csdn.net/20180306160839123?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

50Ω参考点被连接至具有一个端口网络的**网络分析仪**。进行天线调试期间，通过移除Balun匹配组件可以断开同芯片的连接。进行Balun调试期间，会断开同天线匹配组件的连接。

　　以下章节将详细说明如何使用网络分析仪来调试天线。虽然这里仅显示了一个Pioneer套件无线鼠标的调试程序，不过该程序适用于所有天线调试。

　　**调试过程**

　　如前面所述，**外壳和接地层的影响使天线所需的频带失调，并且影响了回波损耗**。因此，天线调试过程包括两个步骤：**首先，将PCB空板调试为所需频带;然后在确定ID后，通过塑料外壳和人体接触检查调试。**

　　使用网络分析仪来检查天线调试。在第一个步骤中，先校准该网络分析仪，然后通过调整匹配网络组件和验证Smith图表中的调试进行天线调试。

　　调试过程中会使用：

　　§ 安捷伦(Agilent)8714ES网络分析仪(已校准)

　　§ Pioneer套件鼠标(如DUT)

　　§ 电气延迟时间为350ps的半刚性电缆

　　§ 质量的射频组件目录(Johanson套件P/N:L402DC)

　　调试过程主要步骤为：

　　1.准备ID

　　2.设置并校准网络分析仪

　　3.PCB空板调试。如同图中所示，标志1、2和3的回波损耗大于15db。

　　4.使用塑料和人体接触来调整调试

　　准备ID

　　该步骤非常重要，因为**同轴线缆的放置情况会使s11的变化值为3dB**。尽量使同轴线缆屏蔽的接地连接靠近传输线返回路径。请执行以下操作：

　　1.打开塑料外壳，去掉电池或断开供电电源。

　　2.使同轴线缆接近芯片的射频输出引脚。断开芯片连接。否则，不仅仅是天线，就连Balun也会连接到同轴线缆。请参见图29。

　　3.请确保，有一个裸露接地层靠近同轴线缆头。将线缆的屏蔽或外壳接地。将该屏蔽/外壳接地时，尽量缩短它与地面间的距离。该距离越小，调试准确度就越高。根据同轴线缆接地的位置，回波损耗测量的差值可为3dB。

　　4.将一个10pF的电容从50Ω参考点的第一个焊盘连接至天线末梢。要在同轴线缆和天线之间始终连接一个电容。这样能够阻止网络分析仪的直流电。

![这里写图片描述](http://img.blog.csdn.net/20180306164451701?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

设置并校准网络分析仪

　　1.使用3.5mm校准套件进行校准。接下来，将网络分析仪的校准套件设置为3.5mm后，按下Agilent8714ES上的cal(校准)按键。您也可以使用其他校准套件，如N型校准套件。

　　2.按下频率按键，分别将启动频率和停止频率设置为2GHz和3GHz，将格式设为Smith图表。

　　3.按下marker(标记)按键，将各标记的频率分别设为2.402GHz、2.44GHz和2.48GHz。

　　4.按下cal(校准)按键，选择网络分析仪上的S11并将其设为用户1端口校准。

　　5.要求连接“open”加载时，请连接“open”加载，并按下measurestandard。

　　6.连接“Short”加载，并按下measurestandard。

　　7.连接至“broadband”加载，并按下measurestandard。然后网络分析仪会计算系数，并将50Ω加载显示为Smith图表上明确标记为50,0的参考点。

　　8.通过按下‘scale’按键并正确设置电气延迟，可调试同轴线缆和设置电气延迟。

　**调试PCB空板**

　　想要调试PCB空板，先要确定天线的阻抗，然后根据匹配网络组件，在所需频带内使回波损耗低于10db。

　　1.将一个8.2pF大小的电容与天线串联起来。在所需频带内，该电容的阻抗为0Ω。该阻抗便是天线阻抗。天线的阻抗等于(100.36–j34.82)，如Smith图表中红色圆圈所示。

![这里写图片描述](http://img.blog.csdn.net/20180306164738685?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

2.确定天线的阻抗后，通过执行阻抗转换使用拓扑使天线阻抗变为50Ω。赛普拉斯MIFA或IFA的大部分匹配网络(图31所示)是由两个组件构成的。

![这里写图片描述](http://img.blog.csdn.net/20180306164826884?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

可以使用标准的开源工具(如:**Bern研究院的SmithV3.10**)对匹配网络组件进行仿真。通过将0.45pF的并联电容和3.6nH的串联电感连接到天线，可以将天线阻抗转换为50Ω,，从而能够在所需的频带中去除虚拟部分。由于准确值不可用，因此我们要选择一个0.5pF的并联电容和一个3.6nH的串联电感。

![这里写图片描述](http://img.blog.csdn.net/20180306164951479?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

以下显示的是匹配网络的最终原理图。ZL表示阻抗为0欧姆时天线的阻抗。Zin指的是输出阻抗为50欧姆时网络分析仪观察到的阻抗。

![这里写图片描述](http://img.blog.csdn.net/20180306165043467?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

仿真软件有助于了解组件值。但实际组件值与仿真值的差别很大。出现这种情况是因为频率为2.4GHz时，电容的走线间电感、焊盘的寄生加载以及接地返回路径构建了一个附加的寄生回路，从而完全改变了Smith图表。对于该应用，需要选择一个0.7pF的电容和一个1.2pF的串联电容，以得到谐振。

![这里写图片描述](http://img.blog.csdn.net/20180306165131227?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

下面是该操作的简要说明。天线阻抗来自假定阻抗为0欧姆的8.2pF电容器。此外，该图也显示了频率为2.4Ghz时走线间电感的寄生电容。接地返回路径紧挨着该天线。但**由于使用了匹配组件，接地返回路径将有额外的寄生电容。这样天线的电感会很大，所以要添加几个电容器以调整该电感**。这是调整天线时遇到的典型问题。理论与实践间存在着明显的差别。用户可添加一个电容器，但请注意，如果添加某个电感，Smith图会向一定的方向移动。图35显示的是使用实际组件的最终Smith图表。

![这里写图片描述](http://img.blog.csdn.net/20180306165333671?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

图中也显示了频率分别为2402MHz、2440MHz和2480MHz的标志1、2和3接近Smith图表上的(50,0)点。显示的是一个良好的匹配。

　　下面制图显示了组件值的回拨损耗。大于15db的回波损耗符合我们的应用。

![这里写图片描述](http://img.blog.csdn.net/20180306165438657?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

如同图中所示，标志1、2和3的回波损耗大于15db。

　　**使用塑料和人体接触来调整调试**

　　PCB的塑料外壳更改了天线调整。所有天线均受到近场或远场物体的影响。如果是一个窄带天线，它受近场物体的影响几率非常大。

　　塑料外壳和附近移动的电池线缆可使天线完全失调，并且在2.402G到2.482G的优选频带内它的回波损耗会低于10db。因此，调试PCB空板后，需要使PCB保持在塑料外壳内，并使用鼠标重新检测调试。这样操作很复杂，尤其是还要将同轴电缆引出塑料装配外。通过在ID内钻出一小孔，可以将该同轴电缆引出去。最后使用塑料外壳或将一只手放在塑料外壳上面(如同用户使用鼠标时)进行检查调试效果。所观察到的回波损耗的影响最小。

![这里写图片描述](http://img.blog.csdn.net/20180306165636889?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
______

**总结**

　　本应用笔记简单介绍了如何使用PRoCBLE/PSoCBLE轻松设计自定义产品的最佳天线。同时也提供了针对不同天线类型所需要的天线布局指南。

____
作者：Tapan Pattnayak 赛普拉斯半导体

_____

出处：http://mp.weixin.qq.com/s/fv36Ze1BjZtYcn5UKWewoA