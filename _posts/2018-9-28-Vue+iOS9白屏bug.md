---
layout: post
title: Vue+iOS9白屏bug
date: 2018-9-28
tag: iOS技术
---

<h3>前言</h3>
现在App的大部分功能使用了内嵌的h5，我们的首页和部分页面也适用的h5，现在客户反应Iphone6，系统版本9.3.1，app版本v1.7.1打开是白屏，所以我开始查找并解决这个bug；

           
<h3>开发流程</h3>
1. 因为目前我的是Xcode9，默认的simulator是11.4的，需要下载9.3的模拟器；
2. 分析原因；
3. 解决方案。


<h3>具体流程</h3>
<h4>下载9.3的simulator</h4>
1. 从Xcode菜单栏打开Preference->Componets->Simulators,下载对应版本的模拟器。点击左侧下载按钮即可开始下载对应的模拟器，文件都是1.5G左右，所以时间较长，请耐心等待。
2. 然后点击Xcode菜单栏Window->Devices，就可以看到所有本机已安装的设备列表。这时候需要点击左下角的+按钮，创建一个新的模拟器，OS Version选择9.3的sdk，创建成功后就可以从左侧列表看见刚添加的设备。

<h4>分析原因</h4>
1. 回到Xcode的运行页面，选择第2步添加的设备，即可运行，成功，首页h5页面的确白屏；
2. 这时候使用Safari浏览器，开发->模拟器-XXX-XXX->右侧箭头，即可选择；如下图：
![image](https://s1.ax1x.com/2018/09/28/iQw55R.jpg)
然后就能查看相关信息；
此时查看index.html文件，发现页面一片空白，body里只有一句<div id="app"></div>，然后有一个报错信息：SyntaxError: Unexpected keyword 'const'. Const declarations are not supported in strict mode；

<h4>解决方案</h4>
上述错误反馈是const这个在严格模式下，不支持，然后我通过全文检索，发现在一个全局js文件中使用了const关键字，然后把该const换成了var，重新build，传到服务器，使用上述模拟器测试，结果正常，能正确显示页面；

OK，完成！

-------------------------------
本文由darling_shadow完成，谢谢。
 
 
