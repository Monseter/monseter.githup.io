---
title: Vuex封装
typora-root-url: ./
date: 2021-01-28 16:21:04
tags: [Vue,Vuex]
categories: 
---
## VUEX封装

面对多业务需求的时候，状态机里的管理就比较混乱，那么最好的方法就是模块化。

在store 内新增一个 ‘modules文件夹’ ,主要做模块化存储，同级新增一个index.js文件用于导出vuex。

modules文件夹内，新建个我们需要的user.js 模块

```javascript
const UserInfo = {
    state: {
        userInfo: {}
    },
    mutations: {
        setUserInfo(state, val) {
            state.userInfo = val
        },
		clear(state) {
			Object.keys(state).forEach(e => {
				state[e] = {}
			})
		}
    },
	actions: {
		setUserInfoSync(contxt,val) {
			return contxt.commit('setUserInfo',val)
		},
		clearSync(contxt) {
			return contxt.commit('clear')
		}
	}
}

export default UserInfo
```

index.js文件代码如下，用于导入modules文件夹下所有的模块并做处理

```javascript
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

let modules = {}  // 放的是modules内的每个模块配置
let sessionStore = []   // 存放配置，防刷新
const storeModules = require.context('./modules',false,/\.js/)   // 导入所有依赖模块

storeModules.keys().forEach(filename => {
	const storeModuleItem = storeModules(filename)  // 获取到具体模块带有 路径 default 属性
	let moduleName = filename.split('/').pop().replace('.js','') 
	moduleName = moduleName[0].toUpperCase() + moduleName.slice(1)  // 对每个模块进行重新命名例 user.js 变成 User
	modules[moduleName] = storeModuleItem.default || storeModuleItem   // default，获取到我定义的模块内的所有内容，前提是该模块是使用 export default 路径导出的
	if (storeModuleItem.default) {
		sessionStore.push(moduleName)
	}
})
export {sessionStore}

let store = new Vuex.Store({
	modules: modules
})

export default store

/**
 * 使用方法
 * $store.state.User.UserInfo  获取内容
 * $store.dispatch('setUserInfoSync',val) 异步修改
 **/
```

最后注意，vuex有个缺点就是刷新时候会出现数据丢失，解决办法是在页面刷新前将我们的vuex moudles配置存放sesssinSorage内，随后再取出来赋值就行了。页面入口文件App.vue

```javascript
import {sessionStore}from "./store/index"  // 引入我们事先存放的vuex内的modules 配置

// 解决刷新vuex为空
    created() {
        let tempStore = sessionStorage.getItem('store');
        if (tempStore) {
            this.$store.replaceState(Object.assign({},this.$store.state,JSON.parse(tempStore))) // 对象合并
        }
        window.onbeforeunload = () => {
            let store = {};
            for(let module of sessionStore) {
                store[module] = this.$store.state[module]
            }
            sessionStorage.setItem('store', JSON.stringify(store))
        }
    }
```