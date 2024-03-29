---
title: Vue2 后台管理项目总结 (二)
date: 2022-05-26
categories: Programming
tags: [Vue2, Project]
---

# 对象的深度拷贝与深度监视

在写项目的用户管理页面时，发现在模态框中编辑用户信息，变动会即时地同步到用户列表中，而且虽然没有保存编辑，但列表中的变动依然存在，这一bug实数诡异。

![image-2022052842656474 PM](https://p.ipic.vip/xs750i.png)

经过排查，最后发现是由于js中赋值语句默认为浅拷贝，即对对象这种引用类型来说，只拷贝了其地址值，两个变量都指向内存中的同一个对象数据，具有关联性。

解决办法也很简单，利用es6的扩展运算符展开对象，然后再赋值即可：

```js
// 两个变量均为对象类型
// 这种赋值方式为浅拷贝，有关联性
this.operationForm = row

// 这种方式避免了问题，生成了两个独立的对象
this.operationForm = { ...row }
```

同理，在使用Vue的数据监视`watch`时，也要注意对象或数组等引用类型。若要监视其内部属性或元素的改变，则应当开启深度监视：

```js
watch: {
  // 深度监视传入数据，若数据发生变化则重新生成echarts图表实例
  chartData: {
    handler: function () {
      this.initChart()
    },
    // 开启深度监视
    deep: true,
  },
},
```

# axios的封装及api的统一管理

对axios的封装和api接口的统一管理，其主要目的就是在帮助我们简化代码和利于后期的更新维护。在做完项目后，才发现了[掘金上的一篇很好的帖子](https://juejin.cn/post/6844903652881072141)。

下面的是我自己在项目中对axios的简易封装：

先判断当前Node的环境，然后根据config生成一个baseURL：

```js
const baseURL =
  process.env.NODE_ENV === 'development'
    ? config.baseURL.dev
    : config.baseURL.pro
```

然后封装工具类class：

```js
class HttpRequest {
  constructor(baseURL) {
    this.baseURL = baseURL
  }
  
  getInsideConfig() {
    const config = {
      // 一些默认配置项
      baseURL: this.baseURL,
      header: {},
    }
    return config
  }
  
  interceptor(instance) {
    // 添加请求拦截器
    instance.interceptors.request.use(
      function (config) {
        // 在发送请求之前做些什么
        return config
      },
      function (error) {
        // 对请求错误做些什么
        return Promise.reject(error)
      }
    )
    // 添加响应拦截器
    instance.interceptors.response.use(
      function (response) {
        // 2xx 范围内的状态码都会触发该函数。
        // 对响应数据做点什么
        return response
      },
      function (error) {
        // 超出 2xx 范围的状态码都会触发该函数。
        // 对响应错误做点什么
        return Promise.reject(error)
      }
    )
  }
  
  // 后序需要接口请求的时候就调用request函数
  request(options) {
    // 创建axios实例
    const instance = axios.create()
    // 拼接配置项
    options = { ...this.getInsideConfig(), ...options }
    // 给实例添加拦截器
    this.interceptor(instance)
    // 返回接口请求的响应数据（promise对象）
    return instance(options)
  }
}

// 将工具类暴露出去
export default new HttpRequest(baseURL)
```

而对api接口管理的一个好处就是，我们把api统一集中起来，如果后期需要修改接口，我们就直接在api.js中找到对应的修改就好了，而不用去每一个页面查找我们的接口然后再修改。还有就是如果直接在我们的业务代码中修改接口，一不小心还容易动到我们的业务代码造成不必要的麻烦。

下面是我自己在项目里对其中几个api的统一管理：

```js
// api.js文件中集中写接口请求

import axios from './axios'

export const getToken = (params) => {
  return axios.request({
    url: '/permission/getToken',
    method: 'get',
    data: params,
  })
}

export const getData = () => {
  return axios.request({
    // method默认为GET
    url: '/home/getData',
  })
}

export const getUser = (params) => {
  return axios.request({
    url: '/user/getUser',
    method: 'get',
    params,
  })
}
```

# 路由守卫

在项目中涉及到了权限管理，其中存在两条逻辑：

1. 只有处于登陆状态下才能访问其他页面（token不存在且路由跳转其他页面是不允许的）
2. 登录状态下不能再访问登录页面了（token存在且路由跳转登录页面是不允许的）

我们应该在main.js中设置路由的全局前置守卫（初始化时执行、每次路由切换前执行），拦截不符合以上两条规则的路由跳转：

```js
router.beforeEach((to, from, next) => {
  store.commit('GET_TOKEN')
  const token = store.state.user.token
  // 规则一
  if (!token && to.name !== 'login') {
    // 重定向到登录页
    next('/login')
  // 规则二
  } else if (token && to.name === 'login') {
    // 重定向到首页
    next('/')
  } else {
    // 放行
    next()
  }
})
```

# localStorage、sessionStorage和Cookie的异同

数据上的生命周期的不同：

- localStorage：除非被永久清除，否则永久保存

- sessionStorage：仅在当前会话会有效，关闭页面或浏览器后被清除
- Cookie：一般由服务器生成，可设置失效时间，如果在浏览器端生成cookie，默认是关闭后失效

存放数据的大小不同：

- Cookie：一般为4kb

- localStorage和sessionStorage：一般为5mb

与服务器端通信不同：

- Cookie：每次都会被携带在HTTP头中，如果使用cookie保存过多数据会带来性能问题

- localStorage和sessionStorage仅在客户端（即浏览器）中保存，不参与和服务器的通信

localStorage、sessionStorage和Cookie的使用也极其相似，但Cookie需要额外加载js-cookie之类的包才能使用。

Cookie的基本使用：

```js
Cookie.set('key', 'value')  //保存数据
Cookie.get('key')  //获取数据
Cookie.remove('key')  //删除数据
Cookie.getJSON('key')  //转为json格式
```

# 遗留问题

目前还遗留了一些尚未完全理解的问题，会在后面的学习中陆续解决完善并记录的。

- [ ] 权限管理（页面权限、动态路由、接口权限、数据操作权限等）
- [ ] 互斥锁的思想
- [ ] 封装的思想
- [ ] 脚手架中生产开发环境的不同配置