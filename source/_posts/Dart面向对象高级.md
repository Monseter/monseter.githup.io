---
title: Dart面向对象高级
typora-root-url: ./
date: 2021-01-28 15:31:43
tags: [Dart, Dart面向对象高级]
categories: 编程语言
---
## 构造方法执行顺序

（1）父类构造方法在子类构造方法体开始执行的位置调用；

（2）如果有初始化列表，初始化列表会在父类构造方法之前执行

## 抽象类

抽象类使用**abstract**表示，不能直接被实列化；

抽象方法不用**abstract**修饰，无实现；

抽象类可以没有抽象方法；

有抽象方法的类一定得声明为抽象类

```dart
void main () {
  var student = new Student();
  student.run();
}
abstract class Person { // 抽象类无法被实列化，只有被继承才可以
  void run (); // 抽象方法
}

class Student extends Person {
  void run() {
    print("this is student");
  }
}
```

## 接口 （implements）

类和接口是统一的，类就是接口；

每个类都隐式的定义了一个包含所有实列成员的接口；

如果是复用已有类的实现，使用继承（extends）;

如果只是使用已有类的外在行为，使用接口（implements）

说明：当我们使用类做为接口时候，最好使用抽象类，因为里面可以做抽象方法，而抽象方法在被继承时候更像是一种接口调用。

```dart
void main () {
  var student = new Student();
  student.run();
}

abstract class Person { // 抽象类无法被实列化，只有被继承才可以
  void run (); // 抽象方法
}

class Student implements Person { // 使用关键字 implements
  void run() {
    print("this is student");
  }
}
```

## Mixins (with)

**说明：dart语言的继承方式是单继承不存在多继承！**

类似于多继承，是在多类继承中重用一个类代码的方式，使用关键字 **with** ；

作为Minx的类不能有**显示声明构造方法；**

作为Minx的类只能继承自Obejct

```dart
void main () {
  var d = new D();
  d.a(); // this is a
  d.b(); // this is b
  d.c(); // this is c
}
class A {
  a() {
    print("this is a");
  }
}
class B {
  b () {
    print("this is b");
  }
}
class C {
  c () {
    print("this is c");
  }
}
class D extends A with B,C{
}

```

## 混合模式

功能比较强大，可以组合几个类构成一个功能模块

```dart
void main () {
  var car = new Car();
  car.size = "big";
  car.name = "玲珑轮胎";
  car.work();
}
// 抽象类
abstract class Engine {
  void work(); // 抽象方法
}
// 把抽象类当成接口
class ElectricEngine implements Engine {
  // 改写父类方法
    @override
  void work() {
    // TODO: implement work
  }
}
class OilEngine implements Engine {
  @override
  void work() {
    // TODO: implement work
    print("this is oilEngine");
  }
}
class tyre {
  String name;
  String size;
  way () {
    print("$name,$size");
  }
}

class Car = tyre with OilEngine; // 形成新的类模块
```

## 操作符覆写

覆写操作符需要在类中定义；

返回类型	operator  操作符  （参数1，参数2.....） {

​			实现体.....

​			return   返回值

}

## 枚举

枚举是一种有穷序列集的数据类型；

使用关键字**enum**定义一个枚举；

常用于**代替常量**，控制语句等

```dart
void main () {
//  const spring = 1;
//  const summer = 2;
//  const automn = 3;
//  const winter = 4;

  var currentSeason = Season.spring; // 直接点就可以了

  switch (currentSeason) {
    case Season.spring:
      print("1~3月");
      break;
    case Season.summer:
      print("4~6月");
      break;
    case Season.automn:
      print("7~9月");
      break;
    case Season.winter :
      print("10~12月");
      break;
  }
}
// 枚举代替常量
enum Season {
  spring,
  summer,
  automn,
  winter
}
```

### 枚举特性

index 从0 开始，依次累加；

不能指定原始值；

不能添加方法

## 泛型

Dart 中类型是可选的，可使用泛型限定类型；

```dart
// 使用关键符号<>
  var list = new List<String>(); // 泛型指定这个列表只能存放字符串
```

使用泛型能有效的减少代码重复；

### 泛型的使用

类的泛型，见上面demo

方法的泛型，在类中的方法名后面使用即可

