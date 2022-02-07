[toc]

# Day 03--视图与逻辑

## 1 页面导航

### 1.1 什么是页面导航

页面导航是指页面之间相互跳转

### 1.2 小程序中实现页面跳转

1. 声明式导航
   - 在页面上声明一个 `<navigator>` 导航组件
   - 通过点击 `<navigator>` 组件实现页面跳转
2. 编程式导航
   - 调用小程序 API ， 实现页面跳转

### 1.3 声明式导航

#### 1.3.1 导航到 tabBar 页面

tabBar 页面是指被配置为 tabBar的页面

在使用 \<navigator> 组件跳转到指定 tabBar 页面时，需要指定 url 和 open-type 属性，其中：

- url 表示要跳转的页面内的地址，必须以 / 开头
- open-type 表示跳转的方式，必须为 switchTab 

```html
<navigator url="/pages/message/message" open-type="switchTab">导航到消息页面</navigator>
```

#### 1.3.2 导航到非 tabBar 页面

非 tabBar 页面指没有配置为 tabBar 页面，方式同上，open-type 属性值为 navigate

```html
<navigator url="/pages/info/info" open-type="navigate">导航到info页面</navigator>
```

为了简便，在导航到非 tabBar 页面时，`open-type="navigate"` 可以省略

#### 1.3.3 后退导航

如果要后退到上一级页面或多级页面，则需要指定 open-type 属性和 delta 属性，其中：

- open-type 属性的值必须时navigateBack， 表示要进行后退导航
- delta 的值必须是数字，表示要后退的层级

```html
<navigator open-type="navigateBack" delta="1">后退</navigator>
```

为了简便，如果只需要后退一个页面，可以省略 delta 属性

### 1.4 编程式导航

#### 1.4.1 导航到 tabBar 页面

调用 `wx.switchTab(Object object)` 方法，可以跳转到 tabBar 页面。其中 Object 参数对象的属性列表如下：

| 属性 |     类型     | 是否必选 |                        说明                        |      |
| :------: | :------: | :--------: | :----------: | ----------------------------------------------------- |
| ***url*** | ***string*** | ***是*** | 需要跳转到的非 tabBar 页面的路径，路径后可以带参数 |      |
|  success  |   function   |    否    |               接口调用成功的回调函数               |      |
|   fail    |   function   |    否    |               接口调用失败的回调函数               |      |
| complete  |   function   |    否    |  接口调用结束的回调函数（调用成功、失败都会执行）  |      |

```html
<button bindtap="gotoMessage">跳转到 message 页面</button>
```

```js
// 通过编程式导航跳转
gotoMessage(){
  wx.switchTab({
    url: '/pages/message/message',
  })
}
```

#### 1.4.2 导航到非 tabBar 页面

调用 `wx.navigateTo(Object object)` 方法，可以跳转到非 tabBar 的页面。其中 Object 参数对象的属性列表如下：

| **属性** | **类型** | 是否必选 |                      **说明**                      |
| :------: | :------: | :------: | :------------------------------------------------: |
|   url    |  string  |    是    | 需要跳转到的非 tabBar 页面的路径，路径后可以带参数 |
| success  | function |    否    |               接口调用成功的回调函数               |
|   fail   | function |    否    |               接口调用失败的回调函数               |
| complete | function |    否    |  接口调用结束的回调函数（调用成功、失败都会执行）  |

```html
<button bindtap="gotoInfo">跳转到 info 页面</button>
```

```js
gotoInfo(){
  wx.navigateTo({
    url: '/pages/info/info',
  })
}
```



#### 1.4.3 后退导航

调用 `wx.navigateBack(Object object)` 方法，可以返回上一页面或多级页面。其中 Object 参数对象的属性列表如下：

| **属性** | **类型** | 默认值 | 是否必选 |                       **说明**                        |
| :------: | :------: | :----: | :------: | :---------------------------------------------------: |
|  delta   |  number  |   1    |    否    | 返回的页面数，如果 delta 大于现有页面数，则返回到首页 |
| success  | function |        |    否    |                接口调用成功的回调函数                 |
|   fail   | function |        |    否    |                接口调用失败的回调函数                 |
| complete | function |        |    否    |   接口调用结束的回调函数（调用成功、失败都会执行）    |

```html
<button bindtap="goBack">后退</button>
```

```js
goBack(){
  wx.navigateBack({
    delta: 1,
  })
}
```

### 1.5 导航传参

#### 1.5.1 声明式导航传参

navigator 组件的 url 属性用来指定将要跳转的页面的额路径。同时，路径的后面还可以携带参数：

- 参数和路径之间用 ？ 分隔
- 参数键与参数值用 = 相连
- 不同参数用 & 分隔

```html
<navigator url="/pages/info/info?name=zs&age=20">跳转到info</navigator>
```

#### 1.5.2 编程式导航 传参

调用 `wx.navigateTo(Object object)` 方法跳转页面时，也可以携带参数

```html
<button bindtap="gotoInfo2">跳转到info</button>
```

```js
gotoInfo2(){
  wx.navigateTo({
    url: '/pages/info/info?name=ls&gender=男',
  })
}
```

#### 1.5.2 在onLoad 中接收导航参数

通过声明式导航传参或编程式导航传参所携带的参数，可以直接在 onLoad 事件中直接获取到

## 2 页面事件

### 2.1 下拉刷新事件

#### 2.1.1 什么是下拉刷新事件

下拉刷新时移动端的专有名词，一种重新刷新页面数据的行为

#### 2.1.2 启用下拉刷新效果

1. 全局开启下拉刷新

2. 局部开启下拉刷新（推荐）

   在页面的 .json 配置文件中，将 enablePullDownRefresh 设置为 true

#### 2.1.3 配置下拉刷新样式

- backgroundColor 用来配置下拉刷新窗口的背景颜色，仅支持16 进制的颜色值

- backgroundTextStyle 用来配置下拉刷新 loading 的样式，仅支持 dark 和 light

#### 2.1.4 监听页面下拉刷新事件

在页面 js 文件中，通过 `onPullDownRefresh()` 函数即可监听当前页面的下拉刷新事件

```html
<view>count:{{count}}</view>
<button bindtap="addCount">+1</button>
```

```js
data: {
  count: 0
},
addCount() {
  this.setData({
  count: this.data.count + 1
})},
onPullDownRefresh: function () {
  console.log("ok");
  this.setData({
    count: 0
  })
}
```

#### 2.1.5 停止下拉刷新效果

调用 `wx.stopPullDownRefresh()` 即可

### 2.2 上拉触底

#### 2.2.1 什么是上拉触底事件

上拉触底是移动端的专有名词，通过手指在屏幕上的上拉滑动操作，从而**加载更多数据**的行为。

#### 2.2.2 监听页面的上拉触底事件

在页面的 js 文件中通过 onReachBottom() 事件即可监听当前页面上的上拉触底事件

```js
onReachBottom: function () {
  console.log("ok");
}
```

#### 2.2.3 配置上拉触底距离

可以在全局或页面的 .json 配置文件中，通过 `onReachBottomDistance` 属性来配置上拉触底的距离。

小程序默认的触底距离是 50px，在实际开发中，可以根据自己的需求修改这个默认值。

#### 2.2.4 案例 - 上拉触底

具体步骤：

1. 定义获取随机颜色的方法

2. 在页面加载时获取初始数据

3. 渲染 UI 结构并美化页面效果

4. 在上拉触底时调用获取随机颜色的方法

5. 添加 loading 提示效果

6. 对上拉触底进行节流处理

实现代码：

1. wxml

   ```html
   <!--pages/contact/contact.wxml-->
   <text>pages/contact/contact.wxml</text>
   <view wx:for="{{colorList}}" wx:key="index" class="num-item" style="background-color: rgba({{item}});">{{item}}</view>
   ```

2. wxss

   ```css
   /* pages/contact/contact.wxss */
   
   .num-item{
     border: 1rpx solid #efefef;
     border-radius: 8rpx;
     line-height: 200rpx;
     margin: 15rpx;
     text-align: center;
     text-shadow: 0rpx 0rpx 5rpx #fff;
     box-shadow: 1rpx 1rpx 6rpx #efefef;
   }
   ```

3. js

   ```js
   // pages/contact/contact.js
   Page({
   
     /**
      * 页面的初始数据
      */
     data: {
       colorList: [],
       // 定义节流阀
       isloading: false
     },
   
     /**
      * 生命周期函数--监听页面加载
      */
     onLoad: function (options) {
       this.getColor()
     },
     getColor() {
       this.setData({
         isloading: true
       })
       // 显示 loading 效果
       wx.showLoading({
         title: '数据加载中...',
       })
       wx.request({
         url: 'https://www.escook.cn/api/color',
         method: "GET",
         success: ({
           data: res
         }) => {
           console.log(res);
           this.setData({
             colorList: [...this.data.colorList, ...res.data]
           })
         },
         complete: () => {
           wx.hideLoading({})
   
           this.setData({
             isloading: false
           })
         }
       })
     },
     /**
      * 页面上拉触底事件的处理函数
      */
     onReachBottom: function () {
       if (this.data.isloading) return
       this.getColor()
     }
   })
   ```

## 3 生命周期

### 3.1 什么是生命周期

生命周期（Life Cycle）是指一个对象从创建 -> 运行 -> 销毁的整个阶段，强调的是一个时间段。

我们可以把每个小程序运行的过程，也概括为生命周期：

- 小程序的启动，表示生命周期的开始
- 小程序的关闭，表示生命周期的结束
- 中间小程序运行的过程，就是小程序的生命周期

### 3.2 小程序生命周期分类

1. 应用生命周期

   从小程序启动 -> 运行 -> 销毁的过程

2. 页面生命周期

   在小程序中，页面的加载 -> 渲染 -> 销毁的过程

### 3.3 什么是生命周期函数

小程序框架提供的内置函数，会伴随着生命周期，自动按次序执行

作用：允许程序员在特定的时间点，执行某些特定的动作。

生命周期强调的是时间段，生命周期函数强调的是时间点

### 3.4 生命周期函数分类

1. 应用的生命周期函数
2. 页面的生命周期函数

### 3.5 应用的生命周期函数

在 app.js 文件中进行声明。

```js
// app.js
App({
  /**
   * 当小程序初始化完成时，会触发 onLaunch（全局只触发一次）
   */
  onLaunch: function () { },
  /**
   * 当小程序启动，或从后台进入前台显示，会触发 onShow
   */
  onShow: function (options) { },
  /**
   * 当小程序从前台进入后台，会触发 onHide
   */
  onHide: function () { },
  /**
   * 当小程序发生脚本错误，或者 api 调用失败时，会触发 onError 并带上错误信息
   */
  onError: function (msg) { }
})
```

### 3.6 页面生命周期函数

```js
Pages({
  /**
   * 生命周期函数--监听页面加载
   */
  onLoad: function (options) { },
  /**
   * 生命周期函数--监听页面初次渲染完成
   */
  onReady: function () { },
  /**
   * 生命周期函数--监听页面显示
   */
  onShow: function () { },
  /**
   * 生命周期函数--监听页面隐藏
   */
  onHide: function () { },
  /**
   * 生命周期函数--监听页面卸载
   */
  onUnload: function () { }
})
```

## 4 WXS 脚本

### 4.1 什么是 WXS 

WXS 是小程序独有的一套脚本语言， 结合 WXML，可以构建出页面结构

### 4.2 wxs 的应用场景

wxml 中无法调用在页面的 .js 中定义的函数，但是，wxml 中可以调用 wxs 中定义的函数。因此，小程序中 wxs 的典型应用场景就是“过滤器”。

### 4.3 wxs 和 JavaScript 的区别*

1. wxs 有自己的数据类型

   - number 数值类型、string 字符串类型、boolean 布尔类型、object 对象类型
   - function 函数类型、array 数组类型、  date 日期类型、   regexp 正则

2. wxs 不支持类似于 ES6 及以上的语法形式

   - 不支持：let、const、解构赋值、展开运算符、箭头函数、对象属性简写、etc...

   - 支持：var 定义变量、普通 function 函数等类似于 ES5 的语法

3. wxs 遵循 CommonJS 规范

   - module 对象

   - require() 函数

   - module.exports 对象

### 4.4 wxs 基础语法

#### 4.4.1 内嵌 wxs 脚本

wxs 代码可以编写在 wxml 文件中的 \<wxs> 标签内，就像 Javascript 代码可以编写在 html 文件中的 \<script> 标签内一样

wxml 文件中的每个 \<wxs>\</wxs> 标签，必须提供 module 属性，用来指定当前 wxs 的模块名称，方便在 wxml 中访问模块中的成员

```html
<view>{{m1.toUpper(username)}}</view>
<wxs module="m1">
  module.exports.toUpper = function (str) {
    return str.toUpperCase()
  }
</wxs>
```

#### 4.4.2 定义外联的 wxs 脚本

wxs 代码还可以编写在以 .wxs 为后缀名的文件内，就像 javascript 代码可以编写在以 .js 为后缀名的文件中一样

```js
function toLower(str) {
  return  str.toLowerCase()
}
module.exports={
  toLower:toLower
}
```

#### 4.4.3 使用 wxs 脚本

在 wxml 中引入外联的 wxs 脚本时，必须为 <wxs> 标签添加 module 和 src 属性，其中：

- module 用来指定模块的名称

- src 用来指定要引入的脚本的路径，且必须是相对路径

```html
<view>{{m2.toLower(country)}}</view>

<wxs src='../../utils/tool.wxs' module="m2"></wxs>
```

### 4.5 WXS 的特点

#### 4.5.1 与 JavaScript 不同

为了降低 wxs（WeiXin Script）的学习成本， wxs 语言在设计时借大量鉴了 JavaScript 的语法。但是本质上，wxs 和 JavaScript 是完全不同的两种语言！

#### 4.5.2 不能作为组件的事件回调

wxs 典型的应用场景就是“过滤器”，经常配合 Mustache 语法进行使用

但是，在 wxs 中定义的函数不能作为组件的事件回调函数

#### 4.5.3 隔离性

隔离性指的是 wxs 的运行环境和其他 JavaScript 代码是隔离的。体现在如下两方面：

1. wxs 不能调用 js 中定义的函数

2. wxs 不能调用小程序提供的 API

#### 4.5.4 性能好

- 在 iOS 设备上，小程序内的 WXS 会比 JavaScript 代码快 2 ~ 20 倍

- 在 android 设备上，二者的运行效率无差异

## 5 案例 - 本地生活（列表页面）

已完成
