---
title: Vue 面试题
typora-root-url: ./
date: 2021-02-24 10:26:52
tags: Vue
categories:
---

## 1、讲一讲MVVM

MVVM 是 Model-View-ViewModel 缩写，也就是把 MVC 中的 Controller 演变成 ViewModel 。Model 层代表数据模型，View 代表 UI 组件，ViewModel 是 View 和 Model 层的桥梁，数据会绑定到 ViewModel 层并自动将数据渲染到页面中，视图变化的时候会通知 ViewModel 层更新数据。

## 2、简单说一下 Vue2.x 响应式数据原理

Vue 在初始化数据时，会使用 Object.defineProperty 重新定义 data 中的所有属性，当页面使用对应的属性时，首先会进行依赖收集（收集当前组件的 watcher 观察者）如果属性发生变化会通知相关依赖进行更新操作（发布订阅）。

## 3、知道 Vue3.x 响应式数据原理么？

Vue3.x 改用 Proxy 替代 Object.defineProperty 。因为 Proxy 可以直接监听对象和数组的变化，并且有多达13种拦截方法。并且作为新标准将受到浏览器厂商重点持续的性能优化。

- **Proxy 指挥代理对象的第一层，那么 Vue3 又是怎样处理这个问题的呢？**

  判断当前 Reflect.get 的返回值是否为 Object，如果是则通过 reactive 方法做代理，这样就实现了深度观测。

- **监测数组的时候可能多次触发 get/set，那么如何防止触发多次呢？**

  我们可以判断 key 是否为当前被代理对象 target 自身属性，可以判断旧值与新值是否相等，只要满足以上两个条件之一时，才有可能执行 trigger 。

## 4、再说一下 Vue2.x 中如何监测数组变化？

使用了函数劫持的方式，重写了数组的方法，Vue 将 data 中的数组进行了原型链重写，指向了自己定义的数组原型方法。这样当调用数组 api 时，可以通知依赖更新。如果数组中包含引用类型，会对数组中的引用类型再次递归遍历进行监控。这样就实现了数组变化。

## 5、nextTick 知道么，实现原理是什么？

在下次 DOM 更新循环结束之后执行延迟回调。nextTick 主要使用了宏任务和微任务。根据执行环境分别尝试采用

Promisse    MutationObserver   setImmediate

如果以上都不行则采用 setTimeout

定义了一个异步方法，多次调用 nextTick 会将方法存入一个队列中，通过这个异步方法清空当前队列。                                                                                  

## 6、说一下  Computed 和 Watch 区别

Computed 本质是一个具备缓存的 watcher,依赖的属性发生变化就会更新视图。适用于计算比较消耗性能的计算场景。当表达式过于复杂时，在模板中放入太多逻辑会让模板难以维护，可以将复杂的逻辑放入计算属性中处理。

Watch 没有缓存性，更多的是观察的作用，可以监听某些数据执行回调。当我们需要深度监听对象