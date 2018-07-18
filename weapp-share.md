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

（2）Page() 函数用来注册一个页面。接受一个 object 参数，其指定页面的初始数据、生命周期函数、事件处理函数等。每个页面有独立的[作用域](https://developers.weixin.qq.com/miniprogram/dev/framework/app-service/module.html)，并提供模块化能力。


**object 内容在页面加载时会进行一次深拷贝，需考虑数据大小对页面加载的开销**

| 属性 | 描述 |
| :---: | :----: |
| onLoad    | 监听页面加载 |
| onShow    | 监听页面显示 |

#### 4. 渲染层和逻辑层

```
// wxml
<view>{{ msg }}</view>

// js
Page({
  onLoad: function () {
    this.setData({ msg: 'Hello World' })
  }
})
```

小程序的 UI 视图和逻辑处理是用多个 webview 实现的，逻辑处理的 JS 代码全部加载到一个 Webview 里面，称之为 AppService，整个小程序只有一个，并且整个生命周期常驻内存，而所有的视图（ wxml 和 wxss）都是单独的Webview来承载，称之为 AppView。

####[通信模型](https://developers.weixin.qq.com/ebook?action=get_post_info&token=935589521&volumn=1&lang=zh_CN&book=miniprogram&docid=0000286f908988db00866b85f5640a)

在渲染首屏的时候，逻辑层与渲染层会同时开始初始化工作，但是渲染层需要有逻辑层的数据才能把界面渲染出来，如果渲染层初始化工作较快完成，就要等逻辑层的指令才能进行下一步工作。因此逻辑层与渲染层需要有一定的机制保证时序正确，这些工作在小程序框架里会处理好，开发者只需要理解生命周期，以及控制合适的时机更新UI即可。

除了逻辑层与渲染层之间的通信有延时，各层与客户端原生交互同样是有延时的。以逻辑层为例，开发者的代码是跑在逻辑层这个线程之上，而客户端原生是跑在微信主线程（安卓上是线程）之上，所以注册给逻辑层有关客户端能力的接口，实际上也是跟微信主线程之间的通信，同样意味着有延时。这也是我们看到大部分提供的接口都是异步的原因。

Page 生命周期

![](https://camo.githubusercontent.com/94a3c070e9bc2a760b7097280b44f4e90bd52e25/68747470733a2f2f6d702e77656978696e2e71712e636f6d2f64656275672f777861646f632f6465762f696d6167652f6d696e612d6c6966656379636c652e706e673f743d323031383739)

---

### 性能优化

#### 1. 启动加载性能

![启动流程](https://upload-images.jianshu.io/upload_images/7101063-d475e57ee0affc64.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![耗时](https://upload-images.jianshu.io/upload_images/7101063-b37c9edad79c9fb5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![初始化流程](https://upload-images.jianshu.io/upload_images/7101063-7c2e5cb97c3b1e36.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

（1）控制代码包内图片资源

小程序代码包经过编译后，会放在微信的 CDN 上供用户下载，CDN 开启了 GZIP 压缩，所以用户下载的是压缩后的 GZIP 包，其大小比代码包原体积会更小。不同小程序之间的代码包压缩比差异也挺大的。

（2）及时清理没有使用到的代码和资源

目前小程序打包是会将工程下所有文件都打入代码包内，也就是说，这些没有被实际使用到的库文件和资源也会被打入到代码包里，从而影响到整体代码包的大小

（3）[分包加载](https://developers.weixin.qq.com/miniprogram/dev/framework/subpackages.html)

![分包加载](https://upload-images.jianshu.io/upload_images/7101063-8d7262f0056a04a8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将小程序中不经常使用的页面放到多个分包中，主包只保留最常用的核心代码
启动时只加载主包，使用时按需加载分包，不需要一次性下载整个代码包

![分包预下载](https://upload-images.jianshu.io/upload_images/7101063-a2b5b6d3699b721b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![独立分包](https://upload-images.jianshu.io/upload_images/7101063-133f565b334046a3.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 2. 渲染性能

视图层和逻辑层的数据传输，实际上通过两边提供的 evaluateJavascript 所实现。即用户传输的数据，需要将其转换为字符串形式传递，同时把转换后的数据内容拼接成一份 JS 脚本，再通过执行 JS 脚本的形式传递到两边独立环境。

（1）频繁的去 setData

- 操作卡顿，交互延迟：JS 线程一直在编译执行渲染，未能及时将用户操作事件传递到逻辑层，逻辑层亦无法及时将操作处理结果及时传递到视图层
- 阻塞通信，页面渲染延迟：由于 WebView 的 JS 线程一直处于忙碌状态，逻辑层到页面层的通信耗时上升，视图层收到的数据消息时距离发出时间已经过去了几百毫秒，渲染的结果并不实时

优化：避免不必要的 setData；对连续的 setData 调用进行合并

（2）使用 setData 传输大量数据

- 造成耗时与数据量正相关，页面更新延迟，可能造成页面更新开销增加

优化：使用 setData 的特殊 key 实现局部更新；每次只设置需要改变的最小单位数据

```
this.setData({"d[0]": 100});
this.setData({"d[1].text": 'Goodbye'}); 
```

（3）页面 onHide （后台态页面）进行 setData

- 这种情况下不应该继续去进行 setData，后台态页面的渲染用户是无法感受的，另外后台态页面去 setData 也会抢占前台页面的执行。

优化：页面切入后台的 setData 调用，延迟到页面重新展示时执行

总结：每次 setData 的调用都是一次进程间通信过程，通信开销与 setData 的数量正相关。

（4）避免不当使用 onPageScroll

![onPageScroll](https://upload-images.jianshu.io/upload_images/7101063-229a606b9144fb7d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 只有必要的时候监听 pageScroll 事件
- 避免在 onPageScroll 中执行复杂逻辑
- 避免在 onPageScroll 中频繁调用 setData
- 避免频繁查询节点信息（SelectQuery），部分场景建议使用节点布局相交状态监听（IntersectionObserver）替代

（5）使用自定义组件

- 在需要频繁更新的场景下，自定义组件的更新只在组件内部进行，不受页面其他部分内容复杂性的影响。（秒杀，定时器）
- 减少代码量，提高代码的复用性。

（6）其他

- 首屏体验：提前请求，利用缓存，避免白屏，及时反馈

---

### 实际开发中遇到问题

#### 1. 小程序兼容问题

![iphoneX](https://upload-images.jianshu.io/upload_images/7101063-59b020a704251592.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

（1）border-radius 设定后在 iphoneX 中元素的边框显示不全

解决方案：只需要把 rpx 改成 px 即可。

（2）iphoneX 中 view 设定 padding-left 在手机中有偏差

```
// wxml
<view class="com-lab ">
      <span>运费</span>      
</view>
<view class="sel-box">
      分类       
</view>

// style
.com-lab span{
  padding-left:30rpx;
}
.sel-box{
  padding-left:30rpx
}
```

修改方式：

```
.com-lab{ padding-left:30rpx; } 
.sel-box{ padding-left:30rpx }
```


（3）iphoneX 适配微信底部操作区问题

所以需要对底部的黑色做处理，否则会遮挡住文字

```
// js
if(this.globalData.systemInfo.model.toLowerCase().indexOf('iphone x') != -1) {
  that.globalData.isIpx = true  //判断是否为iPhone X 默认为值false，iPhone X 值为true
}

// wxss
.fix-ipx-tabbar-bottom {
  bottom: 66rpx；
}

.fix-ipx-tabbar-bottom::after {
  content: '';
  position: fixed;
  bottom: 0rpx;
  height: 66rpx;
  width: 100%;
  background: #FFF;
}
```

#### 2. 小程序组件问题

（1）文本输入常用的标签 input、textarea

- 当页面有遮罩层时，无法遮盖 textarea 的文字内容。
- 修改 textarea、或者 input 里面的文本内容，如果在文本中修改，光标会跑到最后面。
- hidden 属性对 textarea 无效，只有使用 wx:if

（2）页面快速点击可以重复触发

解决方案：

```
globalLastTapTime:0,
preventMoreTap:function(e){
      var globalTime = this.globalLastTapTime;
      var time = e.timeStamp;
      if(Math.abs(time-globalTime) < 500 && globaTime != 0) {
        this.globalLastTapTime = time; 
        return true;
      } else{
        this.globalLastTapTime = time; 
        return false;
      }     
 }

 // js
let app = getApp();
Page({
   xxx:function(e){
     if(app.preventMoreTap(e)) {
        return ;
     }
     //跳转
   }
})
```


（3）页面间重复跳转几次之后锁死

使用 navigateTo 做页面跳转，只能跳转10次

- 通过 navigateTo 或 redirecTo，在 url 里面传递
- 把变动的参数放到缓存中，然后更新缓存。这种方法显然不好，缓存中会有多个参数。
- 通过 getCurrentPages() 获取一个数组对象取上个页面的序列然后使用 setData() 方法

```
var pages = getCurrentPages();
var prevPage = pages[pages.length - 2];
prevPage.setData({})
wx.navigateBack()
```

（4）canvas 绘制

- ctx.draw() 之后并不能直接使用 wx.canvasToTempFilePath [天生延时](https://developers.weixin.qq.com/ebook?action=get_post_info&token=935589521&volumn=1&lang=zh_CN&book=miniprogram&docid=0006a2289c8bb0bb0086ee8c056c0a)
- ctx.createLinearGradient 在 iOS 下会导致绘制之后的画布崩溃
- 绘制成功后，将画布导出本地临时路径，用 setData 设置之后，Android 下第二次直接预览不能成功

------

### [mpvue](https://github.com/Meituan-Dianping/mpvue)、[wepy](https://github.com/Tencent/wepy) 和 [Taro](http://github.com/nervjs/taro)

| | 微信小程序 | mpvue | wepy | Taro |
| :---: | :---: | :---: | :----: | :----: |
| 语法规范 | 小程序规范 | vue.js 规范 | 类vue.js 规范  | React 规范 |
| 模板系统 | 字符串模板 | 字符串模板 | 字符串模板  | JSX |
| 类型系统 | 不支持 | 业务代码 | 业务代码  | 业务代码 + JSX 模板 |
| 组件规范 | 小程序组件 | 小程序组件 + html 标签 | 小程序组件  | 小程序组件 |
| 样式规范 | wxss | less,sass,postcss | less,sass,styus  | less,sass,postcss |
| 多端复用 | 无 | 复用为 H5 | 复用为 H5  | 复用为 H5 |
| 自动构建 | 无 | webpack 构建 | 内建构建系统  | 内建构建系统 + webpack |
| 上手成本 | 全新学习 | 熟悉 Vue.js | 熟悉 Vue.js + wepy  | 熟悉 React |
| 数据流管理 | 不支持 | Vuex | Redux  | Redux |



---

### 学习

[小程序资料](https://github.com/justjavac/awesome-wechat-weapp)

[从源码看微信小程序启动过程](https://tech.youzan.com/weapp-booting/)



