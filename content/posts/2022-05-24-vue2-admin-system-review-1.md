---
title: Vue2 后台管理项目总结 (一)
date: 2022-05-24
categories: Programming
tags: [Vue2, Project]
---

虽说当今漫山遍野都是各式各样的后台管理项目，并且布局和功能都大同小异，着实没有任何新意亮点，但为了练手巩固刚学完的Vue2知识，以及熟悉前端项目开发的流程，我最终还是决定自己做一个出来。这个后台管理项目是基于Vue2开发，从零开始，没有选择借助大神PenJiaChen的[现成模板](https://github.com/PanJiaChen/vue-admin-template)。

项目中运用了Vue脚手架环境、Vue Router路由管理、Vuex状态管理、elementUI组件库、Echarts数据可视化、axios请求库等技术。

以目前的能力和精力，开发出首页看板、用户管理等页面，拥有管理员登入登出、数据可视化展示、用户信息增删改查等功能。同时将许多通用的功能模块封装成组件，对外暴露接口，为通用后台管理系统的后续开发提供了丰富的可扩展性、可维护性。

![Xnip2022-05-28_23-03-15](https://p.ipic.vip/s4spuq.png)

![Xnip2022-05-28_23-03-54](https://p.ipic.vip/pyjwsn.png)

下面内容是在项目基本完成后，对开发过程中遇到的重难点、对Bug的解决处理方式的零散记录总结。

# 路由懒加载

在路由规则中，我们每个路径对应的组件，都可以用`import()`来做到路由懒加载，然后当路由被访问的时候才加载对应组件。[官方文档](https://router.vuejs.org/zh/guide/advanced/lazy-loading.html)

```js
{
  path: '/dashboard',
  name: 'Dashboard',
  // 直接加载
  // component: '@/views/Dashboard'
  // 懒加载
  component: () => import('@/views/Dashboard'),
  // 元数据
  meta: {
    title: '看板',
    icon: 's-data',
  },
},
```

# 解决重复路由跳转的报错

在侧边栏导航进行路由跳转。当我们重复点击导航的时候，会在浏览器控制台报错。这个报错是因为路由跳转的回调返回的是一个promise对象，如果没有对其设置异常捕获，则会出现报错。

![image-2022052844118900 PM](https://p.ipic.vip/1j7cc5.png)

可以在每次写路由跳转的时候补上catch来捕获异常，也可以一劳永逸，直接封装修改VueRouter原型身上的push方法：

```js
const originalPush = VueRouter.prototype.push

VueRouter.prototype.push = function push(location) {
  return originalPush.call(this, location).catch((err) => err)
}
```

# 项目中的路径

在项目代码中应当尽量使用相对路径，而当项目的文件结构变得很深的时候，大量使用`../`往外翻找就会显得可读性很低。这时候就应当配置一个别名`@`作为src文件夹的路径，其他路径均可以从src文件夹出发找到。

在项目根目录的jsconfig.json中的compilerOptions中添加：

```js
"paths": {
  "@/*": [
    "src/*"
  ]
}
```

目前版本的vue-cli脚手架已经帮我们在jsconfig.json中配置好了相关别名，很贴心了可以说。当然，我们还可以配置多组其他常用路径的别名，以简便项目中路径的写法。

# elementUI中Layout布局的样式问题

侧边栏el-aside默认会出现滚动条，但由于其样式太丑，我们往往需要将滚动条隐藏。

```css
.el-aside::-webkit-scrollbar {
  display: none; 
}
```

同时，我们会发现el-container默认随着内部元素的高度而撑开自身高度。若需要el-container占满全部视口屏幕，有两种方式实现。

第一种，使用vh作单位。但若页面高度超出视口高度，开始可以往下滚动时，el-container的高度并不会随之变化。

```css
.el-container {
  height: 100vh;
}
```

第二种，使用%作单位。但不能仅将el-container的高度改为100%，还需要同时将其所有的父元素高度都改为100%。

```css
html,
body,
#app,
.el-container {
  height: 100%;
}
```

# 修改hr分隔线元素的颜色

我们没法直接修改hr的颜色，而是要先将其边框去掉，高度设为1px。即当做一个普通div看待。

```css
hr {
  border: 0;
  height: 1px;
  background-color: #ccc;
}
```

# 对axios返回结果的处理

在使用axios发送请求时，对返回的结果进行then处理，可当时却一直拿不到Mock返回的data数据。经过测试发现，返回的数据并不直接在response对象中，而是包含在其内部的data中：

![image-2022052851312675 PM](https://p.ipic.vip/h6cs3g.png)

为了简便使用，我们可以先对其解构赋值，提取出data中的数据：

```js
getData().then(res => {
  const { code, data } = res.data
})
```

# 关闭tag标签的逻辑

当用户关闭当前所在的tag，应当自动跳转到前面一个tag的页面。主要点是使用`indexOf()`获取到当前tag在列表中的的索引号

```js
handleCloseTag(tag) {
  // 关闭当前tag后自动跳转到上一个tag
  if (tag.name === this.$route.name) {
    this.$router.push({
      name: this.tags[this.tags.indexOf(tag) - 1].name,
    })
  }
  this.tags.splice(this.tags.indexOf(tag), 1)
},
```

# 组件身上的attribute

在通过 Prop 向子组件传递数据时，attribute有时候直接写属性名，有时候却要在前面加上v-bind的冒号。

经过查阅[Vue官方文档](https://cn.vuejs.org/v2/guide/components-props.html#%E4%BC%A0%E9%80%92%E9%9D%99%E6%80%81%E6%88%96%E5%8A%A8%E6%80%81-Prop)后理解，当直接写属性名的时候，传递的数据仅为字符串类型。当前面加上冒号后，传递的数据为一个动态的变量或静态的js表达式（数字、布尔值、数组、对象）。

# 组件身上的事件监听

给DOM元素绑定原生事件，可以直接用v-on`@`指定原生事件名。给子组件绑定自定义事件，也可以直接用v-on`@`指定自定义事件名。

但是要给子组件绑定原生事件，则需要在v-on`@`指定的原生事件名后面加上修饰符`.native`。

```html
<!-- 组件中的原生事件 -->
<my-component @click.native="onClick"></my-component>
```

另外，如果不需要传递参数，则指定的回调函数直接写函数名即可。若需要传递参数，才需要加上括号及参数。还可以在参数中额外用`$event`占位，代表事件对象传递给回调函数。

# 获取模块化store中的state状态

在全局注册Vuex后，所有的vc身上都带有了`$store`属性。我们可以在组件中通过`this.$store.state`访问state状态，通过`this.$store.dispatch()`分发action，通过`this.$store.commit()`提交mutation。

不过，若使用module模块化分割store后，经过我的测试，在组件中应改用`this.$store.state.moduleName`访问模块中的state状态。而dispatch和commit则不需要加上模块名。

