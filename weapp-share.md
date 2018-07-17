## <center>小程序分享</center>

### 基础

#### 1. 语言与文件

  - WXML (WeiXin Markup language) 用于描述页面的结构
  - WXSS (WeiXin Style Sheet) 用于描述页面的样式
  - [WXS](https://developers.weixin.qq.com/miniprogram/dev/framework/view/wxs/) (WeiXin Script) 是小程序的一套脚本语言，结合 WXML，可以构建出页面的结构。
  - JS（JavaScript，小游戏的主体）

#### 2. [组件与 API](https://developers.weixin.qq.com/miniprogram/dev/component/)
  
  - 视图组件
  - 表单组件
  - 画布
  - 框架提供丰富的微信原生 API ，可以方便的调起微信提供的能力，如获取用户信息，本地存储，支付功能等。

#### 3. App 和 Page 的生命周期

（1）App() 函数用来注册一个小程序。接受一个 object 参数，其指定小程序的生命周期函数等。

  **全局的 getApp() 函数可以用来获取到小程序实例。**

| 属性 | 描述 | 触发时机 |
| :---: | :----: | :----: |
| onLaunch | 监听小程序初始化 | 当小程序初始化完成时，会触发 onLaunch（全局只触发一次） |
| onShow    | 监听小程序显示      | 当小程序启动，或从后台进入前台显示，会触发 onShow     |
| onHide    | 监听小程序隐藏      | 当小程序从前台进入后台，会触发 onHide     |
| onError    | 错误监听函数      | 当小程序发生脚本错误，或者 api 调用失败时，会触发 onError 并带上错误信息     |
| onPageNotFound    | 页面不存在监听函数      | 当小程序出现要打开的页面不存在的情况，会带上页面信息回调该函数     |

（2）Page() 函数用来注册一个页面。接受一个 object 参数，其指定页面的初始数据、生命周期函数、事件处理函数等。数据绑定

**object 内容在页面加载时会进行一次深拷贝，需考虑数据大小对页面加载的开销**

| 属性 | 描述 |
| :---: | :----: |
| onLoad    | 监听页面加载 |
| onShow    | 监听页面显示 |

------

##### 3.[模板](https://developers.weixin.qq.com/ebook?action=get_post_info&token=935589521&volumn=1&lang=zh_CN&book=miniprogram&docid=000ee2c29d4f805b0086a37a254c0a)与[引用](https://developers.weixin.qq.com/ebook?action=get_post_info&token=935589521&volumn=1&lang=zh_CN&book=miniprogram&docid=000ee2c29d4f805b0086a37a254c0a)

WXML 支持使用模板减少代码体积。模板是在 WXML 代码中对相同的代码进行复用的方式。WXML 提供模板（template），可以在模板中定义代码片段，然后在不同的地方调用。使用 name 属性，作为模板的名字。然后在  <template/\> 内定义代码片段

```
<template name="msgItem">
  <view>
    <text> {{index}}: {{msg}} </text>
    <text> Time: {{time}} </text>
  </view>
</template>


<template is="msgItem" data="{{...item}}"/>
```

WXML 提供两种文件引用方式 import 和 include。

import 可以在该文件中使用目标文件定义的 template

include 可以将目标文件中除了  <template/\> <wxs/\> 外的整个代码引入，相当于是拷贝到 include 位置

##### 4.用户操作与事件响应

由于微信使用的不是 HTML ，所以也不能通过添加超链接（a元素）的方式来检测用户的点击事件。

对于需要监听点击事件的元素，应该在 WXML 中使用 bindtap 属性或 catchtap 属性进行绑定。除了点击一次，微信也提供按住、开始触摸、松手等事件响应。在 WXML 中绑定好一个事件之后，就能在主程序中使用。其他的 API 中也有相应的事件，这些事件乐意在微信小程序的官方文档中查阅到。当需要在小程序的页面间进行跳转时，应该使用 wx.navigateTo() 方式。 

**注意，有关于页面层级跳转，微信将层级跳转限制在 10 层。在开发时一定注意不要超过了相应限制;
wx.navigateTo 和 wx.redirectTo 不允许跳转到 tabbar 页面，只能用 wx.switchTab 跳转到 tabbar 页面**

### 基础配套

####  &emsp;  快速开始

##### &emsp;&emsp;&emsp; [官方文档](https://developers.weixin.qq.com/miniprogram/dev/index.html)
   
##### &emsp;&emsp;&emsp; [项目设置](https://developers.weixin.qq.com/miniprogram/dev/devtools/project.html#%E9%A1%B9%E7%9B%AE%E8%AE%BE%E7%BD%AE)
   
##### &emsp;&emsp;&emsp; [资料大全](https://github.com/justjavac/awesome-wechat-weapp)
   
#### &emsp; 开发支持

##### &emsp;&emsp;&emsp; [开发工具](https://developers.weixin.qq.com/miniprogram/dev/devtools/download.html?t=201879)
##### &emsp;&emsp;&emsp; [开发者社区](https://developers.weixin.qq.com/)


#### 逻辑层

小程序开发框架的逻辑层由 JavaScript 编写。

逻辑层将数据进行处理后发送给视图层，同时接受视图层的事件反馈。

- 增加 [App](https://developers.weixin.qq.com/miniprogram/dev/framework/app-service/app.html) 和 [Page](https://developers.weixin.qq.com/miniprogram/dev/framework/app-service/page.html) 方法，进行程序和页面的注册。
    + Page() 函数用来注册一个页面。object 内容在页面加载时会进行一次深拷贝，需考虑数据大小对页面加载的开销
    
    + 初始化数据将作为页面的第一次渲染。data 将会以 JSON 的形式由逻辑层传至渲染层
- 增加 getApp 和 getCurrentPages 方法，分别用来获取 App 实例和当前页面栈。

- 提供丰富的 [API](https://developers.weixin.qq.com/miniprogram/dev/api/)，如微信用户数据，扫一扫，支付等微信特有能力

- 每个页面有独立的[作用域](https://developers.weixin.qq.com/miniprogram/dev/framework/app-service/module.html)，并提供模块化能力。

- 开发者写的所有代码最终将会打包成一份 JavaScript，并在小程序启动的时候运行，直到小程序销毁，所以逻辑层也称之为 App Service。

#### 视图层

- 框架的视图层由 WXML 与 WXSS 编写，由组件来进行展示。

- 将逻辑层的数据反应成视图，同时将视图层的事件发送给逻辑层。

- 组件(Component)是视图的基本组成单元。

 > 网页开发渲染线程和脚本线程是互斥的，这也是为什么长时间的脚本运行可能会导致页面失去响应，而在小程序中，二者是分开的，分别运行在不同的线程中。网页开发者可以使用到各种浏览器暴露出来的 DOM API，进行 DOM 选中和操作。而如上文所述，小程序的逻辑层和渲染层是分开的，逻辑层运行在 JSCore 中，并没有一个完整浏览器对象，因而缺少相关的 DOM API 和 BOM API。

小程序的 UI 视图和逻辑处理是用多个 webview 实现的，逻辑处理的 JS 代码全部加载到一个 Webview 里面，称之为 AppService，整个小程序只有一个，并且整个生命周期常驻内存，而所有的视图（ wxml 和 wxss）都是单独的Webview来承载，称之为 AppView。

小程序是一个多 WebView 的架构，每一个小程序页面都是不同的 WebView 渲染后显示的，在这个架构下不好去用某个 WebView 中 的 ServiceWorker 去管理所有的小程序页面。

可以创建一个单独的线程去执行 JavaScript，在这个环境下执行的都是有关小程序业务逻辑的代码，而界面渲染相关的任务全都在 WebView 线程里执行，通过逻辑层代码去控制渲染哪些界面，那么这一层当然就是所谓的渲染层。这就是小程序双线程模型的由来。

###[渲染层和逻辑层通信模型](https://developers.weixin.qq.com/ebook?action=get_post_info&token=935589521&volumn=1&lang=zh_CN&book=miniprogram&docid=0000286f908988db00866b85f5640a)

在渲染首屏的时候，逻辑层与渲染层会同时开始初始化工作，但是渲染层需要有逻辑层的数据才能把界面渲染出来，如果渲染层初始化工作较快完成，就要等逻辑层的指令才能进行下一步工作。因此逻辑层与渲染层需要有一定的机制保证时序正确，这些工作在小程序框架里会处理好，开发者只需要理解生命周期，以及控制合适的时机更新UI即可。

除了逻辑层与渲染层之间的通信有延时，各层与客户端原生交互同样是有延时的。以逻辑层为例，开发者的代码是跑在逻辑层这个线程之上，而客户端原生是跑在微信主线程（安卓上是线程）之上，所以注册给逻辑层有关客户端能力的接口，实际上也是跟微信主线程之间的通信，同样意味着有延时。这也是我们看到大部分提供的接口都是异步的原因。(wx.canvasToTempFilePath)

*Page 实例的生命周期：*

![Page 生命周期](https://mp.weixin.qq.com/debug/wxadoc/dev/image/mina-lifecycle.png?t=201879)


#### [分包加载](https://developers.weixin.qq.com/miniprogram/dev/framework/subpackages.html)

![](https://upload-images.jianshu.io/upload_images/7101063-21c1aeb9c9c1c5fc.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 将小程序中不经常使用的页面放到多个分包中，主包只保留最常用的核心代码
- 启动时只加载朱保，使用时按需加载分包，不需要一次性下载整个代码包

![启动流程](https://upload-images.jianshu.io/upload_images/7101063-d475e57ee0affc64.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![耗时](https://upload-images.jianshu.io/upload_images/7101063-b37c9edad79c9fb5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/7101063-7c2e5cb97c3b1e36.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/7101063-a2b5b6d3699b721b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/7101063-133f565b334046a3.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### setData

##### 工作原理

小程序的视图层目前使用 WebView 作为渲染载体，而逻辑层是由独立的 JavascriptCore 作为运行环境。在架构上，WebView 和 JavascriptCore 都是独立的模块，并不具备数据直接共享的通道。当前，视图层和逻辑层的数据传输，实际上通过两边提供的 evaluateJavascript 所实现。即用户传输的数据，需要将其转换为字符串形式传递，同时把转换后的数据内容拼接成一份 JS 脚本，再通过执行 JS 脚本的形式传递到两边独立环境。

而 evaluateJavascript 的执行会受很多方面的影响，数据到达视图层并不是实时的。

##### 常见的 setData 操作错误

1. 频繁的去 setData

- Android 下用户在滑动时会感觉到卡顿，操作反馈延迟严重，因为 JS 线程一直在编译执行渲染，未能及时将用户操作事件传递到逻辑层，逻辑层亦无法及时将操作处理结果及时传递到视图层

- 渲染有出现延时，由于 WebView 的 JS 线程一直处于忙碌状态，逻辑层到页面层的通信耗时上升，视图层收到的数据消息时距离发出时间已经过去了几百毫秒，渲染的结果并不实时

2. 每次 setData 都传递大量新数据

由 setData 的底层实现可知，我们的数据传输实际是一次 evaluateJavascript 脚本过程，当数据量过大时会增加脚本的编译执行时间，占用 WebView JS 线程。

页面的data数据会涉及相当多的字段，你并不需要每次都将整个data字段重新设置一遍，你只需要把改变的值进行设置即可，宿主环境会自动把新改动的字段合并到渲染层对应的字段中，如下代码所示。data中的key还可以非常灵活，以数据路径的形式给出，例如 this.setData({"d[0]": 100}); this.setData({"d[1].text": 'Goodbye'}); 我们只要保持一个原则就可以提高小程序的渲染性能：每次只设置需要改变的最小单位数据。

3. 后台态页面进行 setData

当页面进入后台态（用户不可见），不应该继续去进行setData，后台态页面的渲染用户是无法感受的，另外后台态页面去setData也会抢占前台页面的执行。

##### 图片对页面切换的影响

大图片和长列表图片的使用会引起 WKWebView 的回收

有一部分小程序会在页面中引用大图片，在页面后退切换中会出现掉帧卡顿的情况。

当前我们建议开发者尽量减少使用大图片资源。

##### 代码包大小的优化

1. 控制代码包内图片资源

小程序代码包经过编译后，会放在微信的 CDN 上供用户下载，CDN 开启了 GZIP 压缩，所以用户下载的是压缩后的 GZIP 包，其大小比代码包原体积会更小。不同小程序之间的代码包压缩比差异也挺大的。

2. 及时清理没有使用到的代码和资源

目前小程序打包是会将工程下所有文件都打入代码包内，也就是说，这些没有被实际使用到的库文件和资源也会被打入到代码包里，从而影响到整体代码包的大小


#### 小程序登录

![登录流程时序](https://mp.weixin.qq.com/debug/wxadoc/dev/image/api-login.jpg?t=2018712)
