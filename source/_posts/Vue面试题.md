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

**level1:** vue2.0中，响应式实现的核心就是 ES5的Object.defineProperty(obj, prop, descriptor). 通过Object.defineProperty()劫持data和props各个属性的getter和setter，getter做依赖收集，setter派发更新。整体来说是一个 数据劫持 + 发布-订阅者模式。

**level2:** 具体来说， ① vue初始化阶段(beforeCreate之后created之前)，遍历data/props，调用Object.defineProperty给每个属性加上getter、setter。② 每个组件、computed 都会实例化一个 watcher（当然也包括每个自定义watcher），订阅渲染/计算所用到的所用data/props/computed，一旦数据发生变化，setter 被调用，会通知渲染 watcher 重新计算、更新组件。

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

Watch 没有缓存性，更多的是观察的作用，可以监听某些数据执行回调。当我们需要深度监听对象中的属性时，可以打开 deep : true 选项，这样便会对对象中的每一项进行监听。这样会带来性能问题，优化的话可以使用字符串形式监听，如果没有写到组件中，不要忘记使用 unWatch 手动注销。

## 7、组件中的 data 为什么是一个函数？

一个组件被复用多次的话，也就会创建多个实例。本质上，这些实例都是同一个构造函数。如果 data 是对象的话，对象属于引用类型，会影响到所有的实例。所以为了保证组件不同的实例之间 data 不冲突，data 必须是一个函数。

## 8、说一下 v-model 的原理

v-model 本质就是一个语法糖，可以看成是 value + input 方法的语法糖。可以通过 model 属性的 prop 和 event 属性来进行自定义。

原生的 v-model，会根据标签的不同生成不同的事件和属性。

## 9、Vue 事件绑定原理说一下

原生事件绑定是通过 addEventListener 绑定给真实元素的，组件事件绑定是通过 Vue 自定义 $on 实现的。

## 10、Vue 模板原理知道么，能简单说一下么？

简单说，Vue 的编译过程就是将 template 转化为 render 函数的过程。会经历以下阶段：

- 生成 AST树

- 优化

- codegen

  首先解析模板，生成 AST 语法树（一种用 JavaScript 对象的形式来描述整个模板）。使用大量的正则表达式对模板进行解析，遇到标签、文本的时候都会执行相对应的钩子进行相关处理。

  Vue 的数据都是响应式的，但其实模板中并不是所有的数据都是响应式的。有一些数据首次渲染后就不会再变化，对应的 DOM 也不会变化。

  那么优化过程就是深度遍历 AST 树，按照相关条件对树节点经行标记。这些标记的节点（静态节点）我们就可以跳过对它们的对比，对运行时的模板起到很大优化作用。

  编译的最后一步就是将优化的 AST 树转化为可执行的代码。

## 11、说一下虚拟 DOM 以及 key 属性的作用

由于在浏览器中操作 DOM 是很昂贵的。频繁的操作 DOM ，会产生一定的性能问题。这就是虚拟 DOM 的产生原因。

Virtual DOM 本质就是利用原生的 JS 对象去描述一个 DOM 节点。是对真实 DOM 的一层抽象。

Virtual DOM 映射到真实 DOM 要经历 VNode 的 create、diff、patch等阶段。

**key 的作用是尽可能的复用 DOM 元素**

新旧 children 中的节点只有顺序不同的时候，最佳的操作应该是通过移动元素的位置来达到更新的目的。

需要在新旧 children 的节点中保存映射关系，以便能够在旧 children 的节点中找到可复用的节点。key 也就是 children 中节点的唯一标识。

## 12、Vue 中组件生命周期调用顺序说一下

组件调用顺序都是先父后子，渲染完成顺序是先子后父。

组件的销毁操作是先父后子，销毁完成的顺序是先子后父。

**加载渲染过程**

父 beforeCreate -> 父 created -> 父 beforeMount -> 子 beforeCreate -> created -> 子 beforeMount ->  子 mounted -> 父 mounted

**子组件更新过程**

父 beforeUpdate -> 子 beforeUpdate -> 子 updated -> 父 updated

**父组件更新过程**

父 beforeUpdate -> 父 updated

**销毁过程**

父 beforeDestory -> 子 beforeDestory -> 子 destoryed -> 父 destoryed

## 13、Vue2.x 组件通信有哪些方式

### **父子组件通信**

父 -> 子 props，子 -> 父 $on、$emit

获取父子组件实列 $parent、$children

ref 获取实例的方式调用组件的属性或者方法

### **兄弟组件通信**

Event Bus 实现跨组件通信 Vue.prototype.$bus = new Vue

### **Vuex运行机制**

Vuex 的 state 作为一个仓库，提供数据驱动 vue component渲染。视图通过 dispach 派发 actions，actions中可以做一些异步操作。actions 或者视图通过 commit 提交给 mutations，mutation 去改变state。

**level1:** 源码分析：vuex其实是一个Vue.js插件，插件都需要提供一个install方法，install方法调用会将Vue作为参数传入。Vue.user(plugin)安装插件，也就是执行插件的install方法。会在全局混入一个 **beforeCreate** 钩子函数，把示例化的 Store 保存到所有组件的 **this.$store **中。
**level2: mutation改变state, 会触发视图改变的原因？通过vue实现的，[实例化vue，把state作为一个data属性。]** ↔️ **核心**

```javascript
let Vue
function install(_Vue) {
  Vue = _Vue
  function vuexInit() {
    const options = this.$options
    console.log('vuexInit -> this.$options', this.$options)
    if (options.store) {
			// // 根实例 this --> Vue
      this.$store =
        typeof options.store === 'function' ? options.store() : options.store
    } else if (options.parent && options.parent.$store) {
			// 组件实例 this --> VueComponent, 如 APP, Home, About...
      this.$store = options.parent.$store
    }
  }
  Vue.mixin({ beforeCreate: vuexInit })
}

class Store {
  constructor(options = {}) {
    const { state = {}, mutations = {}, getters = {} } = options
    this._mutations = mutations
    // getter实现原理
    const computed = {}
    this.getters = {}
    for (let [key, fn] of Object.entries(getters)) {
      computed[key] = () => fn(this.state)
      Object.defineProperty(this.getters, key, {
        get: () => this._vm[key]
      })
    }
    this._vm = new Vue({
      data: { $$state: state }, // 核心原理
      computed
    })
  }

  commit(type, payload) {
    if (this._mutations[type]) {
      this._mutations[type](this.state, payload)
    }
  }

  get state() {
    return this._vm._data.$$state
  }
}

export default { Store, install }

```



## 14、SSR 了解么

SSR 也就是服务端渲染，将 Vue 在客户端把标签渲染成 HTML 的工作放在服务端完成，然后再把 HTML 直接返回给客户端。

SSR 有着更好的 SEO、并且首屏加载速度更快等有点。不过也有些缺点，比如我们开发的组件会受到限制，服务的渲染支持 beforeCreate 和 created 两个钩子，当我们需要一些外部扩展库时需要特殊处理，服务端渲染应用程序也需要处于 Node.js 的运行环境。还有就是服务器会有更大的负载需求。

## 15、你都做过哪些 Vue 的性能优化？

**编码阶段**

- 尽量减少 data 中的数据，data 中的数据都会增加 getter 和 setter，会收集对应的 watcher。

- v-if 和 v-for 不能连用

- 如果需要使用 v-for 给每个元素绑定事件时使用事件代理
- SPA 页面采用 keep-alive 缓存组件
- 在更多的情况下，使用 v-if 替代 v-show
- key 保证唯一
- 使用路由懒加载、异步组件
- 防抖、节流
- 第三方模块按需导入
- 图片懒加载

**SEO 优化**

- 预渲染
- 服务端渲染 SSR

**打包优化**

- 压缩代码
- 使用 cdn 加载第三方模块
- 多线程打包 happypack
- sourceMap 优化

## 16、hash 路由和 history 路由实现原理说一下

location.hash 的值实际就是 URL 中 # 后面的东西。

history 实际采用了 HTML5 中提供的 API 来实现，主要有 history.pushState() 和 history.replaceState()。

## 17、Vue 与 React 区别

### 相同点

1. 使用virtural DOM + Diff算法。
2. 组件化思想。

### 不同点

模板语法的不同：react通过JSX渲染模板，vue通过拓展的html语法进行渲染。比如react中插值、条件、循环都通过JS语法实现，vue是通过指令v-bind、v-if、v-for实现的。
监听数据变化原理不同：vue通过getter、setter劫持通知数据变化，react通过比较引用的方式进行。vue使用的响应式数据，而react是不可变数据。vue改变数据直接赋值，react需要调用setState方法（用新的state替换旧的state）。

## 18、loader、plugin、tree shaking

### **loader**

对模块的源代码进行转换，将不同的语言转换为JS，或将内联图像转换为data url。如：文件，url-loader、file-loader。转换编译，babel-loader、ts-loader。模板，html-loader。样式，style-loader、css-loader、less-loader。清理，eslint-loader。框架，vue-loader。

### plugin

解决loader无法实现的其他事儿。比如 HtmlWebpackPlugin、CleanWebpackPlugin、webpack-bundle-analyzer、DllPlugin、HotModuleReplacementPlugin。

### **tree shaking**

消除无用的js代码（剔除模块中没有导出或引用的部分）。仅支持ES Module静态引入方式，不支持require运行时动态引入方式。

ES6模块引入是静态分析的，故而可在编译时正确判断加载哪些代码。

可剔除的内容有限。webpack配合uglifyJS打包文件，只能shaking部分代码，像模块代码存在副作用，立即执行函数等都不能shaking。uglifyJS不进行程序流分析，只简单判断变量后续是否被引用、修改，不去排除有可能有副作用的代码。（rollup会）还有，比如项目中router.js引用了页面组件，但是在路由渲染中没有用到，也无法shaking掉。

## 18、懒加载路由

vue-router实现原理

**level1**:

1. 将需要懒加载的子模块，打包成单独的文件。ES6的import()。
2. hashChange时，根据hash变化执行特定的函数，加载子模块。
3.  实现的三种方式，location.hash + hashChange()，HTML5规范的pushState(IE10) + popState事件监听，abstract nodejs默认值。

**level2：**源码分析。路由安装，利用mixin给每个组件注入beforeCreated和destory钩子函数，在Vue原型上定义route 和 router，并进行响应式处理，定义全局的 roter-link 和 router-view 组件。根据路由配置创建映射关系。根据传入路径计算出新的路径，在路路径切换过程中，执行一系列的导航守卫函数，更改 Url，渲染对应组件。

