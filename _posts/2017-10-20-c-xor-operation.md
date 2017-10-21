---
layout: post
title: C语言中位运算异或“∧”的作用
date: 2017-10-20
categories: C语言
tags: [C语言,软件,嵌入式]
description: C语言加强
---

## **1.概念**

异或运算符"∧"也称XOR运算符。它的规则是若参加运算的**两个二进位同号，则结果为0**（假）；**异号则为1**（真）。即 0∧0＝0，0∧1＝1， 1^0=1，1∧1＝0。

|运算|说明|
|---|---|
|0^0=0,0^1=1|0异或任何数，其结果=任何数|
|1^0=1,1^1=0|1异或任何数，其结果=任何数取反|
|x^x=0|任何数异或自己，等于把自己置0|

## **2.应用**

**（1）使特定位翻转**

　比如：01111010，想使其低4位翻转，即1变为0，0变为1。可以将它与00001111进行∧运算，即

　　![这里写图片描述](http://img.blog.csdn.net/20171020090747603?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

结果值的低4位正好是原数低4位的翻转。要使哪几位翻转就将与其∧运算的该几位置为1即可。这是因为原数中值为1的位与1进行∧运算得0，原数中的位值0与1进行∧运算的结果得1。

**(2)实现两个值的交换**

通过按位异或运算，可以实现两个值的交换，而不必使用临时变量。例如交换两个整数a=3，b=4的值，可通过下列语句实现：

a＝a∧b;
b＝b∧a;
a=a∧b;

![这里写图片描述](http://img.blog.csdn.net/20171020093302547?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

```c
#include <stdio.h>

void main()
{
	int a=3,b=4;

	 a=a^b;
	 printf("first value %d\n",a);

	 b=b^a;
	 printf("second value %d\n",b);

	 a=a^b;
	 printf("third value %d\n",a);

}
```

![这里写图片描述](http://img.blog.csdn.net/20171020093542844?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## **参考：**

1.[C语言中位运算符异或“∧”的作用](http://www.cnblogs.com/soTired/p/5312307.html)

2.[C语言异或运算](http://blog.csdn.net/broadce/article/details/52071360)