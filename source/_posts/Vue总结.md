---
title: Vue总结
typora-root-url: ./
date: 2021-01-28 16:09:58
tags: Vue
categories: WEB框架
---
## 父子组件传值修饰符 sync
描述：sync 取代传统的 prop 父子组件传值从而达到数据的双向绑定，正常来说子组件是不可以任意修改父组件的内容容易造成数据混乱，从而造成难以维护的困扰！

官方推荐使用一种：update:my-pro-name 的模式来替代触发事件，目的是为了优雅而不粗鲁的实现父子组件间的双向绑定！

如下 demo ：

```vue
// 父组件代码
<template>
    <div>
        <input type="button"
               value="我是父组件中的按钮"
               @click="show">
        <child @upIsShow="changeIsShow" v-show="isShow"/>   注意i
    </div>
</template>
<script>
    import child from "@/components/child"
    export default {
        data() {
            return {
                isShow:false
            }
        },
        components:{
            child
        },
        methods:{
            show(){
                this.isShow=true;
            },
            changeIsShow(bol){
                this.isShow=bol;
            }
        }
    }
</script>

// 子组件代码
<template>
    <div>
         我是一个子组件，我在红色的海洋里！
        <input type="button" value="点我隐身" @click="upIsShow">
    </div>
</template>
<script>
    export default {
        methods:{
            upIsShow(){
                // upIsShow 触发父组件桥梁
                this.$emit("upIsShow",false); 
            }
        }
    }
</script>
```

##### 按照官方推荐的作一次修改，将父组件中的事件@upIsShow修改为@update:isShow：

```javascript
// 父组件
<child @update:isShow="changeIsShow" v-show="isShow"/>
    
// 子组件相应调整下
upIsShow(){
    this.$emit("update:isShow",false);
}
```

运行一切 ok !

##### 下面将父组件的 changeIsShow 方法直接写成匿名函数

``` javas
// 修改前
<child @update:isShow="changeIsShow" v-show="isShow"/>
// 修改后改为匿名函数
<child @update:isShow="function(bol){isShow=bol}" v-show="isShow"/>
```

运行一切 ok !

##### 下面再将匿名函数改成箭头函数

``` javas
// 修改前匿名函数
<child @update:isShow="function(bol){isShow=bol}" v-show="isShow"/>
// 修改后箭头函数
<child @update:isShow="bol=>isShow=bol" v-show="isShow"/>
```

运行一切ok !

##### 作最后的修改！ 注意！

``` javas
// 修改前箭头函数
<child @update:isShow="bol=>isShow=bol" v-show="isShow"/>
// 修改后带有修改符 sync
<child :isShow.sync="isShow" v-show="isShow"/>
```

**总结：** 以上代码 :isShow.sync="isShow"其实是 @update:isShow="bol=>isShow=bol"语法糖。 是一种简写形式，常见的是后者，前者现在也是明白了。父组件中接收子组件传数据的方法名依旧不变，调整的也就是语法糖而已。

```javascript
// 父组件
changeIsShow(bol){
    this.isShow=bol;
}
//子组件
upIsShow(){
    this.$emit("update:isShow",false);
}
```

## v-for 为什么不推荐使用 index 作为key

我们知道当我们使用v-for渲染列表数据的时候，key我们都使用index作为唯一。在vue 中虚拟dom会做一初始化数据，因为数据改变会触发重新渲染但不会导致重建。这是个性能问题，所以当我们在列表中加入数据的时候，因为新增数据所以列表index也被改变了，最后虚拟dom在渲染的时候就会出现重建降低性能。因此，我们最好给一个id或者别的属性。注：index 接收number 与 string 类型。

## 为什么Vue里的data要用return返回

官网示列：

```javascript
var vm = new Vue(
    {
        el: "#example",
        data: {
            message: "hello"
        }
    }
)
```

项目中使用：

```javascript
data() {
    return {
        message: 'hello'
    }
}
```

### 为什么要把data变成函数并 return 属性呢？

我们知道组件是一个可以复用 的实例，当你引用一个组件的时候，组件里的data是一个普通的对象，所用到这个组件的都会引用这同一个data，从而造成了数据污染。

将data封装成函数后，在实例化组件的时候，我们只是调用了data函数生成的数据副本，也就避免了数据污染。

## Promise详情

Promise意在让异步代码变得干净和直观，让异步代码变得尽然有序。

Promise在设计上具有原子性，即只有三种状态：等待（Pending）、成功（Fulfilled）、失败（Rejected）。在调用支持Promise的异步方法时，逻辑变得非常简单。

### 基本语法

创建的Promise对象作为异步方法的返回值，所有状态就可以使用它所提供的方法进行控制。

resolve(value)方法控制当前Promise对象是否进入了成功状态，一但执行该方法有且只有一个返回值，Promise会从等待进入到成功状态，并且不会接受别的状态了。

reject(reason) 方法控制当前Praomise对象是否进入失败阶段，同样一旦进入失败阶段无法改变其状态。

```javascript
return new Promise( (resolve,reject) => {
	resolve(data) // 作为返回值
    reject(err) // 失败状态返回错误
})
```

## Flex布局注意
#### 

```css
display: flex;
flex-wrap: wrap;
align-content: flex-start;  
```

flex布局，用了flex-wrap:wrap;自动换行属性后，导致两行div中间有很大一块空行，所以一定要加  ”align-content: flex-start“ 这个属性，换行后都是从最左边排列的。从而解决两个div之间出现大空白问题。

## $set 个人理解

情景：vue开发中，当我们给响应式的对象添加属性时，新增的属性不会立即显示到界面中，但是通过控制台是可以打印出来的。

响应数组及对象：响应式对象和响应式数组是指在vue初始化时期，利用Object.defineProperty()方法对其进行监听，这样在修改数据时会及时体现在界面上  。

$set是在vue初始化实例后对所需的数据进行改变其动态响应性，vue实例化后追加的数据一般是无法具有动态响应性。

## 结合Iview tabele 插槽理解及使用

### 具名插槽
  v2.6.0更新 v-slot,取代了slot 以及 slot-scope
  这个用法很简单，一般用于容器固定位置；跟 `v-on` 和 `v-bind` 一样，`v-slot` 也有缩写，即把参数之前的所有内容 (`v-slot:`) 替换为字符 `#`。例如 `v-slot:header` 可以被重写为 `#header`：

  一个不带 `name` 的 `<slot>` 出口会带有隐含的名字“default”。

  注意：具名插槽最好使用HTML5语义化标签

  ```vue
  <template>
  	<!-- 子组件 -->
  	<slot name="header"></slot>
  	<slot name="section"></slot>
  	<slot></slot> 
  </template>
  
  <template>
  	<!-- 父组件 -->
  	<children>
          <template v-slot:header>
              <h1>Here might be a page title</h1>
           </template>
  		<!-- 或者如下 -->
          <template #header>  //必须要在 template 中使用
           	<span></span>
  		</template>
      </children>
  
  	<!-- 对应的渲染效果如下 -->
  	<children>
         <header>
          <h1>Here might be a page title</h1>
        </header>
      </children>
  </template>
  
  <!-- 然而，和其它指令一样，该缩写只在其有参数的时候才可用。这意味着以下语法是无效的： -->
  <!-- 这样会触发一个警告 -->
  <current-user #="{ user }">
    {{ user.firstName }}
  </current-user>
  
  <!-- 如果你希望使用缩写的话，你必须始终以明确插槽名取而代之： -->
  <current-user #default="{ user }">
    {{ user.firstName }}
  </current-user>
  ```

### 作用域插槽

  当被提供的内容*只有*默认插槽时，组件的标签才可以被当作插槽的模板来使用。这样我们就可以把 `v-slot` 直接用在组件上：

  ```vue
  <template>
  	<!-- 子组件 -->
  	<slot v-bind:header="header"></slot>
  	<slot v-bind:default="section"></slot>
  	<slot v-bind="arcticle"></slot>
  	<slot></slot>
  </template>
  
  <template>
  	<!-- 父组件 -->
  	<children v-slot="section">  ===  v-bind="section" === 
          v-bind:default="section" *只有*默认插槽时可以直接绑定到组件上
       	 <template v-slot:default="slotProps">
              {{ slotProps.xx }}
            </template>
  		<template v-slot:header="slotProps">
              {{ slotProps.xx }}
            </template>
      </children>
  	<slot></slot> 
  </template>
  
  说明：v-bind:header="header"，什么意思呢其实就是在 slot 元素上绑定了以header为作用名，值为header的一个作用域，或者你也可以不写。引用的时候 slotProps 可以为任意字段，=== v-bind:header="header" 中的 header 对象
  ```

注意默认插槽的缩写语法**不能**和具名插槽混用，因为它会导致作用域不明确：

引用 VUE.js 文档

```vue
<!-- 无效，会导致警告 -->
<current-user v-slot="slotProps">
  {{ slotProps.user.firstName }}
  <template v-slot:other="otherSlotProps">
    slotProps is NOT available here
  </template>
</current-user>
```

只要出现多个插槽，请始终为*所有的*插槽使用完整的基于 `<template>` 的语法

### 解构插槽

  可以看 vue.js 文档，使用方法如下

  ```vue
  <current-user v-slot="{ user: person }">
    {{ person.firstName }}
  </current-user>
  ```

### iview Table 案例：将table作为子组件封装的时候，父组件用来进行v-for操作时候，获取index

```vue
<!-- 父组件引用了这个table组件 -->
<RiskTable
    ref="riskttTable"
    :riskTableData="item.record"
    :riskColumns.sync="checkRecordColumns"
    :columnIndex="1"
>
    // slot-scope="params" 可以简写成 v-slot="params"
    <template slot-scope="params" slot="feedBack">  // slot name 是 column 里的name
        <RadioGroup v-if="item.isShow ? false : true" v-model="params.row.feedback" @on-change="handleRadio(params.row,index)">
            <Radio label="1">正常</Radio>
            <Radio label="0">异常</Radio>
        </RadioGroup>
        <div v-if="item.isShow ? true : false">
            <img :src="require(params.row.feedback === 0 ? '../assets/error.png' : '../assets/success.png')" alt="">
        </div>
    </template>
</RiskTable>
```

```vue
<!-- table子组件 -->
<template
    v-for="column in riskColumns"
    :slot="column.slot ? column.slot : ''"
    slot-scope="params"   判断有无slot 
>
	判断有无slot,v-bind 绑定table中的数据
	具名插槽以及作用域插槽
    <slot :name="column.slot ? column.slot : ''" v-bind="params"></slot>
</template>


```

## $event的通俗理解

- **$event 是指当前触发的是什么事件（鼠标点击、键盘事件等）**
- **event.target**  获取事件源对象，包括改该Dom节点的一些相关属性
  1. **event.target.nodeName**  获取节点名称，例如 p、span等标签
  2. **event.target.id** 获取节点属性绑定的id 名称
  3. **event.target.className** 获取节点属性绑定的class名称
- **event.currentTarget** 获取当前事件对象，包括该Dom节点的一些相关属性

### Example事件委托机制:

事件委托机制意思就是减少dom操作，通过事件冒泡将事件反馈到父元素上，统一由父元素进行处理。以下是vue实列

```vue
<template>
	<ul class="parent" @click="handleEntrust($event,123)"> 事件对象，自定义参数
        <li>1111</li>
        <li>2222</li>
        <li>3333</li>
    </ul>
</template>

export default {
	methods: {
		handleEntrust(ev, val) {
			console.log(ev,val)
		}
	}
}
注：此时如果我们要点击li 1111 那么就会出现事件冒泡，依次向上传递到父级元素，由父级事件handleEntrust进行处理，我们只需要根据 ev.targe 获取到的事件源对象判断进行相应的处理就可以了
```