---
layout: post
title: iOS和Android传json字符串到前端h5
date: 2019-9-20
tag: 大前端技术
---

<h3>前言</h3>
最近有需求在iOS和Android中分别传json字符串到h5中使用，发现了问题。

           
<h3>问题</h3>

在完成所有的逻辑功能后，开始自测，结果发现iOS传字符串，但是h5接收的是json对象；Android传的是字符串，h5接收的是字符串，花了几个小时，各种调试，各种debug，后来我妥协了，在h5前端做了兼容处理。


<h3>解决</h3>
<h4>Android传json字符串代码</h4>

```
  JSONObject jsonObject = new JSONObject();
  jsonObject.put("img","http://xxx.jpg");
  String json = jsonObject.toString();
  if (!TextUtils.isEmpty(json)){
      mWebview.loadUrl("javascript:uploadResult('" + json + "')");
  } else {
      upLoadFail("服务器返回错误",filePath);
  }
```

<h4>iOS传json字符串代码</h4>

```
  NSDictionary *dataDic = [model.result optDic:@"file"];
  NSData *data=[NSJSONSerialization dataWithJSONObject:dataDic options:NSJSONWritingPrettyPrinted error:nil];
  NSString *dataStr = [[NSString alloc]initWithData:data encoding:NSUTF8StringEncoding];
  if ([Common isEmpty:dataStr]) {
      NSString *msg = [NSString stringWithFormat:@"%@,是否重试？",@"上传出现未知错误"];
      [weakSelf showAlert:msg buttons:@[@"取消",@"重试"] handler:^(NSInteger buttonIndex) {
          if (1 == buttonIndex) {
              [weakSelf requestUploadWithData:image];
          }
      }];
      return;
  }
  NSString *jsCode = [NSString stringWithFormat:@"uploadResult(%@)",dataStr];
  [weakSelf.webView evaluateJavaScript:jsCode completionHandler:^(id _Nullable _id, NSError * _Nullable error) {
      NSLog(@"---%@",error);
  }];
```

<h4>前端的兼容处理</h4>

```
  var data;
  if (typeof jsonStr === 'string') {
    data = JSON.parse(jsonStr);
  } else {
    data = jsonStr;
  }
  this.needUpImages.push(data.large);
```

OK，完成！

-------------------------------
本文由darling_shadow完成，谢谢。
 
 
