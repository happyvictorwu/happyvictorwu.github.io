---
title: App界面 - WebKit
date: 2020-03-03 19:14:23
categories:
- [iOS, 基础, WebKit]
tags:
- 编程
---

# App界面 - WebKit
一句话作用：把网页url加载到手机上。



## Web基础

![](https://raw.githubusercontent.com/happy-yuxuan/picgo/master/img/15655779413945.jpg)



### UIWebView

iOS12后弃用。
内存系统泄漏，稳定差，WebCore和JSCore Crash较多，对HTML5和CSS3支持较少。



### WebKit

* 独立进程，内存。 Crash不影响主App，更多更友好的HTML和CSS支持，采用JIT技术

* 是一个开源的Web浏览器引擎
* iOS中的WebKit.framework就是在WebCore、底层桥接、JSCore引擎等核心模块的基础上，针对iOS平台的项目封装。



### WKWebView

```objc
// 在view中添加一个webView
[self.view addSubview:({
	self.webView = [[WKWebView alloc] initWithFrame:CGRectMake(...)];
    self.webview;
})];

// webView加载网页
[self.webView loadRequest:[NSURLRequest requestWithURL:[NSURL URLWithString:@"https://happy-yuxuan.github.io"]]];
```

使用流程：

1. 创建WKWebView
2. 设置Delegate以及样式、JS注入等
3. 加载URL或HTML字符串
4. 在相应的回调中处理业务逻辑



## 常用代理方法 

![](https://raw.githubusercontent.com/happy-yuxuan/picgo/master/img/15717295162779.jpg)

WKNavigationDelegate**  代理方法，如加载的进度等信息

* `decidePolicyForNavigationAction` 是否加载请求
* `didFinishNavigation` 完成加载
* `didFailNavigation` 失败加载
* `webViewWebContentProcessDidTerminate` webView Crash回调




### 作为观察者被监听 （KVO）
```objc
/* 注册监听
- self作为监听者，接受事件
- 监听 self.webview 的 estimatedProgress 属性
- 在NSKeyValueObservingOptionNew的时候发通知
*/
[self.webview addObserver:self
    		   forKeyPath:@"estimatedProgress"
    			  options:NSKeyValueObservingOptionNew
    			  context:nil];

// 移除监听
[self.webview removeObserver:self forKeyPath:@"estimatedProgress"];


/* 接受通知
- change 对应着上面的options
*/
- （void)observerValueForKeyPath:(nullable NSString *)keyPath
    				    ofObject:(nullable id)object
        change:(nullable NSDictionary<NSKeyValueChangeKey, id> *)change
        				 context:(nullable void *)context {
	// 业务逻辑
}
```

见代码： (设置对网络请求WebView的属性estimatedProgress的监听)

1. `self.webview` 添加一个观察这self去监听自己的"estimatedProgress"属性的变化。 `option`是变化什么时候发送通知
2. 接收通知：观察者`self`需要实现对应接收通知的的方法。
3. 移除通知：在观察者被销毁的时候需要移除监听！



## iOS中的Web应用

* JavaScriptCore 是 JavaScript和Native通信
* 跨平台 & 热修复
* 业务逻辑 (JS open Api) (模板引擎)