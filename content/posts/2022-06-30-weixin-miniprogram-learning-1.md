---
title: 微信小程序开发学习 (一)
date: 2022-06-30
categories: Programming
tags: [miniprogram]
---

# 小程序开发与普通web开发的区别

由于小程序运行在微信环境中，所以不能调用浏览器的DOM、BOM的API，但是可以调用微信提供的各种API，如地理位置、扫码、支付等……

# 小程序项目的基本组成结构

![image-20220629172613244](https://p.ipic.vip/b7p4bd.png)

- pages：用来存放小程序的所有页面
- utils：用来存放工具性质的模块
- app.js：小程序项目的入口文件
- app.json：小程序项目的全局配置文件
- app.wxss：小程序项目的全局样式文件
- project.config.json：项目的配置文件
- sitemap.json：用来配置小程序及其他页面是否允许被微信索引（SEO）

# 页面的四大文件

每个页面以文件夹的形式存在于项目的pages文件夹中，其中包含了四大文件。

wxml：相当于HTML文件。但标签名、属性名都有所不同，也提供了类似Vue的模板语法。

wxss：相当于css文件，但新增了rpx的尺寸单位，提供了全局样式和局部样式，同时仅支持部分css选择器。

js：app.js是项目的入口文件，页面的.js文件是页面的入口文件，其他普通的js文件则用来封装公共的函数或属性供页面使用。

json：本页面的配置文件，若与app.json冲突，则会覆盖app.json的相关配置项。

# app.json

是小程序项目的全局配置文件。常用配置项有`pages`、`window`、`tabBar`、`style`。

pages记录了当前小程序所有页面的存放路径。pages数组中的第一个页面将会被当做小程序的首页进行渲染。若要增加项目pages文件夹里面的页面，只需要在app.json的pages数组中新增页面的路径，开发者工具会自动帮我们在项目pages文件夹中新建好页面相关的四个文件。



window可以全局设置小程序窗口的外观，即navigationBar和background部分。

![image-20220704134451240](https://p.ipic.vip/hwcax9.png)

window对象中的常用的配置项：

![image-20220704134808115](https://p.ipic.vip/x109cf.png)



tabBar可以设置小程序底部的tabBar效果，用以实现多页面的快速切换。tabBar分为底部和顶部两种，当渲染顶部tabBar时不显示icon，只显示文本。且最少2个最多5个页签。

![image-20220704140857735](https://p.ipic.vip/q7l8ae.png)

tabBar对象中的常用配置项：

![image-20220704141209277](https://p.ipic.vip/rbvb3e.png)

而每个tab页签的配置项，即list数组中每个对象的属性有：

![image-20220704141356363](https://p.ipic.vip/rnsv99.png)

> 作为tabBar的页面在pages配置项中需要放在最前面，否则无法渲染出来。



style控制是否启用新版的组件样式。默认为v2，即开启新版的组件样式。若不需要可删除。

# 页面的json文件

如果某些页面想要拥有特殊的窗口表现，就可以在页面的json文件中配置，与app.json中冲突的部分会覆盖其全局配置。

# 小程序的宿主环境

小程序中的通信主体是渲染层和逻辑层，其中wxml和wxss工作在渲染层，而js脚本工作在逻辑层。两者通过微信客户端进行转发，同时逻辑层与第三方服务器之间的通信也通过微信客户端进行转发。

小程序提供了九种常用组件，包括普通视图`view`、滚动视图`scroll-view`、按钮`button`、图片`image`、轮播图`swiper`和`swiper-item`、文本`text`和富文本`rich-text`等。

小程序提供了三大类的API，事件监听API、同步API、异步API。事件监听API以`on`开头。同步API以`Sync`结尾，其执行结果可以通过函数的返回值直接获取，异常则抛出错误。异步API则需要通过`success`、`fail`、`complete`回调接收调用的结果。

# wxml的模板语法

数据绑定使用Mustache语法，先在页面js文件的`data`配置项中定义数据，然后在wxml中使用双大括号包裹要使用的数据名称，即可实现绑定。wxml中使用Mustache语法既可以绑定内容，也可以绑定属性，还可以进行运算，如三元运算、算术运算等。

> 在Vue中Mustache不可以绑定属性值，需要使用v-bind，而小程序可以。



事件绑定。小程序中常用的事件有：`bindtap`轻点、`bindinput`文本输入、`bindchange`状态改变。事件对象event常用的属性有：target触发事件的源头组件的属性值集合、detail额外的信息。event身上还有其他很多属性。

# this.setData

函数中使用`this.setData`可以修改data配置项中的数据：

```js
data: {
  count: 0
},

btnHandler() {
  this.setData({
    count: this.data.count + 1
  })
}
```

# 事件传参

wxml中不能在绑定事件的同时在小括号中传参。而要使用`data-*`自定义属性来传参，其中`*`代表参数的名字，属性值即为参数。

```html
<button bindtap="btnHandler" data-info="{{2}}">事件传参</button>
```

js中接收事件传递的参数，需要使用事件对象中的`e.target.dataset.*`。

```js
btnHandler(e) {
  this.setData({
    count: this.data.count + e.target.dataset.info
  })
}
```

# bindinput

使用bindinput事件来响应文本框的输入事件。

```html
<input bindinput="inputHandler"><input>
```

在js中使用`e.detail.value`接收输入的文本。

```js
inputHandler(e) {
  console.log(e.detail.value)
}
```

# 条件渲染

使用`wx:if`判断是否需要渲染出该代码块。同时，还可以配合使用`wx:elif`和`wx:else`来添加else判断。

```html
<view wx:if="{{condition}}"> TRUE </view>
```

若要一次性控制多个组件的展示与隐藏，可以使用`<block>`标签将多个组件包裹起来，在block上使用wx:if控制。也可以使用view标签包裹，但view标签会被实际渲染出来。而block标签只起包裹作用，不会被实际渲染出来。

```html
<block wx:if="{{condition}}">
  <view> VIEW1 </view>
  <view> VIEW2 </view>
</block>
```

还可以使用`hidden`属性控制元素的显示与隐藏。

```html
<view hidden="{{condition}}"> 条件为true隐藏，false显示 </view>
```

> wx:if是动态地创建和移除元素，而hidden只是样式上切换展示隐藏，元素始终存在。若要频繁切换，则应当使用hidden。

# 列表渲染

使用`wx:for`可以根据指定的数组，循环渲染重复的组件结构。

```html
<view wx:for="{{array}}">
  当前索引为{{index}}，当前项为{{item}}
</view>
```

使用`wx:for-index`可以指定当前索引的变量名，使用`wx:for-item`可以指定当前项的变量名。用得少……

```html
<view wx:for="{{array}}" wx:for-index="indexName" wx:for-item="itemName">
  当前索引为{{indexName}}，当前项为{{itemName}}
</view>
```

## wx:key

类似Vue的:key，小程序在实现列表渲染时也建议使用列表项中的唯一key值，从而提高渲染的效率。

```js
// js中
data: {
  userList: [
    {id: 1, name: 'Jack'},
    {id: 2, name: 'Leon'},
    {id: 3, name: 'Mike'},
  ]
}
```

```html
<!-- wxml中 -->
<view wx:for="{{userList}}" wx:key="id"> {{item.name}} </view>
```

key值不需要使用双大括号包裹。

# wxss的模板样式

与css相比，扩展了rpx尺寸单位和@import样式导入。

`rpx`（responsible pixel）是微信小程序独有的，用来解决不同屏幕适配问题。其将不同的屏幕在宽度上等分为750份，即屏幕的总宽度为750rpx，从而在较小的屏幕上1rpx代表的尺寸较小，在较大的屏幕上1rpx代表的尺寸较大。小程序在不同屏幕上运行时会自动把rpx单位换算成对应的px单位来渲染，实现屏幕的自动适配。



`@import`后面跟上需要导入的外联样式表的相对路径，用`;`结尾。

```css
/** common.wxss **/
.small-p {
  padding: 10rpx;
}
```

```css
/** app.wxss **/
@import "common/common.wxss";

.middle-p {
  padding: 30rpx;
}
```



在app.wxss中的样式是全局样式，而页面的wxss中是局部样式，只会作用于当前页面。同样权重的前提下，局部样式会覆盖全局样式中冲突的部分。当权重不同的时候，以权重更高的样式为准。

# 网络数据请求

处于安全性考虑，小程序对数据接口的请求做出限制，只能请求HTTPS类型的接口，且必须将接口的域名添加到信任列表中。

使用wx.request()方法发起GET请求：

```js
wx.request({
  url: 'https://xxx.com/api/get', // 请求的接口地址
  method: 'GET', // 请求的方式
  data: { // 发送到服务器的数据
    name: 'Leon',
    age: 23
  },
  success: (res) => { // 请求成功后的回调函数
    console.log(res.data)
  }
})
```

使用wx.request()方法发起POST请求：

```js
wx.request({
  url: 'https://xxx.com/api/post', // 请求的接口地址
  method: 'POST', // 请求的方式
  data: { // 发送到服务器的数据
    name: 'Leon',
    age: 23
  },
  success: (res) => { // 请求成功后的回调函数
    console.log(res.data)
  }
})
```

> 很多时候需要在页面刚加载的时候，自动请求一些初始化的数据，则需要在页面的`onLoad`事件中调用获取数据的函数。

> 如果后端仅提供了http协议的接口，为了不耽误开发进度，可以在开发者工具中临时开启“开发环境不校验请求域名、TLS版本及HTTPS证书”选项，以跳过request合法域名的校验。

> 小程序的宿主环境不是浏览器而是微信客户端，所以不存在跨域问题。且没有Ajax模块，所以只能叫“发起网络数据请求”。