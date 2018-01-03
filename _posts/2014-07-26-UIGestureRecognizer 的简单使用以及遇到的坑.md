---
layout: post
title: UIGestureRecognizer 的简单使用以及遇到的坑
date: 2014-07-26
tag: iOS技术
---             

<h2>前言</h2>
由官方文档可知，UIGestureRecognizer基类是一个抽象类，我们主要是使用它的子类。 
           
<h2>实践出真知</h2>
UIGestureRecognizer，我们主要使用它的子类，有以下子类：
```
- UITapGestureRecognizer 
- UIPinchGestureRecognizer 
- UIRotationGestureRecognizer 
- UISwipeGestureRecognizer 
- UIPanGestureRecognizer 
- UILongPressGestureRecognizer 
```
从名字上我们就能知道， Tap（点击）、Pinch（捏合）、Rotation（旋转）、Swipe（滑动，快速移动，是用于监测滑动的方向的）、Pan （拖移，慢速移动，是用于监测偏移的量的）以及 LongPress（长按）。

举个栗子
```
UITapGestureRecognizer *tapGesture = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(click)]; 
tapGesture.numberOfTapsRequired = 1; 
[_imageView addGestureRecognizer:tapGesture]; 
```
好了，以上就是简单的使用了，然后最近项目中有一个需求，需要在一张照片上同时处理点击和双击，好了，很简单：
```
UITapGestureRecognizer *tapGesture = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(click)];
tapGesture.numberOfTapsRequired = 1;
[_imageView addGestureRecognizer:tapGesture];
UITapGestureRecognizer *doubleGesture = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(doubleClick)];
doubleGesture.numberOfTapsRequired = 2;
[_imageView addGestureRecognizer:doubleGesture];
```
然后调试的时候发现双击的时候单击事件也会被执行，后来通过查看文档和和Google。得到以下：

不过有些手势是关联的，怎么办呢？例如 Tap 与 LongPress、Swipe与 Pan，或是 Tap 一次与Tap 兩次。 
手势识别是具有互斥的原则的，比如单击和双击，如果它识别出一种手势，其后的手势将不被识别。所以对于关联手势，要做特殊处理以帮助程序甄别，应该把当前手势归结到哪一类手势里面。 
比如，单击和双击并存时，如果不做处理，它就只能发送出单击的消息。为了能够识别出双击手势，就需要做一个特殊处理逻辑，即先判断手势是否是双击，在双击失效的情况下作为单击手势处理。使用 
[A requireGestureRecognizerToFail：B]函数，它可以指定当A手势发生时，即便A已经滿足条件了，也不会立刻触发，会等到指定的手势B确定失败之后才触发。以下：
```
// 关键在这一行，双击手势确定监测失败才会触发单击手势的相应操作
[tapGesture requireGestureRecognizerToFail:doubleGesture];
```
<h3>其他主要手势</h3>
其他、iphone操作手势的大概种类 
1.点击(Tap) 
点击作为最常用手势，用于按下或选择一个控件或条目（类似于普通的鼠标点击）、 
2.拖动(Drag) 
拖动用于实现一些页面的滚动，以及对控件的移动功能。 
3.滑动(Flick) 
滑动用于实现页面的快速滚动和翻页的功能。 
4.横扫(Swipe) 
横扫手势用于激活列表项的快捷操作菜单 
5.双击(Double Tap) 
双击放大并居中显示图片，或恢复原大小（如果当前已经放大）。同时，双击能够激活针对文字编辑菜单。 
6.放大(Pinch open) 
放大手势可以实现以下功能：打开订阅源，打开文章的详情。在照片查看的时候，放大手势也可实现放大图片的功能。 
7.缩小(Pinch close) 
缩小手势，可以实现与放大手势相反且对应的功能的功能：关闭订阅源退出到首页，关闭文章退出至索引页。在照片查看的时候，缩小手势也可实现缩小图片的功能。 
8.长按(Touch &Hold) 
在我的订阅页，长按订阅源将自动进入编辑模式，同时选中手指当前按下的订阅源。这时可直接拖动订阅源移动位置。 
针对文字长按，将出现放大镜辅助功能。松开后，则出现编辑菜单。 
针对图片长按，将出现编辑菜单。 
9.摇晃(Shake) 
摇晃手势，将出现撤销与重做菜单。主要是针对用户文本输入的。

-------------------------------
本文由darling_shadow整理完成，谢谢。
 
 
