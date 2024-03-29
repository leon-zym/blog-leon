---
title: 关于 Vue 脚手架项目中的两个 id="app" 的问题
date: 2022-05-15
categories: Programming
tags: [Vue2]
---

众所周知，在使用vue脚手架构建项目的时候，在静态资源的index.html中，会有一个id="app"的div，用于Vue实例对象vm的挂载。但是同时会发现在根组件App.vue中，也有一个id="app"的div。那么问题来了，在项目构建打包输出的时候，vm到底会挂载在哪儿呢？两个相同的id会不会冲突呢？

经过测试，我们将index.html中的id改为`app1`，项目页面加载不出来，浏览器控制台报错：
```shell
Cannot find element: #app
```

而将App.vue中的id改为`app1`，项目可以正常运行。同时，在浏览器中检查元素，发现body中的根元素的id也变为了`app1`：
![Element](https://p.ipic.vip/2vajp9.png)

由此可以推断出，Vue实例化的时候，el绑定的`#app`应该是index.html中的div元素。而App.vue中的div元素作为所有组件内容的集大成者，被挂载到index.html中div元素的位置上，替代原来的元素，完成内容的注入。

简单来说，就是index.html中的`<div id="app">`相当于目标位置，而App.vue中的`<div id="app">`相当于内容源。在构建打包输出的时候，内容源会注入并替代目标位置，两者不冲突。