---
layout: post
title: Altium Designer PCB中各层作用详解
date: 2017-03-12
categories: 电子之路
tags: [软件,PCB,技术]
description: 画板画板
---

转自:[AD使用总结--2__PCB中各层作用详解](http://blog.csdn.net/spdian/article/details/53436697)

1.mechanical:
机械层是定义整个PCB板的外观的，其实我们在说机械层的时候就是指整个PCB板的外形结构。禁止布线层是定义我们在布电气特性的铜时的边界，也就是说我们先定义了禁止布线层后，我们在以后的布过程中，所布的具有电气特性的线是不可能超出禁止布线层的边界. 

2.top overlay和bottom overlay:
是定义顶层和底的丝印字符，就是一般我们在PCB板上看到的元件编号和一些字符。 
 
 3.top paste和bottom paste:
 顶层、底焊盘层，它就是指我们可以看到的露在外面的铜铂，（比如我们在顶层布线层画了一根导线，这根导线我们在PCB上所看到的只是一根线而已，它是被整个绿油盖住的，但是我们在这根线的位置上的top paste层上画一个方形，或一个点，所打出来的板上这个方形和这个点就没有绿油了，而是铜铂。 

4.top solder和bottom solder: 
这两个层刚刚和前面两个层相反，可以这样说，这两个层就是要盖绿油的层。Solder是露出焊盘，paste是用于在焊盘上贴锡膏。另外paste层主要是用于SMD(表贴)元件的焊盘。

 5.Signal layer(信号层) ：
 信号层主要用于布置电路板上的导线。包括Top layer(顶层)，Bottom layer(底层)和30个MidLayer(中间层)。  

 6.Internal plane layer(内部电源/接地层) ：
 该类型的层仅用于多层板，主要用于布置电源线和接地线.我们称双层板，四层板，六层板，一般指信号层和内部电源/接地层的数目。  

7.Mechanical layer(机械层) ：
它一般用于设置电路板的外形尺寸，数据标记，对齐标记，装配说明以及其它的机械信息。这些信息因设计公司或PCB制造厂家的要求而有所不同。执行菜单命令Design|Mechanical Layer能为电路板设置更多的机械层。另外，机械层可以附加在其它层上一起输出显示。
  
Mechanical 1：一般用来绘制PCB的边框，作为其机械外形，故也称为外形层；

Mechanical 2：我们用来放置PCB加工工艺要求表格，包括尺寸、板材、板层等信息；

Mechanical 13 & Mechanical 15：ETM库中大多数元器件的本体尺寸信息，包括元器件的三维模型；为了页面的简洁，该层默认未显示；

Mechanical 16：ETM库中大多数元器件的占位面积信息，在项目早期可用来估算PCB尺寸；为了页面的简洁，该层默认未显示，而且颜色为黑色。

8.Solder mask layer(阻焊层) ：
在焊盘以外的各部位涂覆一层涂料，如防焊漆，用于阻止这些部位上锡。阻焊层用于在设计过程中匹配焊盘，是自动产生的。Protel 99 SE提供了Top Solder(顶层)和Bottom Solder(底层)两个阻焊层。 

 9.Paste mask layer(锡膏防护层，SMD贴片层) ：
 它和阻焊层的作用相似，不同的是在机器焊接时对应的表面粘贴式元件的焊盘。Protel 99 SE提供了Top Paste(顶层)和Bottom Paste(底层)两个锡膏防护层。主要针对PCB板上的SMD元件。如果板全部放置的是Dip(通孔)元件，这一层就不用输出Gerber文件了。在将SMD元件贴PCB板上以前，必须在每一个SMD焊盘上先涂上锡膏，在涂锡用的钢网就一定需要这个Paste Mask文件,菲林胶片才可以加工出来。Paste Mask层的Gerber输出最重要的一点要清楚，即这个层主要针对SMD元件，同时将这个层与上面介绍的Solder Mask作一比较，弄清两者的不同作用，因为从菲林胶片图中看这两个胶片图很相似。   

 10.Keep out layer(禁止布线层) ：
 用于定义在电路板上能够有效放置元件和布线的区域。在该层绘制一个封闭区域作为布线有效区，在该区域外是不能自动布局和布线的。  

 11.Multi layer(多层) ：电路板上焊盘和穿透式过孔要穿透整个电路板，与不同的导电图形层建立电气连接关系，因此系统专门设置了一个抽象的层—多层。一般，焊盘与过孔都要设置在多层上，如果关闭此层，焊盘与过孔就无法显示出来。  

 12.Drill layer(钻孔层) ：
 钻孔层提供电路板制造过程中的钻孔信息(如焊盘，过孔就需要钻孔)。Protel 99 SE提供了Drill gride(钻孔指示图)和Drill drawing(钻孔图)两个钻孔层。
 
 13.solder mask层和paste mask层:
 阻焊层：solder mask，是指板子上要上绿油的部分；因为它是负片输出，所以实际上有solder mask的部分实际效果并不上绿油，而是镀锡，呈银白色！ 
 
助焊层：paste mask，是机器贴片时要用的，是对应所有贴片元件的焊盘的，大小与top layer/bottom layer层一样，是用来开钢网漏锡用的。 

参考：
[菜鸟入门：PCB设计中各层的意义你真的懂么](http://www.elecfans.com/dianzichangshi/20161109447370.html)