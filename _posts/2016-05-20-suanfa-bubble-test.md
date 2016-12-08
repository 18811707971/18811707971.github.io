---
layout: post
title: 排序算法——冒泡排序
date: 2016-05-20
categories: 算法
tags: [算法,C,程序]
description: 冒泡排序的简单实现
---


1.基本思想：

通过无序区相邻记录的关键字间的比较和位置的交换，使关键字最小的记录如气泡一般逐渐往上“漂浮”直至“水面”。

即：临近的数字两两进行比较,按照从小到大或者从大到小的顺序进行交换,这样一趟过去后,最大或最小的数字被交换到了最后一位,然后再从头开始进行两两比较交换,直到倒数第二位时结束。

2.举例：

下面以对 3  2  4  1 进行冒泡排序说明。

第一轮 排序过程
3  2  4  1    （最初）
2  3  4  2    （比较3和2，交换）
2  3  4  1    （比较3和4，不交换）
2  3  1  4    （比较4和1，交换）
第一轮结束，最大的数4已经在最后面，因此第二轮排序只需要对前面三个数进行再比较。

第二轮 排序过程
2  3  1  4 （第一轮排序结果）
2  3  1  4 （比较2和3，不交换）
2  1  3  4 （比较3和1，交换
第二轮结束，第二大的数已经排在倒数第二个位置，所以第三轮只需要比较前两个元素。

第三轮 排序过程
2  1  3  4  （第二轮排序结果）
1  2  3  4  （比较2和1，交换）
至此，排序结束。
算法总结及实现

对于具有N个元素的数组R[n]，进行最多N-1轮比较；

第一轮，逐个比较（R[0], R[1]）,  （R[1], R[2]）,  （R[2], R[3]）,  …….  （R[N-2], R[N-1]） ;  最大的元素会被移动到R[N-1]上。

第二轮，逐个比较（R[0], R[1]）,  （R[1], R[2]）,  （R[2], R[3]）,  …….  （R[N-3], R[N-2]）；第二大元素会被移动到R[N-1]上。

。。。。
以此类推，直到整个数组从小到大排序。

无论数组是否排序好了，都会进行N-1轮比较； 而优化实现，在数组已经排序好的情况下，会提前退出比较，减小了算法的时间复杂度。

3.C例程参考：

	#include <stdio.h>
	
	#define NUM 15
	
	void bubble (int num[],int n);
	
	int main ()
	{
		int num[NUM]={88,55,99,48,48,9,666,55,99,774,36,24,78,98,106};
	
		printf("space:%d\n",sizeof(num)/sizeof(num[0]));//数组元素个数的计算方法
	
		bubble(num,NUM);
		
		printf("Finnal order:\n");
		for(int i=0;i<NUM;i++)
		printf("%4d",num[i]);
		printf("\n");
		
		return 0;
	}
	
	void bubble (int num[],int n)
	{
		int temp;
		int flag;
		
		for(int i=0;i<n-1;i++){	//排序次数控制
			flag = 1;
			for(int j=0;j<n-1-i;j++){	//每次排序元素
				if(num[j]>num[j+1]){
					flag = 0;
					temp = num[j];
					num[j] = num[j+1];
					num[j+1] = temp;
				}
			}
				printf("every order:");
				printf("%d\n",i);
				for(int k=0;k<n;k++)
				printf("%4d",num[k]);
				printf("\n");
			if(flag)
					break;
		}
	}

关键：

![这里写图片描述](http://img.blog.csdn.net/20161208093849656?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

4.参考

[经典排序算法 - 冒泡排序Bubble sort ] (http://www.cnblogs.com/kkun/archive/2011/11/23/2260280.html)

[C语言冒泡排序算法及代码] (http://c.biancheng.net/cpp/html/2443.html)

[C语言冒泡排序法详解] (http://jingyan.baidu.com/article/6525d4b13f920bac7d2e9484.html)

今日箴言：唯有思考,才有真知