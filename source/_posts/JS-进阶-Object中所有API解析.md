---
title: JS Object中所有API解析
typora-root-url: ./
date: 2021-03-10 10:57:39
tags:
categories: JavaScript
---

## 简介

用于对 `JavaScript` 中 `Object` 基础 `API` 不熟悉，举个开发中常见的需求，经常会有类似的封装 `http` 到原型 `Vue.prototype`，一般我们是这样封装的，但容易被篡改。

```javascript
import Vue from 'vue';

function http() {
    console.log('调用http 接口方法')
}
Vue.prototype.$http = http;
var vm = new Vue();
vm.$http();
vm.$http = 1; // 一旦被修改就会改变，虽然一般正常情况下不会被修改
vm.$http()  // 当再次调用的时候就会报错，此时并不是一个方法，而是一个值
```

熟悉`Object.defineProperty`或者说熟悉对象`API`的人，一般是如下代码写的，则不会出现被修改的问题。

```javascript
import Vue from 'vue';

function http() {
    console.log('调用http 接口方法')
}
Object.definePrototype(Vue.prototype,'$http', {
    get() {
        return http
    }
})
var vm = new Vue();
vm.$http();
vm.$http = 1; // 这里即使修改也无法生效
vm.$http(); // 调用正常
```

> **vue-router 源码里就是类似这样写的**，`this.$router`，`this.$route`无法修改。

```javascript
// vue-router 源码
Object.defineProperty(Vue.prototype, '$router', {
	get () { return this._routerRoot._router }
})
Object.defineProperty(Vue.prototype, '$route', {
	get () { return this._routerRoot._route }
})
```

## Object 构造器（`constructor`）的成员

创建对象的两种方式：

```javascript
var o = new Object();
var o = {}; // 推荐
```

该构造器可以接受任何类型的参数，并且会自动识别参数的类型，并选择更合适的构造器来完成相关操作。比如：

```javascript
var o = new Object('something');
o.constructor; // ƒ String() { [native code] }
var n = new Object(123);
n.constructor; // ƒ Number() { [native code] }
```

### Object.prototype（慎用）

该属性是所有对象的原型（包括 `Object` 对象本身），语言中的其它对象正是通过该属性来添加东西从而实现它们之间的继承关系。所以要谨慎使用。比如：

```javascript
var s = new String('字符串');
Object.prototype.custom = 1;
console.log(s.custom); // 1
```

> 注意：这就是原型链问题

### Object.prototype.toString(radix) （常用）

该方法返回的是一个用于描述目标对象的字符串。特别地，当目标是一个 `Number` 对象时，可以传递一个用于进制数的参数 `radix`，该参数的默认值为 10。

```javascript
var b = { prop: 11 }
console.log(b.toString()); // [object Object]
var a = new Number('123');
console.log(typeof(a.toString()) == 'string'); // true
```

### Object.prototype.toLocalString()

该方法的作用与`toString()`基本相同，只不过它做一些本地化处理。该方法会根据当前对象的不同而被重写，例如`Date()`,`Number()`,`Array()`,它们的值都会以本地化的形式输出。当然，对于包括`Object()`在内的其他大多数对象来说，该方法与`toString()`是基本相同的。在浏览器环境下，可以通过`BOM`对象`Navigator`的`language`属性（在`IE`中则是`userLanguage`）来了解当前所使用的语言：

```javascript
navigator.language; //'en-US'
```

### Object.prototype.valueOf()

该方法会返回一个包含基本类型的 `this` 值。如果是 `Number` 返回其基本数值，而 `Date` 对象返回的是一个时间戳。

```javascript
var d = new Date()
console.log(d.valueOf()); // 1615348000258
console.log(typeof(d.valueOf())); // number

var b = new Number(123);
console.log(typeof(b.valueOf())); // number
console.log(b.valueOf() === b); // false
```

### Object.prototype.hasOwnProperty(prop)（常用）

该方法用于判断目标对象属性中是否包含自身属性，如果存在返回 `true` ，而当该属性是从原型链中继承而来或根本不存在时，返回 `false`。

```javascript
var obj = { name: '小明' }
console.log(obj.hasOwnProperty('name')); // true
console.log(obj.hasOwnProperty('toString')); // false
```

### Object.prototype.isPrototypeOf(obj)（常用）

该方法用于判断目标对象是否为当前对象的原型，如果是返回 `true`，而且，当前对象所在的原型上所有对象都能通过该测试，并不局限与它的直系关系。

```javascript
var str = new String('');
console.log(Object.prototype.isPrototypeOf(str)); // true
console.log(Array.prototype.isPrototypeOf(str)); // false
```

### Object.prototype.propertyIsEnumerable(prop)

如果目标属性能在`for in`循环中被显示出来，该方法就返回`true`

```javascript
var a = [1,2,3];
a.propertyIsEnumerable('length'); // false
a.propertyIsEnumerable(0); // true
```

## ES5 中附加的 Object 属性

在`ES3`中，除了一些内置属性（如：`Math.PI`），对象的所有的属性在任何时候都可以被修改、插入、删除。在`ES5`中，我们可以设置属性是否可以被改变或是被删除——在这之前，它是内置属性的特权。`ES5`中引入了**属性描述符**的概念，我们可以通过它对所定义的属性有更大的控制权。这些**属性描述符**（特性）包括：

- `value`——当试图获取属性时所返回的值
- `writable`——该属性是否可写
- `enumerable`——该属性在`for in`循环中是否会被枚举
- `configurable`——该属性是否可被删除
- `set()`——该属性的更新操作所调用的函数
- `get()`——获取属性值时所调用的函数

另外，**数据描述符**（其中属性为：`enumerable`，`configurable`，`value`，`writable`）与**存取描述符**（其中属性为`enumerable`，`configurable`，`set()`，`get()`）之间是有互斥关系的。

可以理解为：在定义了`set()`和`get()`之后，描述符会认为存取操作已被定义了，其中再定义`value`和`writable`会**引起错误**。以下是*ES3*风格的属性定义方式

```javascript
var person = {};
person.legs = 2;
```

以下是等价的 ES5 通过**数据描述符**定义属性的方式：

```javascript
var person = {};
Object.defineProperty(person, 'legs', {
    value: 2,
    writable: true,
    configurable: true,
    enumerable: true
});
```

其中， 除了 value 的默认值为`undefined`以外，其他的默认值都为`false`。这就意味着，如果想要通过这一方式定义一个可写的属性，必须显示将它们设为`true`。或者，我们也可以通过`ES5`的存储描述符来定义：

```javascript
var person = {};
Object.defineProperty(person, 'legs', {
    set:function(v) {
        return this.value = v;
    },
    get: function(v) {
        return this.value;
    },
    configurable: true,
    enumerable: true
});
person.legs = 2;
```

这样一来，多了许多可以用来描述属性的代码，如果想要防止别人篡改我们的属性，就必须要用到它们。

### Object.defineProperty(obj,prop,descriptor)

> Vue2.x 响应式数据原理，把一个普通 JavaScript 对象传给 Vue 实例的 data 选项，Vue 将遍历此对象所有的属性，并使用 Object.defineProperty 把这些属性全部转为 getter/setter。Object.defineProperty 是仅 ES5 支持，且无法 shim 的特性，这也就是为什么 Vue 不支持 IE8 以及更低版本浏览器的原因。

### Object.defineProperties(obj,props)

该方法的作用与`defineProperty()`基本相同，只不过它可以用来一次定义多个属性。比如：

```javascript
var glass = Object.defineProperties({}, {
    'color': {
        value: 'transparent',
        writable: true
    },
    'fullness': {
        value: 'half',
        writable: false
    }
});
glass.fullness; // 'half'
```

### Object.getPrototypeOf(obj)

之前在`ES3`中，我们往往需要通过`Object.prototype.isPrototypeOf()`去猜测某个给定的对象的原型是什么，如今在`ES5`中，我们可以直接询问改对象“你的原型是什么？”

```javascript
var str = new String('');
console.log(Object.getPrototypeOf(str)); // [String: '']
console.log(Object.getPrototypeOf(str) === String.prototype); // true
var list = new Array([]);
console.log(Object.getPrototypeOf(list)); // []
var obj = new Object({})
console.log(Object.getPrototypeOf(obj)); // {}
```

### Object.create(obj,descr)（常用）

该方法用于创建一个新的对象并为其设置原型，用（上述）属性描述符来定义对象的原型属性。

```javascript
var parent = {hi: 'Hello'};
var o = Object.create(parent, {
    prop: {
        value: 1
    }
});
o.hi; // 'Hello'
// 获得它的原型
Object.getPrototypeOf(parent) === Object.prototype; // true 说明parent的原型是Object.prototype
Object.getPrototypeOf(o); // {hi: "Hello"} // 说明o的原型是{hi: "Hello"}
o.hasOwnProperty('hi'); // false 说明hi是原型上的
o.hasOwnProperty('prop'); // true 说明prop是原型上的自身上的属性。
```

现在，我们甚至可以用它来创建一个完全空白的对象，这样的事情在`ES3`中可是做不到的。

```javascript
var o = Object.create(null);
typeof o.toString(); // 'undefined'
```

### Object.getOwnPropertyDescriptor(obj,property)

该方法可以让我们详细查看一个属性的定义。甚至可以通过它一窥那些内置的及之前不可见的属性。

```javascript
Object.getOwnPropertyDescriptor(Object.prototype, 'toString');
// {
  // value: [Function: toString],
  // writable: true,
  // enumerable: false,
  // configurable: true
//}
```

### Object.getOwnPropertyNames(obj)

该方法返回一个数组，其中包含了当前对象所有属性的名称（字符串），不论它们是否可枚举。当然，也可以用`Object.keys()`来单独返回可枚举的属性。

```javascript
var obj = { name: '小明', age: 12, height: 180 }
console.log(Object.getOwnPropertyNames(obj)); // [ 'name', 'age', 'height' ]
console.log(Object.keys(obj)); // [ 'name', 'age', 'height' ]
```

### Object.prevenExtensions(obj) 与 Object.isExtensible(obj)

`preventExtensions()`方法用于禁止向某一对象添加更多属性，而`isExtensible()`方法则用于检查某对象是否可以被添加属性。

```javascript
var obj = { name: '小明', age: 12, height: 180 }
console.log(Object.isExtensible(obj));  // true
Object.preventExtensions(obj);
console.log(Object.isExtensible(obj)); // false
obj.school = '希望小学'
console.log(obj.school); // undefined
```

### Object.seal(obj) 与 Object.isSeal(obj)

`seal()`方法可以让一个对象密封，并返回被密封后的对象。`seal()`方法的作用与`preventExtensions()`基本相同，但除此之外，它还会将现有属性 设置成不可配置。也就是说，在这种情况下，我们只能变更现有属性的值，但不能删除或（用`defineProperty()`）重新配置这些属性，例如不能将一个可枚举的属性改成不可枚举。

```javascript
var person = {legs:2};
// person === Object.seal(person); // true
Object.isSealed(person); // true
Object.getOwnPropertyDescriptor(person, 'legs');
// {value: 2, writable: true, enumerable: true, configurable: false}
delete person.legs; // false (不可删除，不可配置)
Object.defineProperty(person, 'legs',{value:2});
person.legs; // 2
person.legs = 1;
person.legs; // 1 (可写)
Object.defineProperty(person, "legs", { get: function() { return "legs"; } });
// 抛出TypeError异常
```

### Object.freeze(obj)  与  Object.isFrozen(obj) （常用）

`freeze()`方法用于执行一切不受`seal()`方法限制的属性值变更。`Object.freeze()` 方法可以冻结一个对象，冻结指的是不能向这个对象添加新的属性，不能修改其已有属性的值，不能删除已有属性，以及不能修改该对象已有属性的可枚举性、可配置性、可写性。也就是说，这个对象永远是不可变的。该方法返回被冻结的对象。

```javascript
var obj = { name: '小明', age: 12, height: 180 }
console.log(Object.isFrozen(obj)); // false 未冻结
Object.freeze(obj) // 进行冻结
console.log(Object.isFrozen(obj));  // true 已冻结
delete obj.name // 进行删除
console.log(obj.name); // 小明 无法删除还是存在
```

## ES6 中新增的 Object 属性

### Object.is(value1, value2) 

该方法用来比较两个值是否严格相等。它与严格比较运算符（===）的行为基本一致。不同之处只有两个：一是`+0`不等于`-0`，二是`NaN`等于自身。

```javascript
console.log(Object.is({}, {})); // false
console.log(Object.is(+0, -0)); // false
console.log(Object.is(NaN, NaN)) // true
console.log(Object.is('小明', '小明'));  // true
```

### Object.assign(target, ...sources)

该方法用来源对象（`source`）的所有可枚举的属性复制到目标对象（`target`）。它至少需要两个对象作为参数，第一个参数是目标对象`target`，后面的参数都是源对象（`source`）。只有一个参数不是对象，就会抛出`TypeError`错误。

```javascript
var target = {a: 1};
var source1 = {b: 2};
var source2 = {c: 3};
obj = Object.assign(target, source1, source2);
target; // {a:1,b:2,c:3}
obj; // {a:1,b:2,c:3}
target === obj; // true
// 如果目标对象与源对象有同名属性，或多个源对象有同名属性，则后面的属性会覆盖前面的属性。
var source3 = {a:2,b:3,c:4};
Object.assign(target, source3);
target; // {a:2,b:3,c:4}
```

> `Object.assign`只复制自身属性，不可枚举的属性（`enumerable`为`false`）和继承的属性不会被复制。

```javascript
Object.assign({b: 'c'},
    Object.defineProperty({}, 'invisible', {
        enumerable: false,
        value: 'hello'
    })
);
// {b: 'c'}
```

> 属性名为`Symbol`值的属性，也会被`Object.assign()`复制。

```javascript
Object.assign({a: 'b'}, {[Symbol('c')]: 'd'});
// {a: 'b', Symbol(c): 'd'}
```

> 对于嵌套的对象且 key 值相同，`Object.assign()`的处理方法是替换，而不是添加。

```javascript
Object.assign({a: {b:'c',d:'e'}}, {a:{b:'hello'}});
```

> 对于数组，`Object.assign()`把数组视为属性名为 0、1、2 的对象进行替换操作。

```javascript
Object.assign([1,2,3], [4,5]); // [4,5,3]
```

### Object.getOwnPropertySymbols(obj) 

该方法会返回一个数组，该数组包含了指定对象自身的（非继承的）所有 `symbol` 属性键。该方法和 `Object.getOwnPropertyNames()` 类似，但后者返回的结果只会包含字符串类型的属性键，也就是传统的属性名。

```javascript
Object.getOwnPropertySymbols({a: 'b', [Symbol('c')]: 'd'});
// [Symbol(c)]
```

### Object.setPrototypeOf(obj, prototype)

该方法设置一个指定的对象的原型 ( 即, 内部`[[Prototype]]`属性）到另一个对象或 `null`。`__proto__`属性用来读取或设置当前对象的`prototype`对象。目前，所有浏览器（包括`IE11`）都部署了这个属性。

```javascript
// ES6写法
var obj = {
    method: function(){
        // code ...
    }
};
// obj.__proto__ = someOtherObj;
// ES5写法
var obj = Object.create(someOtherObj);
obj.method = function(){
    // code ...
};
```

该属性没有写入`ES6`的正文，而是写入了附录。`__proto__`前后的双下划线说明它本质上是一个内部属性，而不是正式对外的一个 API。无论从语义的角度，还是从兼容性的角度，都不要使用这个属性。而是使用`Object.setPrototypeOf()`（写操作），`Object.getPrototypeOf()`（读操作），或`Object.create()`（生成操作）代替。

在实现上，`__proto__`调用的`Object.prototype.__proto__`。`Object.setPrototypeOf()`方法的作用与`__proto__`作用相同，用于设置一个对象的`prototype`对象。它是`ES6`正式推荐的设置原型对象的方法。

## 在`ES8`中新增的`Object`属性

### Object.getOwnPropertyDescriptors(obj)

该方法基本与`Object.getOwnPropertyDescriptor(obj, property)`用法一致，只不过它可以用来获取一个对象的所有自身属性的描述符。

```javascript
console.log(Object.getOwnPropertyDescriptors(Object.prototype));
// 打印结果如下：
{
  constructor: {
    value: [Function: Object],
    writable: true,
    enumerable: false,
    configurable: true
  },
  __defineGetter__: {
    value: [Function: __defineGetter__],
    writable: true,
    enumerable: false,
    configurable: true
  },
  __defineSetter__: {
    value: [Function: __defineSetter__],
    writable: true,
    enumerable: false,
    configurable: true
  },
  hasOwnProperty: {
    value: [Function: hasOwnProperty],
    writable: true,
    enumerable: false,
    configurable: true
  },
  __lookupGetter__: {
    value: [Function: __lookupGetter__],
    writable: true,
    enumerable: false,
    configurable: true
  },
  __lookupSetter__: {
    value: [Function: __lookupSetter__],
    writable: true,
    enumerable: false,
    configurable: true
  },
  isPrototypeOf: {
    value: [Function: isPrototypeOf],
    writable: true,
    enumerable: false,
    configurable: true
  },
  propertyIsEnumerable: {
    value: [Function: propertyIsEnumerable],
    writable: true,
    enumerable: false,
    configurable: true
  },
  toString: {
    value: [Function: toString],
    writable: true,
    enumerable: false,
    configurable: true
  },
  valueOf: {
    value: [Function: valueOf],
    writable: true,
    enumerable: false,
    configurable: true
  },
  __proto__: {
    get: [Function: get __proto__],
    set: [Function: set __proto__],
    enumerable: false,
    configurable: true
  },
  toLocaleString: {
    value: [Function: toLocaleString],
    writable: true,
    enumerable: false,
    configurable: true
  }
}
```

### Object.values(obj)

`Object.values()` 方法与`Object.keys`类似。返回一个给定对象自己的所有可枚举属性值的数组，值的顺序与使用`for...in`循环的顺序相同 ( 区别在于`for-in`循环枚举原型链中的属性 )。

```javascript
var obj = {a:1,b:2,c:3};
Object.keys(obj); // ['a','b','c']
Object.values(obj); // [1,2,3]
```

### Object.entries(obj)

`Object.entries()` 方法返回一个给定对象自己的可枚举属性`[key，value]`对的数组，数组中键值对的排列顺序和使用 `for...in` 循环遍历该对象时返回的顺序一致（区别在于一个`for-in`循环也枚举原型链中的属性）。

```javascript
var obj = {a:1,b:2,c:3};
Object.keys(obj); // ['a','b','c']
Object.values(obj); // [1,2,3]
Object.entries(obj); // [['a',1],['b',2],['c',3]]
```

## 在`ES10`中新增的`Object`属性

### Object.fromEntries(iterable) 

`Object.fromEntries()`方法返回一个给定可迭代对象（类似`Array`、`Map`或其他可迭代对象）对应属性的新对象。

`Object.fromEntries()` 是 `Object.entries()`的逆操作。

```javascript
var obj = { name: '小明', age: 12, height: 180 }

var objList = Object.entries(obj);
console.log(objList); // [ [ 'name', '小明' ], [ 'age', 12 ], [ 'height', 180 ] ]
var newObj = Object.fromEntries(objList)
console.log(newObj); // { name: '小明', age: 12, height: 180 }
var entries = new Map([
    ['name', '小明'],
    ['age', 18]
  ]);
console.log(Object.fromEntries(entries)); // { name: '小明', age: 18 }
```

