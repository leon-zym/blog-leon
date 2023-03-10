---
title: Promise 回顾
date: 2022-05-09
categories: Programming
tags: [JavaScript]
---

# Promise简介

Promise是ES6对JS中异步编程的新解决方案。其本质是一个构造函数。

相比旧的异步编程方案（回调函数）来说，优势在于：

1. 支持链式调用，解决了回调地狱的问题
2. 指定回调函数的方式更加灵活

# Promise基本使用

```js
// 实例化promise对象
const p = new Promise((resolve, reject) => {
  // 如果失败，则调用reject
  if(err) reject(err);
  // 如果成功，则调用resolve
  resolve(data);
});

// 调用then方法处理结果
p.then(value => {
  // 成功的处理
}, reason => {
  // 失败的处理
});
```

resolve方法会把promise对象的状态设为成功，传入一个data作为成功的值。reject方法会把promise对象的状态设为失败，传入一个err作为失败的值。

then方法传入两个函数作为参数，第一个函数处理结果为成功时的值value，第二个处理结果为失败时的值reason。

Promise可以用来封装fs文件读取操作，以及Ajax请求操作等，使之成为Promise风格的异步操作。

# 如何修改promise对象的状态

promise的状态其实是实例对象中的一个属性`PromiseState`，其值初始为pending，可以被改变为resolved或rejected。但只能被改变一次，不能来回改变。

resolve函数：pending  =>  fulfiled (resolved)

reject函数：pending  =>  rejected

throw抛出错误：pending  =>  rejected

# 能否执行多个回调

一个promise实例化对象后面可以依次跟多个then方法链式调用。

# 改变状态与指定回调的顺序问题

在使用Promise构造函数实例化对象的时候，传入的executor执行器会在Promise内部立即同步调用，而异步操作会在执行器中执行。

改变状态与指定回调两者的执行顺序看情况：

若执行器executor中的函数是一个异步操作，则先执行then方法指定回调，再改变promise对象的状态（一般情况）。

若执行器executor中的函数是一个同步操作，则会先改变promise对象的状态，再执行then方法指定回调。

> 未完待续……