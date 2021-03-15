---
title: JS 中 new 操作符原理及 this 概念
typora-root-url: ./
date: 2021-03-11 16:20:50
tags:
categories:
---

## this 的概念

`this` 就是指针，指向我们调用函数的对象，`this` 是 `JavaScript` 中的一个关键字，它是函数运行时，在函数体内自动生成的一个对象，只能在函数体内部使用。

- 全局环境中的 `this` 指向全局变量（`window`）

- 函数中的 `this` 由调用函数的方式来决定

  1. 如果函数是独立调用的，在严格模式下（use strict）是指向undefined的，在非严格模式下，指向window；
  2. **如果这个函数是被某一个对象调用，那么this指向被调用的对象**

- 构造函数与原型里面的 `this`

  造函数里的 `this` 以及原型里的 `this` 对象指的都是生成的实例；（由new决定的），通过 `new` 操作符可以初始化一个 `constructor` 的指向，`new` 的作用就是创建一个对象的实例，`constructor` 也就指向了一个新的执行环境“在这个对象之中。后面 `new` 原理再详解。

- 箭头函数按词法作用域来绑定它的上下文，所以this 实际上会引用原来的上下文

  简单理解就是：箭头函数会保持它当前执行上下文的词法作用域不变，而普通函数则不会，箭头函数从包含它的词法作用域中继承了 `this` 的值。

### 看一下案例示题（面试题）

1. 示题1

   ```javascript
   var obj = {   
    a: 10,
    b: this.a + 10, //这里的this指向window(全局)，a为undefined  ==>  undefined + 20 = NaN
    fn: function () {
        return this.a;  // 指向 obj 内部的 a
      }
   }
   console.log(obj.b); //NaN
   console.log(
   obj.fn(),  //10
   obj.fn   //fn
   );   
   ```

2. 示题2

   ```javascript
   
   function a(xx) {         
       this.b = xx; 
   } 
   var o = {}; 
   
   a.apply(o, [5]); // 等价于 o.b = 5 <=> this.b this这里就是指 o 这个对象
   // funciton a(xx) {
   //    o.b = xx
   // }
   
   alert(a.b);    // undefined 
   alert(o.b);    // 5
   ```

   o 本来是一个空对象，经过 `a.apply(o,[5])` 后变为 `{ b: 5 }`。原理：`apply` 改变了 `this` 指向，它令 `a` 构造函数在 `o` 对象内部执行操作，上述提到的 “**如果这个函数是被某一个对象调用，那么this指向被调用的对象**” ，就是这句话的意思。

3. 示题3

   ```javascript
   var obj = {
       a: 10,
       getA: function () {
           console.log(this.a);
       }
   }
   obj.getA() // 10
   var test = obj.getA;
   test() // undefined
   ```

   > 上述示题就是把 `getA` 单独拿出来调用的，所以 this 指向 undefined

4. 示题4

   ```javascript
   // 声明一个构造函数 
    function Person(name, age) {
       this.name = name;
       this.age = age;
       console.log(this);      //与下面的this是一样的，都是Person
   }   
   // Person();           //this 指向window
   Person.prototype.getName = function () {
       console.log(this);      //与上面的this是一样，都是Person
   }; 
   var p1 = new Person("test", 18);
   p1.getName()
   
   ```

5. 示题5

   ```javascript
   var obj = {
      foo: "test",
      fn: function(){
          var mine = this;
          console.log(this.foo);       //test
          console.log(mine.foo);       //test
   
          (function(){
             console.log(this.foo);    //undefined
             console.log(mine.foo);    //test
          })();  
      } 
   };
   obj.fn();
   
   ```

   > (1) 在外部函数中， this 和 mine 两者都指向了obj，因此两者都可以正确地引用访问foo
   >
   > (2) 在内部函数中， this 不再指向 obj ， this.foo 没有在内部函数中被定义，而指向到本地的变量 mine保持在范围内，并且可以访问（在ES5之前，内部函数的this将指向全局的 window对象； 而ES5中，内部函数中的this是未定义的。）

6. 示题6

   ```javascript
   function foo() {
       console.log(this.a);
   }
   var obj1 = {
       a: 3,
       foo: foo
   };    
   var obj2 = {
       a: 5,
       foo: foo
   };
   obj1.foo();     //3
   obj2.foo();     //5
   
   obj1.foo.call(obj2);    //5
   obj2.foo.call(obj1);    //3
   ```

   > *new 绑定的优先级高于隐含绑定，并且 new 和 call / apply 不能同时使用，所以  new  foo.call(obj1) 是不允许的，也就是不能直接对比测试 new绑定 和 明确绑定*

### call、apply、bind 区别

- 共同点

  **call()、apply()、bind() 都是用来重定义 this 这个对象的！**

  ```javascript
  var name = '小明', age = 17;
  var obj = {
      name: '小红',
      age: this.age,
      fn: function () {
          console.log(`${this.name}年龄${this.age}`);
      }
  }
  var exmp = {
      name: '大熊',
      age: 18
  };
  
  obj.fn.apply(exmp)  // 大熊年龄18
  obj.fn.call(exmp) // 大熊年龄18
  obj.fn.bind(exmp)() // 大熊年龄18
  ```

  > *以上出了 bind 方法后面多了个 () 外 ，结果返回都一致！*
  >
  > *由此得出结论，bind 返回的是一个新的函数，你必须调用它才会被执行。*

- 不同点

  传参不同

  ```javascript
  var name = '小明', age = 17;
  var obj = {
      name: '小红',
      age: this.age,
      fn: function (from,to) {
          console.log(`${this.name}年龄${this.age}来自${from}去往${to}`);
      }
  }
  var exmp = {
      name: '大熊',
      age: 18
  };
  
  obj.fn.apply(exmp,['apply','上海']) // 大熊年龄18来自apply去往上海
  obj.fn.call(exmp, 'call', '上海') // 大熊年龄18来自call去往上海
  obj.fn.bind(exmp,'bind','上海')()  // 大熊年龄18来自bind去往上海
  obj.fn.bind(exmp,['bind','上海'])()  // 大熊年龄18来自bind,上海去往undefine
  ```

  从上面四个结果不难看出:

  call 、bind 、 apply 这三个函数的第一个参数都是 this 的指向对象，第二个参数差别就来了：

  call 的参数是直接放进去的，第二第三第 n 个参数全都用逗号分隔，直接放到后面 **obj.myFun.call(db,'成都', ... ,'string' )**。

  apply 的所有参数都必须放在一个数组里面传进去 **obj.myFun.apply(db,['成都', ..., 'string' ])**。

  bind 除了返回是函数以外，它 的参数和 call 一样。

  当然，三者的参数不限定是 string 类型，允许是各种类型，包括函数 、 object 等等！

## new 操作符

### 首先得知道 new 做了什么

```javascript
// 例子1
function Student(){
}
var student = new Student();
console.log(student); // {}
// student 是一个对象。
console.log(Object.prototype.toString.call(student)); // [object Object]
// 我们知道平时声明对象也可以用new Object(); 只是看起来更复杂
// 顺便提一下 `new Object`(不推荐)和Object()也是一样的效果
// 可以猜测内部做了一次判断，用new调用
/** if (!(this instanceof Object)) {
*    return new Object();
*  }
*/
var obj = new Object();
console.log(obj) // {}
console.log(Object.prototype.toString.call(student)); // [object Object]

typeof Student === 'function' // true
typeof Object === 'function' // true
```

从这里例子中，我们可以看出：一个函数用`new`操作符来调用后，生成了一个全新的对象。而且`Student`和`Object`都是函数，只不过`Student`是我们自定义的，`Object`是`JS`本身就内置的。再来看下控制台输出图，便于理解：

![image-20210311180203587](image-20210311180203587.png)

与`new Object()` 生成的对象不同的是`new Student()`生成的对象中间还嵌套了一层`__proto__`，它的`constructor`是`Student`这个函数。

```javascript
// 等价于下面
student.constructor === Student;
Student.prototype.constructor === Student;
```

> 小结：从这个简单例子来看，`new`操作符做了两件事：
>
> 1. 创建了一个全新的对象。
> 2. 这个对象会被执行`[[Prototype]]`（也就是`__proto__`）链接。

接下来我们再来看升级版的**例子2**：

```javascript
// 例子2
function Student(name){
    console.log('赋值前-this', this); // {}
    this.name = name;
    console.log('赋值后-this', this); // {name: '小明'}
}
var student = new Student('小明');
console.log(student); // {name: '小明'}
```

由此可以看出：这里 `Student` 函数中的 `this` 指向 `new Student()` 生成的对象 `student` 。

> 小结2：从这个例子来看，`new`操作符又做了一件事：
>
> 1. 生成的新对象会绑定到函数调用的`this`。

上述 的列子中函数都是没有返回值的，下面测下有函数值的情况：

```javascript
// 例子4
function Student(name){
    this.name = name;
    // Null（空） null
    // Undefined（未定义） undefined
    // Number（数字） 1
    // String（字符串）'1'
    // Boolean（布尔） true
    // Symbol（符号）（第六版新增） symbol

    // Object（对象） {}
        // Function（函数） function(){}
        // Array（数组） []
        // Date（日期） new Date()
        // RegExp（正则表达式）/a/
        // Error （错误） new Error()
    // return null;
}
var student = new Student('小明');
console.log(student); {name: '小明'}
```

测试这七种类型后MDN JavaScript类型，得出的结果是：前面六种基本类型都会正常返回`{name: '若川'}`，后面的`Object`(包含`Functoin`,`Array`, `Date`, `RegExg`, `Error`)都会直接返回这些值。即函数如果 `return []` 那么获取的就是 `[]` 而不是 `{name: '小明'}` 具体值。

> 小结3：
>
> 1. 如果函数没有返回对象类型`Object`(包含`Functoin`, `Array`, `Date`,`RegExg`, `Error`)，那么`new`表达式中的函数调用会自动返回这个新的对象。

### 总结 **new 运算符的运行过程**

`new` 运算符的主要目的就是为我们创建一个用户定义的对象类型的实例或具有构造函数的内置对象的实例（比如箭头函数就没有构造函数，所以是不能` new `的）。`new` 操作符的执行大概有以下几个步骤：

1. 创建一个新的空对象
2. 把新对象的` __proto__ `链接到构造函数的 `prototype `对象（每一个用户定义函数都有一个 `prototype` 属性指向一个对象，该对象有一个 `constructor` 属性指向该函数），让我们的公共属性和方法可以从原型上继承，不用每个实例都创建一次。
3. 将第一步创建的新的对象作为构造函数的` this `的上下文，执行构造函数，构造函数的执行让我们配置对象的私有属性和方法。
4. 执行构造函数，如果构造函数没有返回值或者返回值不是一个对象，则返回 `this`。

### new 操作符模拟实现

```javascript
/**
 * 模拟实现 new 操作符
 * @param  {Function} ctor [构造函数]
 * @return {Object|Function|Regex|Date|Error}      [返回结果]
 */
function newOperator(ctor){
    if(typeof ctor !== 'function'){
      throw 'newOperator function the first param must be a function';
    }
    // ES6 new.target 是指向构造函数
    newOperator.target = ctor;
    // 1.创建一个全新的对象，
    // 2.并且执行[[Prototype]]链接
    // 4.通过`new`创建的每个对象将最终被`[[Prototype]]`链接到这个函数的`prototype`对象上。
    var newObj = Object.create(ctor.prototype);
    // ES5 arguments转成数组 当然也可以用ES6 [...arguments], Aarry.from(arguments);
    // 除去ctor构造函数的其余参数
    var argsArr = [].slice.call(arguments, 1);
    // 3.生成的新对象会绑定到函数调用的`this`。
    // 获取到ctor函数返回结果
    var ctorReturnResult = ctor.apply(newObj, argsArr);
    // 小结4 中这些类型中合并起来只有Object和Function两种类型 typeof null 也是'object'所以要不等于null，排除null
    var isObject = typeof ctorReturnResult === 'object' && ctorReturnResult !== null;
    var isFunction = typeof ctorReturnResult === 'function';
    if(isObject || isFunction){
        return ctorReturnResult;
    }
    // 5.如果函数没有返回对象类型`Object`(包含`Functoin`, `Array`, `Date`, `RegExg`, `Error`)，那么`new`表达式中的函数调用会自动返回这个新的对象。
    return newObj;
}
```

最后用模拟实现的`newOperator`函数验证下之前的**例子**：

```javascript
// 例子3 多加一个参数
function Student(name, age){
    this.name = name;
    this.age = age;
    // this.doSth();
    // return Error();
}
Student.prototype.doSth = function() {
    console.log(this.name);
};
var student1 = newOperator(Student, '若', 18);
var student2 = newOperator(Student, '川', 18);
// var student1 = new Student('若');
// var student2 = new Student('川');
console.log(student1, student1.doSth()); // {name: '若'} '若'
console.log(student2, student2.doSth()); // {name: '川'} '川'

student1.__proto__ === Student.prototype; // true
student2.__proto__ === Student.prototype; // true
// __proto__ 是浏览器实现的查看原型方案。
// 用ES5 则是：
Object.getPrototypeOf(student1) === Student.prototype; // true
Object.getPrototypeOf(student2) === Student.prototype; // true
```

