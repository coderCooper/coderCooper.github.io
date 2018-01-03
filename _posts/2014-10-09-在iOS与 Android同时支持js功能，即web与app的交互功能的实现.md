---
layout: post
title: 在iOS与 Android同时支持js功能，即web与app的交互功能的实现
date: 2014-10-09
tag: Android技术
---             

<h2>前言</h2>
在App的实践开发中，会需要使用h5与原生产生交互，比如h5调用app下订单页面，开启支付，经过实践，然后在开发过程中统一使用了以下方案。
           
<h2>实现过程</h2>
考虑到选择的方案是跳转url截取判断，所以就需要和h5约定好指定格式的字符串，然后客户端根据约定的串截取后判断执行指定的行为；
我们使用的是：
```
var url = ":darling:click_tel:darling:"+cmd+":darling:"+parm;
```

根据:darling:截取成字符串数组，第0位为目标行为，第1位为参数key，第2位为参数值，其实这就有个局限性，无法传多个参数，后来中间位基本被抛弃，使用最后一位参数传递json串，然后客户端解析处理数据。

<h3>h5代码</h3>

服务器就主要是js代码了。具体代码如下：
```
/*
* cmd --> 参数的key
* parm --> 参数的value
*/
function sendConment(cmd,parm){
    var url = ":darling:click_tel:darling:"+cmd+":darling:"+parm;
    document.loction = url;
    client_click.click_tel(parm);
}

```
<h3>iOS实现逻辑</h3>

在iOS客户端中使用：比较简单，就是判断用户点击的串，然后通过截取判断字符串，然后根据和服务器定义好的字典，进行不同的行为：
```
- (BOOL) webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType{
    NSURL *url = [request URL];
    NSString *requestString = [url absoluteString];
    NSArray *components = [requestString componentsSeparatedByString:@":darling:"];
    if (components.count > 1) {
        NSString *identifier = components[1];//目标行为
        NSString *argsKey = components[2];//参数名称
        NSString *argsValue = components[3];//参数的值
        if ([identifier isEqualToString:@"click_tel"]) {
            //浏览器不进行跳转
            return NO;
          }
        }
    return YES;
  }

```
<h3>Android实现逻辑</h3>

在Android客户端中使用：Android官方提供了与js交互的接口； 
在5.0之后，程序员需要显式的在类名前申明@SuppressLint("JavascriptInterface")
```
@SuppressLint("JavascriptInterface")
public class WebInfoActivity extends BaseClientActivity {
    ...
}
```

然后在webView初始化的时候；
```
//在初始化的时候进行js的申明；
//第一个参数为本地的下文中的内部类，第二个参数为服务器使用的方法名；
webView.addJavascriptInterface(new OrderInfo(), "client_click");
```

最后在
```
public final class OrderInfo {
    //JavaScript调用此方法跳到订单详情页
    @JavascriptInterface
    public void click_tel(final String parms) {
        AlertDialog.Builder builder = new AlertDialog.Builder(WebInfoActivity.this);
        builder.setTitle("拨打电话");
        builder.setMessage(parms);
        builder.setPositiveButton("呼叫", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                Intent intent = new Intent();
                intent.setAction(Intent.ACTION_CALL);
                intent.setData(Uri.parse("tel:" + parms)); 
                startActivity(intent);
            }
        });
        builder.setNegativeButton("取消", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {

                }
            });
            builder.create();
            builder.show();
        }
    }
```

在android中为了更好的用户体验，需要在WebViewClient的shouldOverrideUrlLoading中进行如下的阻止跳转；
```
@Override
public boolean shouldOverrideUrlLoading(WebView view, String url) {
    if (url != null && url.contains(":darling:")) {//表示是与app的交互
            return true;
        }else{
           return false; 
        }
    }
```

-------------------------------
本文由darling_shadow整理完成，谢谢。
 
 
