---
layout: post
title: 智能卡7816协议
date: 2017-08-31
categories: SMART CARD
tags: [IC-CARD,7816,BUS]
description: IC-CARD STUDY AND APPLICATION
---

## **1.硬件接口**

![这里写图片描述](http://img.blog.csdn.net/20170831090940185?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### **1.1端跨解释：**

|连接接口|解释|
|---------|:-----|
|VCC|电源输入（A类：5V， B类：3V）|
|RST |复位信号输入|
|CLK |时钟信号输入|
|GND |地，基准电压|
|VPP |编程电压输入，由卡选用|
|I/O |串行数据的输入、输出|

![这里写图片描述](http://img.blog.csdn.net/20170901101950970?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### **1.2IC卡的操作信息交互流程:**

当卡的触点物理的连接到接口设备的触点时，电路才能运行。

（1） 接口设备能够控制IC卡各IO引脚使其激活。 

（2） 接口设备给卡发送复位信号使卡复位启动。 

（3） 卡要向接口设备发送复位应答信号，将通信中必要的相关信息告知接口设备。 

（4） 接口设备对卡进行一次热复位，卡进行复位应答。 

（5） 接口设备发起一个PPS交互指令，选择要与卡通信的协议和相关参数。 

（6） 根据选择的协议（T=0或T=1）进行数据的通信。

![这里写图片描述](http://img.blog.csdn.net/20170901091935874?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### **1.3卡激活** 

为了与一个已经物理的连接的卡发起交互，接口设备需要通过下面的操作激活卡。

（1）RST置成 L 

（2）VCC上电 

（3）接口设备上I/O置成接收模式 

（4）在A类操作条件下，VPP应该置为停止状态；在B类操作条件下，不接VPP

（5）CLK将要被给一个时钟信号。

#### **1.4冷复位**

![这里写图片描述](http://img.blog.csdn.net/20170901093916688?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

激活结束后（RST在L状态，VCC上电，接口设备I/O在接收模式，CLK有一个适合的并且稳定的时钟信号），卡已经准备好冷复位。在冷启动之前，卡的内部状态没有定义。接口设备将发出一个冷复位信号，并从IC卡得到一个复位应答，过程如下： 

（1）从Ta时刻起，接口设备施加CLK。

（2）在Ta后不超过200个时钟周期内，IC卡必须将其I/O线置为接收方式。由于接口设备也必须在这段期限内置其I/O线驱动器为接收方式，所以I/O线在Ta后最迟不超过200个时钟周期的时间内置为高电平。为此，接口设备的I/O触点应经过一个上拉电阻接到V CC。 

（3）接口设备应从Ta开始保持RST端为低电平状态至少400个时钟周期，并在T b 将其置为高电平； 
 
（4）IC卡上I/O的复位应答将在T b时刻后的400至40，000个时钟周期内开始； 

（5）如果IC卡在RST被拉高后的40000个时钟周期内没有应答，则RST将被拉低并且卡将被去激活。

注:1.假定卡的内部状态在冷复位前不定,这样卡的设计必须避免不适当的操作

2.卡的复位可以由接口设备在任意时间随意启动

#### **1.5热复位**

![这里写图片描述](http://img.blog.csdn.net/20170901094340267?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

IC卡对终端的复位应答有着规定的规格和内容，如果终端收到的复位应答不符合规定要求时，终端将启动一个热复位并从IC卡获得复位信号。过程如下： 

（1）在VCC和CLK信号保持稳定后，接口设备通过拉低RST发起热复位，RST低电平至少要持续400个时钟周期； 

（2）在Tc之后的最多200周期内，IC卡和接口设备都必须置I/O为接收方式，即I/O线在Tc后最迟不超过200个时钟周期内置为高电平； 

（3）接口设备在RST保持低电平大于400个时钟周期后，将其拉高； 

（4）IC卡上I/O的复位应答将在T后的400至40，000个时钟周期内开始；

（5）如果IC卡在RST被拉高后的40000个时钟周期内没有应答，RST将被拉低并且卡将被去激活； 

#### **1.6时钟停止**

![这里写图片描述](http://img.blog.csdn.net/20170901094824545?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

对于支持时钟停止的卡，当接口设备认为没有来自卡的传输并当I/O已经保持在H至少1860时钟周期（延时tg），在VCC上电并且RST在H时，接口设备可以对于CLK（Te）进行时钟停止。 

当时钟停止时（在Te~Tf），根据已被确定好的时钟指示器X ，CLK将要继续保持在在H或是在L。时钟指示器X的值应在复位应答序列TA（i）当中设置。 

在Tf，接口设备重启时钟，在至少700时钟延时后（th），在I/O的信息交换可以继续。

#### **1.7去激活** 

![这里写图片描述](http://img.blog.csdn.net/20170901095026795?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

当信息交换被完成或者被终止（例如卡没有应答，或检测到卡的移动），接口设备将按照下面的序列停用IC卡：

 （1）将RST 置L 
 
（2）将CLK置L（除非时钟停止在L状态）

（3）将VPP去激活 

（4）将I/O置L

 （5）将VCC去激活

#### **1.8参数选择**

接口设备可以发送协议和参数选择指令（PPS），选择要和IC卡进行通信的协议和相关的参数。 
 
（1） PPS协议 
 
——>IFD发送一个PPS请求给ICC 

——>如果ICC收到一个错误的请求，则不响应。
 
——>如果ICC受到一个正确的请求，则返回一个PPS响应，否则将超过初始等待时间 

——>如果ICC超出初始等待时间，则IFD复位或者拒绝ICC 

——>如果IFD收到一个错误的应答，则复位或者拒绝ICC

 ——>如果PPS交换失败，则IFD复位或者拒绝ICC  
 
（2） PPS请求的结构和内容 

PPS 请求和响应分别包括一个初始字节 PPSS 后随格式字节 PPS0 三个可选参数字节 PPS1 PPS2 和 PPS3 以及一个检测字节 PCK。

PPS 识别 PPS 请求或响应并等于 FF。

PPS0 通过位 b5 b6 b7 分别指明可选字节 PPS1 PPS2 PPS3 的存在 位 b4 到b1 传输参数 T 的值以提出协议 位 b8 留作未来使用并设定为 0。

PPS1 允许 IFD 对卡提出 F 和 D 的值 。

PPS请求和应答的结构如下：

![这里写图片描述](http://img.blog.csdn.net/20170901101239108?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

如果IFD不发送PPS1，它应该继续使用Fd和Dd。ICC通过响应PPS1确认这两个值（此时使用的Fn和Dn），或者不响应PPS1而继续使用默认值Fd和Dd。 PPS2和PPS3保留将来使用。 PCK的值应使从PPSS至PCK（包括PCK）的所有字节的异或操作结果为0。


## **2.通讯协议说明**

#### **2.1字符传输：**

Smart Card的字符传输采用的是**异步半双工模式**，这种异步的模式很像个人电脑上的RS232通信。传输一个字符时，除了8Bits的数据外，还加了以下几个Bits：

起始位：用于字符帧的同步

校验位： 用于校验检测

在字符传输之前，I/O应该处于高电平。智能卡与CPU之间按照异步方式传送字节，其具有统一的字符传输格式，一个字由连续的10个ETU完成发送，期间传送1个起始位，8个数据位和1个奇偶检验位。其数据帧传输格式：10bit。

![这里写图片描述](http://img.blog.csdn.net/20170831134031370?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

7816-3定义的通讯协议，基本上可以说是RS232的翻版并在此基础上进行的改进，7816中只有一个IO同时兼具数据收发的功能，这点和RS232用RxD和TxD进行数据收发是不同的。在RS232中我们有9600波特率、起始位、奇偶校验位、停止位这些概念，而在7816-3中都完整地保留了下来，只不过7816-3中引入了etu，没有使用bps，但是基本原理是一致的。etu的定义可以更加精确地描述每个数据位在传输过程中收发双方的职责和角色转换。

根据定义在智能卡上电复位的时候 **1 etu = 372 / f** ，其中f = 读写设备通过CLK管脚提供给智能卡的时钟频率，通常在1--5 MHz之间。

**etu**的单位是时间单位秒（毫秒、微秒），**等同于传输每个数据位所需的时间**。对其取倒数得出来的就是每秒传输的数据位，也就是bps。我们取f = 3.579545 MHz，用3579545除以372结果等于9622.4约为9600 bps。

#### **2.2复位应答——ATR**

智能卡始终处于被动的状态，所以终端设备在和智能卡进行数据交互的时候，需要首先给智能卡发指令，智能卡才会对应地给出应答。而智能卡告诉终端的第一句话就是ATR，亦即“复位应答”。

**ATR内容:**


![这里写图片描述](http://img.blog.csdn.net/20170831110723458?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


**各字符解释：**

|字符|说明|
|----|----|
|TS|初始化字符|
|TO|格式化字符|
|TAi, TBi, TCi, TDi (i=0/1)|接口字符|
|T1, T2, T3 … TK|历史字符（指明了一些通用的信息，如卡的制造商，卡中被嵌入的芯片，卡的文件状态等）|
|TCK|校验字符|

TS:初始字符，定义随后字符的逻辑电平和位序。

![这里写图片描述](http://img.blog.csdn.net/20170831111441687?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

T0:两部分组成
 
高四位（b5-b8）称之为Y1，用来指示后继字符TA1至TD1是否存在，b5-b8位被置为逻辑“1“状态者，相应地表明TA1至TD1的存在。 

低四位组（b1-b4）称之为K，则表明历史字节存在的数量

#### **2.3T=0 协议**

**T=0是按照单个字符的方式实现智能卡和终端数据传输。**

卡片和终端之间的数据传输是通过**命令响应**的方式进行的，卡片只能被动地接收命令，并且给出响应。**所有的命令都是以命令头开始**，而该命令被完整地执行后（无论结果对错），必须以包含状态字（SW1 SW2）的响应结束。卡片和终端之间如何具体进行数据传输的，就依靠不同的通讯协议来实现，其中主要有T=0、T=1、T=CL。其中T=0和T=1适用于接触式卡片（7816），而T=CL适用于非接触式卡片（14443）。

数据传输时，终端向卡片发送命令，包含5个字节（分别是CLA + INS + P1 + P2 + P3，其中INS表示指令，说明该命令的作用）的命令头，也就是说卡片必须要收到来自终端的5个字节的命令头，才能决定下一步该干什么，然后根据做出的决定给终端一个反馈意见。同样终端在发送完5个字节的命令头后，也需要等待来自卡片的反馈后，再决定下一步该干什么。

反馈信息一共有三种：（1）空操作的过程字节NULL（0x60）；（2）响应字节ACK；（3）状态字的第一个字节SW1。

NULL字节相当于网络通信中的“心跳包”，就是告诉终端不要进行超时处理，继续等待，而且NULL字节不见得是在卡片接收5个字节的命令头后反馈，通常在卡片需要进行大量数据更新或者复杂的加解密运算时，来不及返回状态字（SW）时先发一个**0x60**。

ACK是用来决定后续的数据传输的，不仅能决定传输的方向，还能决定后续传输字节的多少。**如果ACK = INS，则传输剩余的全部字节**，如果ACK = INS ^ FF，则传输后续的一个字节。

SW1只能是“0x9X”以及“0x6X”（不能是空操作字节“0x60”），如果卡片返回的是SW1，那么接下来必须紧跟着另一个状态字节SW2，标志着卡片对于该命令处理的结束，如果不断电，卡片在发送完SW2之后会等待下一个命令的到来。另外，因为SW1只能是“0x6X”和“0x9X”，所以INS一定不能取值0x6X和0x9X。

ACK里面为什么会有仅传输后续一个字节的情形，而不是一次传完后续的所有字节？主要是因为如果智能卡芯片没有那么多可以用于临时缓存命令数据的RAM空间的话，就需要接收一个字节先处理一个字节，直到剩余的字节能被一次性地处理了，再反馈一个INS作为过程字节，把剩余的字节全部收进来。

#### **2.4T=1协议**

T=1，最小的数据传输单元是“数据块”，这个数据块由若干个字节组成，其中有些字节是必须的，有些字节是可选的。每个数据块最多可以包含多少个字节（也就是数据块的大小）可以通过三种途径确定：

（1）ATR中对应协议T=1的专有接口字节指出；

（2）使用默认的值；

（3）在数据传输过程中协商。

一个数据块其实就是一个“数据包”，其中包头的3个字节（NAD 1字节 + PCB 1字节 + LEN 1字节）和包尾1个或者2个字节（如果采用LRC校验就是 1字节，采用CRC校验就是 2字节）是必须的，中间的信息数据域INF则是可选的。其中NAD代表节点地址，基本不用，可以设置为“00”；PCB代表协议控制字节，用来指明数据块的类型（分为：传递信息域数据的I-块，应答响应的R-块，负责通讯参数协商的S-块）、序列号、是否存在后续的链接块、是否有校验错误、额外超时等待请求、信息数据域大小的协商等。

**T=0、1的区别：**协议最明显的不同是T=1协议按照OSI的参考模型进行了分层，分别是：物理层、数据链路层和应用层。物理层主要是数据字符的传输，数据链路层主要是数据块的传输，而应用层主要是APDU的交互传输。APDU的交互与数据字符的传输，这些和T=0协议里面描述的大体相同，关键的就是数据链路层定义的数据块传输。因为这个数据链路层的定义，使得T=1协议基本具备了可以实现复杂网络数据传输的全部特征，相比而言T=0协议简直就等同于“裸传”了。


#### **2.5APDU报文结构**

APDU（ApplicationProtocolDataUnit--应用协议数据单元）。协议数据单元PDU（ProtocolDataUnit）是指对等层次之间传递的数据单位。协议数据单元(ProtocolDataUnit)物理层的PDU是数据位（bit），数据链路层的PDU是数据帧（frame），网络层的PDU是数据包（packet），传输层的PDU是数据段（segment），其他更高层次的PDU是数据（data）。

智能卡数据的操作，包括选择、读取、写入/更新都是通过特定的文件操作APDU命令来完成的。

**命令APDU由一个四个字节长的命令头和可变长度的命令体构成。**

![这里写图片描述](http://img.blog.csdn.net/20170831113224956?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

命令APDU传输的数据长度由Lc表示(命令数据域的长度)。 

命令响应APDU数据域的最大长度由期望数据长度(Le)表示。当 Le 值为零时，应答数据域最大长度为256。如果命令报文中需要，则Le总是‘00’。

**命令各字段含义:**

|字段|含义|
|---|----|
|CLA|命令报文的类别字节|
|P1| 参数 1|
|P2|参数 2|
|Lc|Data的长度（含4字节mac）|
|Data|在命令的数据字段中发送的字节串|
|Le|在向命令响应的数据字段中期望的字节最大数|


**四种命令：**

|类别|组成|
|---|---|
|Case1 | CLA INS P1 P2|
|Case2 | CLA INS P1 P2 Le|
|Case3 | CLA INS P1 P2 Lc Data|
|Case4 | CLA INS P1 P2 Lc Data Le|


**Case1:**

4字节命令头，这种情况时，命令中没有数据送到卡（ Lc）中，也没有数据从卡中返回（ Le）。

不含安全报文的命令： CLA | INS | P1 | P2

含安全报文的命令： CLA | INS | P1 | P2 | Lc | MAC

注： Lc = MAC 的长度， 4 字节。

**Case2:**

5字节命令头，这种情况时，命令中没有数据送到卡（ Lc）中，有数据从卡中返回（ Le）。

不含安全报文的命令： CLA | INS | P1 | P2 | Le

含安全报文的命令： CLA | INS | P1 | P2 | Lc | MAC | Le

注： Lc = MAC 的长度， 4 字节。

**Case3:**

5字节命令头，这种情况时，命令中有数据送到卡（ Lc）中，没有数据从卡中返回（ Le）。

不含安全报文的命令： CLA | INS | P1 | P2 | Lc | Data

含安全报文的命令： CLA | INS | P1 | P2 | Lc | Data+MAC

注： Lc = 数据的长度 + MAC 的长度（ 4 字节）。

**Case4:**

5字节命令头，这种情况时，命令中既有数据送到卡（ Lc）中，也有数据从卡中返回（ Le）。

不含安全报文的命令： CLA | INS | P1 | P2 | Lc | Data | Le

含安全报文的命令： CLA | INS | P1 | P2 | Lc | Data+MAC | Le

注： Lc = 数据的长度 + MAC 的长度（ 4 字节）。



**响应APDU包括一个可变长度的报文体和一个长度为2个字节的报文尾。**

![这里写图片描述](http://img.blog.csdn.net/20170831113426958?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**字段解释：**

|字段|解释|
|---|---|
|Data|在响应的数据字段中收到的字节串|
|SW1|状态字 1|
|SW2|状态字 2|





## **参考：**

1.[深入理解7816（1）---- 关于F/D和etu](http://blog.sina.com.cn/s/blog_4df8400a0101gkss.html)

2.[深入理解7816（2）---关于ATR ](http://blog.sina.com.cn/s/blog_4df8400a0102vcrk.html)

3.[深入理解7816（3）-----关于T=0 ](http://blog.sina.com.cn/s/blog_4df8400a0102vcyp.html)

4.[深入理解7816（4）---关于T=1 ](http://blog.sina.com.cn/s/blog_4df8400a0102vdiy.html)

5.[ISO 7816协议及智能卡驱动介绍](https://wenku.baidu.com/view/7198de8683d049649b665889.html?re=view)

6.[IC卡标准 中文版 第三册](https://wenku.baidu.com/view/d2ce754c2b160b4e767fcf96.html?re=view)

7.[智能卡 7816协议 ](http://blog.chinaunix.net/xmlrpc.php?id=4062696&r=blog/article&uid=28781109)

8.[智能卡 APTU命令 ](http://blog.chinaunix.net/xmlrpc.php?r=blog/article&uid=28781109&id=4062923)

9.[IC卡复位应答ATR的数据元和它们的意义 ](http://www.cnblogs.com/utank/p/5463269.html)

10.[ISO7816 传输协议 T0 T1](http://blog.csdn.net/michaelcao1980/article/details/12967609)

11.[APDU](http://www.cnblogs.com/snail0404/p/5436348.html)

12.[IC卡通信协议详解(7816-3)](https://wenku.baidu.com/view/385f58e30722192e4436f608.html)


