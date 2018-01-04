---
layout: post
title: ListView滑动到指定位置&更新某一行的view
date: 2013-5-26
tag: Android技术
---             

<h2>前言</h2>
1. 每次回到首页需要重新获取数据，测试需要用户查看的item在指定位置；
2. 更新指定行的view，而不需要整体notify。
        
<h2>滑动到指定位置</h2>
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

<h2>更新某一行的view</h2>
```
   public void updateView(int itemIndex) {
        // 得到第1个可显示控件的位置,记住是第1个可显示控件噢。而不是第1个控件
        int visiblePosition = gridView.getFirstVisiblePosition();
        // 得到你需要更新item的View
        View convertView = gridView.getChildAt(itemIndex - visiblePosition);
        if (convertView != null) {//此时被刷新行被隐藏
            ViewHolder holder = (ViewHolder) convertView.getTag();
            ImageEntry entry = images.get(itemIndex);
            if (entry.isCheck()) {
                holder.img_check.setVisibility(View.VISIBLE);
            } else {
                holder.img_check.setVisibility(View.INVISIBLE);
            }
        }
    }
```

-------------------------------
本文由darling_shadow整理完成，谢谢。
 
 
