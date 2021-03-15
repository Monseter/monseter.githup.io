---
title: JS 宏任务与微任务
typora-root-url: ./
date: 2021-02-25 17:48:53
tags: JavaScript
categories:
---

## 简单理解同步异步、宏任务和微任务

概述：js 是单线程的，所有的任务都要排队挨个执行，就好比做保健（执行js代码），保健师傅只有一个（单线程），顾客（js代码）需排队享受服务，排队的顺序按照顾客的种类（同步异步、宏任务微任务）和顾客到店顺序（在代码中的位置）执行；

同步与异步、宏任务和微任务分别是函数两个不同维度的描述。

- 异步任务

  setTimeout 和 setInterval、*ajax、事件绑定等*

- 同步任务

  除了异步任务外的所有任务

- 微任务

  `process.nextTick` 和 `Promise` 后的 `then` 语句和 `catch` 语句等

- 宏任务

  除了微任务以外的所有任务 

## 执行顺序判断方法

先同步再异步，在此基础上先宏任务再微任务

## 实例分析

```javascript
setTimeout(() => {
    console.log('异步1任务time1');
    new Promise(function (resolve, reject) {
        console.log('异步1宏任务promise');
        setTimeout(() => {
            console.log('异步1任务time2');
        }, 0);
        resolve();
    }).then(function () {
        console.log('异步1微任务then')
    })
}, 0);
console.log('主线程宏任务');
setTimeout(() => {
    console.log('异步2任务time2');

}, 0);
new Promise(function (resolve, reject) {
    console.log('宏任务promise');
    // reject();
    resolve();
}).then(function () {
    console.log('微任务then')
}).catch(function () {
    console.log('微任务catch')
})
console.log('主线程宏任务2');
```

本例中需注意第9行的then是在第14行的setTimeout之前执行的，而第5行的setTimeout在第14行之后执行。也就是在一个异步任务代码块中，会先执行完所有同步语句（包括宏任务和微任务），然后去执行整个代码中的同级别的异步任务，而第5行的setTimeout因是第二层异步语句，会被放到之后才执行。

执行结果如下：

![image-20210225175538828](image-20210225175538828.png)