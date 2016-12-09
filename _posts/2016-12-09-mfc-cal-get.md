---
layout: post
title: VS2005——MFC简易计算器
date: 2016-12-09
categories: MFC
tags: [学习,工作,编程]
description: 简易计算器实现

---


经过几天的看书，查资料，总算是把一个MFC工程的框架有了一定的了解，对类等相关的概念有了一些认识，特别是看了文本框中对编辑框文本的读取方法之后，总算自己对计算器的书写有了一些想法。于是，开工，走起，当然，在过程中也遇到了一些问题，但是经过努力，总算做出了一个简易的计算器。这里的这个只要是看了师姐给的资料做的，更多的是参考了网上前辈的轮子。自己的等再进行完善后，再出最终版（现在还没解决如何实现编辑框不同行的判断）！

1.最后的框架：

![这里写图片描述](http://img.blog.csdn.net/20161210001100486?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

可以实现简单的加减乘除运算，并带有退格，清除功能。更多的功能敬请期待...........

2.建立一个文本框的MFC工程，可以得到如下的类：

![这里写图片描述](http://img.blog.csdn.net/20161210001412191?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

我们主要对xxxxxDlg这个框架类进行操作。

3.处理数据变量定义：

定义三个变量，分别用于存放三个编辑框的输入的数，我这里是通过在编辑框添加一个变量来实现。添加后，两个发生了变化：

如下：

（1）CalculatorDlg.h 文件：

![定义三个变量](http://img.blog.csdn.net/20161210002110591?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

（2）CalculatorDlg.c文件

![在构造函数中初始化变量](http://img.blog.csdn.net/20161210002151998?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast) 

DoDataExchange函数

![这里写图片描述](http://img.blog.csdn.net/20161210002340766?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

这个函数实现了对话框控件和类成员变量的关联，然后我们就可以使用下面的两个函数实现数据的读入写出：

![这里写图片描述](http://img.blog.csdn.net/20161210005502538?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

4.编辑框输入的选择

我们发现前面的计算器中三个编辑框，前两个是用于用于计算的数据的输入，但我如何判别输入的数据是到一还是二编辑框呢？这里我们添加了一个鼠标左键消息，用于判别。我们在两个输入编辑框分别添加一个事件处理函数，EN_SETFOCUS这个消息来区分焦点。

![鼠标左键消息](http://img.blog.csdn.net/20161210003339467?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![两个编辑框焦点区分](http://img.blog.csdn.net/20161210003309793?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

我们通过一个标志flag的值来判断此时用于哪个编辑框输入。这个焦点的判别刚开始还让我烦恼了好长时间，一直没有想到如何判别到底是哪个编辑框的输入。现在总算完美解决了。

5.下面是实现的主要代码


	void CCalculatorDlg::OnLButtonDown(UINT nFlags,CPoint point)//鼠标左键的消息
	{
		this->SetFocus();//焦点事件处理
		flag = 0;
		CDialog::OnLButtonDown(nFlags,point);
	}
	void CCalculatorDlg::OnEnSetfocusEdit1()
	{
		// TODO: 在此添加控件通知处理程序代码
		flag = 1;
	}
	
	void CCalculatorDlg::OnEnSetfocusEdit2()
	{
		// TODO: 在此添加控件通知处理程序代码
		flag = 2;
	}
	
	void CCalculatorDlg::OnBnClickedButtonNum0()
	{
		// TODO: 在此添加控件通知处理程序代码
		UpdateData(TRUE);
		if (flag == 1)
			m_Num1 = m_Num1 * 10 + 0;//编辑框1的输入保存到了m_Num1中
		else if (flag == 2)
			m_Num2 = m_Num2 * 10 + 0;
		UpdateData(FALSE);
	}
	
	void CCalculatorDlg::OnBnClickedButtonNum1()
	{
		// TODO: 在此添加控件通知处理程序代码
		UpdateData(TRUE);
		if (flag == 1)
			m_Num1 = m_Num1 * 10 + 1;
		else if (flag == 2)
			m_Num2 = m_Num2 * 10 + 1;
		UpdateData(FALSE);
	}
	
	void CCalculatorDlg::OnBnClickedButtonNum2()
	{
		// TODO: 在此添加控件通知处理程序代码
		UpdateData(TRUE);
		if (flag == 1)
			m_Num1 = m_Num1 * 10 + 2;
		else if (flag == 2)
			m_Num2 = m_Num2 * 10 + 2;
		UpdateData(FALSE);
	}
	
	void CCalculatorDlg::OnBnClickedButtonNum3()
	{
		// TODO: 在此添加控件通知处理程序代码
		UpdateData(TRUE);
		if (flag == 1)
			m_Num1 = m_Num1 * 10 + 3;
		else if (flag == 2)
			m_Num2 = m_Num2 * 10 + 3;
		UpdateData(FALSE);
	}
	
	void CCalculatorDlg::OnBnClickedButtonNum4()
	{
		// TODO: 在此添加控件通知处理程序代码
		UpdateData(TRUE);
		if (flag == 1)
			m_Num1 = m_Num1 * 10 + 4;
		else if (flag == 2)
			m_Num2 = m_Num2 * 10 + 4;
		UpdateData(FALSE);
	}
	
	void CCalculatorDlg::OnBnClickedButtonNum5()
	{
		// TODO: 在此添加控件通知处理程序代码
		UpdateData(TRUE);
		if (flag == 1)
			m_Num1 = m_Num1 * 10 + 5;
		else if (flag == 2)
			m_Num2 = m_Num2 * 10 + 5;
		UpdateData(FALSE);
	}
	
	void CCalculatorDlg::OnBnClickedButtonNum6()
	{
		// TODO: 在此添加控件通知处理程序代码
		UpdateData(TRUE);
		if (flag == 1)
			m_Num1 = m_Num1 * 10 + 6;
		else if (flag == 2)
			m_Num2 = m_Num2 * 10 + 6;
		UpdateData(FALSE);
	}
	
	void CCalculatorDlg::OnBnClickedButtonNum7()
	{
		// TODO: 在此添加控件通知处理程序代码
		UpdateData(TRUE);
		if (flag == 1)
			m_Num1 = m_Num1 * 10 + 7;
		else if (flag == 2)
			m_Num2 = m_Num2 * 10 + 7;
		UpdateData(FALSE);
	}
	
	void CCalculatorDlg::OnBnClickedButtonNum8()
	{
		// TODO: 在此添加控件通知处理程序代码
		UpdateData(TRUE);
		if (flag == 1)
			m_Num1 = m_Num1 * 10 + 8;
		else if (flag == 2)
			m_Num2 = m_Num2 * 10 + 8;
		UpdateData(FALSE);
	}
	
	void CCalculatorDlg::OnBnClickedButtonNum9()
	{
		// TODO: 在此添加控件通知处理程序代码
		UpdateData(TRUE);
		if (flag == 1)
			m_Num1 = m_Num1 * 10 + 9;
		else if (flag == 2)
			m_Num2 = m_Num2 * 10 + 9;
		UpdateData(FALSE);
	}
	
	void CCalculatorDlg::OnBnClickedButtonAdd()
	{
		// TODO: 在此添加控件通知处理程序代码
		UpdateData(TRUE);
		double Val;
		Val = m_Num1 + m_Num2;
		m_Ret = Val;
		UpdateData(FALSE);
	}
	
	void CCalculatorDlg::OnBnClickedButtonSub()
	{
		// TODO: 在此添加控件通知处理程序代码
		UpdateData(TRUE);
		double Val;
		Val = m_Num1 - m_Num2;
		m_Ret = Val;
		UpdateData(FALSE);
	}
	
	void CCalculatorDlg::OnBnClickedButtonMut()
	{
		// TODO: 在此添加控件通知处理程序代码
		UpdateData(TRUE);
		double Val;
		Val = m_Num1 * m_Num2;
		m_Ret = Val;
		UpdateData(FALSE);
	}
	
	void CCalculatorDlg::OnBnClickedButtonDiv()
	{
		// TODO: 在此添加控件通知处理程序代码
		UpdateData(TRUE);
		double Val;
		if (m_Num2 == 0)
			Val = m_Num1;
		else
			Val = m_Num1 / m_Num2;
		m_Ret = Val;
		UpdateData(FALSE);
	}
	
	void CCalculatorDlg::OnBnClickedButtonRec()
	{
		// TODO: 在此添加控件通知处理程序代码
		UpdateData(TRUE);
		double Val;
		if (flag == 1)
			Val = 1/m_Num1;
		else if (flag == 2)
			Val = 1/m_Num2;
		m_Ret = Val;
		UpdateData(FALSE);
	}
	
	void CCalculatorDlg::OnBnClickedButtonCle()
	{
		// TODO: 在此添加控件通知处理程序代码
		GetDlgItem(IDC_EDIT1)->SetWindowText(_T(""));
		GetDlgItem(IDC_EDIT2)->SetWindowText(_T(""));
		GetDlgItem(IDC_EDIT3)->SetWindowText(_T(""));
		m_Num1 = 0;
		m_Num2 = 0;
		flag = 0;
		m_Ret = 0;
		//清空编辑框后显示为0
		GetDlgItem(IDC_EDIT1)->SetWindowText(_T("0"));
		GetDlgItem(IDC_EDIT2)->SetWindowText(_T("0"));
		GetDlgItem(IDC_EDIT3)->SetWindowText(_T("0"));
	}
	
	void CCalculatorDlg::OnBnClickedButtonLef()
	{
		// TODO: 在此添加控件通知处理程序代码
		CString str;
		
		if (flag == 1)
		{
			GetDlgItem(IDC_EDIT1)->GetWindowText(str);
			str = str.Left(str.GetLength()-1);//退格的实现
			GetDlgItem(IDC_EDIT1)->SetWindowText(str);
		}
		else if (flag == 2)
		{
			GetDlgItem(IDC_EDIT2)->GetWindowText(str);
			str = str.Left(str.GetLength()-1);
			GetDlgItem(IDC_EDIT2)->SetWindowText(str);
		}
	}

6.消息实现机制

（1）.在.h文件中产生消息处理函数的声明

![这里写图片描述](http://img.blog.csdn.net/20161210004913645?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

（2）在类的消息映射表中添加消息映射入口项

![这里写图片描述](http://img.blog.csdn.net/20161210004842801?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

（3）.在类中实现具体的消息处理函数

![这里写图片描述](http://img.blog.csdn.net/20161210004947287?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

对控件的处理均为消息处理函数

7.演示

![加法计算](http://img.blog.csdn.net/20161210005307349?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

这里就简单的演示加法，其他也可以计算的。

8.总结：

自己刚开始学习MFC编程，可能还有很多的地方理解的不够到位，存在很多问题，但是从空白到一点，也是一个小小的进步。这几天看着书，跟着学习，也实现了一些小功能，但还是需要不断地去巩固，动手动脑，脚踏实地，在学习的过程中，不断地反思，虽然很艰难，但还是要继续前行。写完，夜已深，他乡的夜总是寒意连连，但为了心中的牵挂，也要坚强，坚持！

今日箴言：

![这里写图片描述](http://img.blog.csdn.net/20161210010201587?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
