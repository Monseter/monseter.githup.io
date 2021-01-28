---
title: Dart面向对象
typora-root-url: ./
date: 2021-01-28 15:42:23
tags: [Dart,Dart面向对象]
categories: 编程语言
---
## 类与对象，声明、创建及基本特性

- 继承

- 构造方法及初始化列表

- 静态成员及对象操作符的使用

### 继承

使用关键字**class**创建一个类；

使用关键字**extends**继承一个类；

子类会继承父类可见的属性和方法，不会继承构造方法；

子类能够复写父类的方法，计算属性方法setter getter；

单继承，多态性。

详情demo

```dart
void main () {
  var sonClass = new son();
  sonClass.fatherWay();
  sonClass.age = 20;
  print(sonClass.isAdult);
  print(sonClass.name);
}
// 这里是父类定义了一些属性及方法
class father {
  String name;
  int age;
  String birthday;

  bool get isAdult => age >18;// 计算属性

  fatherWay () {
    print("this is father class");
  }
}
// 这里是子类继承父类
class son extends father {
  String name = "class name is son";

  @override // 复写修饰符,用于复用父类方法或者修改父类方法，可以省略
  fatherWay() {
    // TODO: implement fatherWay
    print("this is son class ");
//    return super.fatherWay();// super 表示直接执行父类方法
  }
}
```

### 计算属性

顾名思义，计算属性的值是通过计算而来，本身不存储值；

计算属性值，其实是通过计算转换到其它实例变量。

详情demo

```dart
void main () {
  var rect = new renderRect();
  rect.width = 20;
  rect.height = 10;
  print(rect.area); // 对rect实例属性值进行计算属get返回一个需要的值
  rect.area = 400;
  print(rect.width); // 20
}

class renderRect {
  num width;
  num height;
  // get计算属性需要一个返回值类型加关键字加方法名（不需要做任何传参操作）最后一个表达式或者花括号也可以
  num get area => width*height;
  // set 不能直接对变量赋值操作，永远记得它们是计算属性方法！！！
  set area(value) => width = value/20;
}

说明：set 计算方法，是对get area方法得到的返回值进行操作的，set area方法中传的参数就是area所获取的值。所以可以说，get针对的是对变量做逻辑处理，而set针对的是动态给变量赋值。
```

### 类构造方法

默认构造方法；

自定义构造类方法（常用）；

构造方法不能重载；

使用命名构造方法，可以实现多个构造方法（解决了构造方法不能重载问题）；

使用**类名.方法名**的形式实现

详情demo

```dart
// 默认的构造方法
void main () {
  var father = new Father();
}
class Father {
  String name;
  num age;

  work() {
    print("this is class father");
  }
}

// 自定义构造方法
void main () {
  var father = new Father("tom",20); // 自定义构造方法，参数必须要给的！！！
}
class Father {
  String name;
  num age;
//  Father (String name,int age) {
//    this.name = name;
//    this.age = age;
//  }

  // 上面的自定义构造方法可以使用语法糖,更简洁下面是上面的缩写形式，下面是语法糖形式
  Father(this.name,this.age);
  
  work() {
    print("this is class father");
  }
}

// 命名构造方法 类名.方法名

var father = new Father("tom",20); // 自定义构造方法，参数必须要给的！！！
  var father1 = new Father.age(20); // 生成命名构造方法实例
  var father2 = new Father.name("Jim"); // 生成命名构造方法实例


Father(this.name,this.age);
  Father.name(this.name); // 命名构造方法
  Father.age(this.age);  // 命名构造方法
```

### 常量类构造方法

如果类是不可变状态，可以把对象定义为**编译时常量**；

使用**const** 声明构造方法，并且所有变量都为**final**

##### 使用**const**声明对象（实例），const可以省略

详情demo

```dart
const constClass = const ConstClass(20,"Anna"); // 创建常量类的实例

// 常量类构造方法
class ConstClass {
  // 所有变量只能被赋值一次
  final int age;
  final String name;
  const ConstClass(this.age,this.name);
}
```

### 工厂构造方法

工厂构造方法类似于设计模式中的工厂模式；

在构造方法前添加关键字**factory**实现一个工厂构造方法；

在工厂方法中可以直接返回一个对象。别的构造方法无法返回一个对象。

### 初始化列表

初始化列表会在构造方法体执行之前执行；

使用逗号分隔初始化表达式；

初始化列表常用于设置**final**变量的值。

详情demo

```dart
class Father {
  String name;
  num age;
  final String gender;
 
  Father.withFinal(Map map) : name=map['name'],age = map['age'], gender = map['gender'] {
    // 初始化列表主要用于对 final 变量值进行类构造前的赋值
  }
}
```

### 静态成员

使用**static**关键字来实现类级别的变量和函数；

静态成员不能访问非静态成员，非静态成员可以访问静态成员；

类中的常量需要使用 **static** **const** 声明。 

详情demo

```dart
void main () {
  var page = new Page();
  page.scrollUp(); // 无法访类级别的方法
}

class Page {
   static const int maxPage = 10; // 类级别变量
   static int currentPage = 1; // 类级别变量

   // 下滑  这里就是类级别的函数方法
  static void scrollDown () { // 静态成员无法访问非静态成员
    currentPage = 1;
  }
  // 上滑
  scrollUp () { // 非静态成员可以访问静态成员
    currentPage++;
  }
}
```

### 对象操作符

条件操作符  **？**

```dart
void main () {
  var person = new Person();
  person?.name; // 用于判断该对象属性或者方法是否存在，主要不会报错
  person?.work();
}
class Person {
  int age;
  String name;
  work() {
    print("this is person");
  }
}
```

类型转换：**as** 

指定类型： **is**  **!is**

级联操作：.. (重点)

级联demo

```dart
void main () {
//  var person = new Person();
//  person..name = "Tom"
//        ..age = 10
//        ..work();

  // 还可以简写
  new Person()..name ="Jim" ..age = 2 ..work();
}
class Person {
  int age;
  String name;
    
  work() {
    print("this is person,$age");
  }
}
```

### 对象call 的方法

可以把对象实列当成方法调用，**call**为关键字

详情demo

```dart
void main () {
  var person = new Person();
  int number = 10;
  person.age = 20;
  person(number); // 对象变成函数，自动执行call方法，可进行传参操作
}
class Person {
  int age;
  String name;
  call(age) { // 当对象转变成函数方法使用的时候，进行回调操作
    print("this is person,$age");
  }
}
```

