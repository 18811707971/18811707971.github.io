---
layout: post
title: UpdateData(TRUE)和UpdateData(FALSE)的区别
date: 2016-12-02
categories: MFC
tags: [C,C++,Windows]
description: MFC study
---


注：[本文转自----UpdateData(TRUE)和UpdateData(FALSE)的区别] (http://blog.csdn.net/thisispan/article/details/7410729)

当你使用了ClassWizard建立了控件和变量之间的联系后：当你修改了变量的值，而希望对话框控件更新显示，就应该在修改变量后调用UpdateData(FALSE)；如果你希望知道用户在对话框中到底输入了什么，就应该在访问变量前调用UpdateData(TRUE)。 

1.

UpdateData(true); 

用窗体上控件中的内容来更新和控件相关连的变量的值（只能更新value类型的变量） 

例如：你在你的窗体中有一个Edit控件，为这个控件关联了CString类型的变量m_strName; 

你在控件中添入内容之后，必须调用UpdateData(true);才能把你添入的内容传给m_strName这个变量

UpdateData(false); 

和上面的正好相反，还是以上面的Edit为例，当你在程序中改变了m_strName变量的内容的时候，如果你想让那个Edit也显示更新后的m_strName,就必须调用UpdateData(false); 

这样在你的Edit中才能显示更新完的变量的值！ 

2.

UpdateData(TRUE)是将控件的状态传给其关联的变量，当然你要为控件关联上变量才行。

UpdateData(FALSE)是将控件的关联变量的值传给控件并改变控件状态。

UpdateData刷新的是当前对话框。

UpdateData(true);//用于将屏幕上控件中的数据交换到变量中。

UpdateData(false);//用于将数据在屏幕中对应控件中显示出来。

3.

控件，与控件关联的变量m_edit，当程序执行，你在控件中输入数21,要把21传到m_edit,UpdateData(true).如果把m_edit修改，要传到控件UpdateData(false);

另外注意一点：

使用UpdateData()函数时，当前界面上所有绑定了的变量(即通过MFC ClassWizard给控件添加了对应的变量)都会被UpdateData(TRUE)更新成对应控件中的内容；同样所有绑定了变量的控件中的内容也会UpdateData(FALSE)更新成对应变量中的内容。

要接受用户的输入就true，

要输入结果给用户就flase

数据量不多的话，最好用SetDlgItemText()和GetDlgItemText()