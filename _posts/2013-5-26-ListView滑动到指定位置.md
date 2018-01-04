---
layout: post
title: ListView滑动到指定位置
date: 2013-5-26
tag: Android技术
---             

<h2>前言</h2>
每次回到首页需要重新获取数据，测试需要用户查看的item在指定位置。
           
<h2>实现代码</h2>
1. 首先需要将目前为止保存到全局变量中；
```
private void saveCurrentPosition() {
    if (mListView != null) {
        int position = mListView.getFirstVisiblePosition();
        View v = mListView.getChildAt(0);
        int top = (v == null) ? 0 : v.getTop();
        //保存position和top
    }
}
```
2. 获取数据之后将目标位置设置为顶部显示；
```
private void restorePosition() {
    if (mFolder != null && mListView != null) {
        int position = 0;//取出保存的数据
        int top = 0;//取出保存的数据
        mListView.setSelectionFromTop(position, top);
    }
```

-------------------------------
本文由darling_shadow整理完成，谢谢。
 
 
