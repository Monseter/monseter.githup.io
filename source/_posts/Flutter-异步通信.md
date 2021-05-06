---
title: Flutter 异步通信
typora-root-url: ./
date: 2021-04-25 10:20:08
tags: Flutter 异步通信
categories: Flutter
---

## Flutter 异步通信及更新数据

在 Flutter 中可用于异步通信的方案有如下：

- Provider

  [Provider 异步通信、Provider 状态管理]: https://biglead.blog.csdn.net/article/details/109181691

- ValueNotifier

- Stream 

  [StreamController 的使用详情]: https://biglead.blog.csdn.net/article/details/109153078

- Bloc 

  [BloC 异步通信、BlocBuilder基本使用、BlocProvide 初探]: https://biglead.blog.csdn.net/article/details/109173443

### ValueNotifier 的基本使用

ValueNotifier 需要结合组件 ValueListenableBuilder 来使用。

```dart
/// 第一步 定义 ValueNotifier  这里传递的数据类型为 String
ValueNotifier<String> _testValueNotifier = ValueNotifier<String>('');
```

```dart
//第二步定义 数据变化后监听的 Widget
Widget buildValueListenableBuilder() {
  return ValueListenableBuilder(
    ///数据发生变化时回调
    builder: (context, value, child) {
      return Text(value);
    },
    ///监听的数据
    valueListenable: _testValueNotifier,
    child: Text(
      '未登录',
      style: TextStyle(color: Colors.red),
    ),
  );
}
```

```dart
///第三步就是数据变化后
 void testFunction() {
   ///赋值更新
   _testValueNotifier.value = '传递的测试数据';
 }

```

在上述代码片段中，传递更新的数据是一个String，当然在业务开发场景中，会有更多自定义的 Model，直接替换上述的String就可以。

### 自定义 ValueNotifier 局部刷新

如有一个用户数据类型的Model定义如下 ：

```dart
///实际中变量可能足够的多
class UserInfo {
  String name;
  int age ;
}
```

我们期望只修改其中的如 age 一个属性的值，此时就需要自定 ValueNotifier ，如下代码 所示：

```dart
///自定义 ValueNotifier
/// UserInfo 为数据类型
class UserNotifier extends ValueNotifier<UserInfo> {

  UserNotifier(UserInfo userInfo): super(userInfo);

  void setName(String name) {
    ///赋值 这里需要注意的是 如果没有给 ValueNotifier 赋值 UserInfo 对象时
    /// value 会出现空指针异常
    value.name =name;
    ///通知更新
    notifyListeners();
  }

}
```

然后在使用的时候，创建 UserNotifier 如下 ：

```dart
///第一步
UserNotifier _testUserNotifier = UserNotifier(UserInfo(name: "", age: 0));
```

构建 buildUserListenableBuilder

```dart
 ///第二步 定义
 Widget buildUserListenableBuilder() {
   return ValueListenableBuilder(
     ///数据发生变化时回调
     builder: (context, value, child) {
       return Text("姓名是：${value.name}  年龄是: ${value.age}");
     },
     ///监听的数据
     valueListenable: _testUserNotifier,
   );
 }
```

当数据变化时进行更新操作

```dart
 void testUserFunction() {
   _testUserNotifier.setName("李四");
 }
```

应用场景：如实际项目开发中的修改用户数据，只修改了其中的一个属性数据，可以考虑使用这种方法，当然有很多情况大家是不考虑这种细节的，直接全部更新的，但是所有的细节综合起来，就解决了 你的应用为什么体验总是差点的问题。