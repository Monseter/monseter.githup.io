---
title: Flutter学习-常用字段属性值详解
typora-root-url: ./
date: 2021-04-14 15:46:24
tags:
categories:
---

# EdgeInsets

文档 API 解释说明如下：

- `fromLTRB(double left, double top, double right, double
  bottom)`

  分别指定四个方向的填充

- `all(double value)`

  所有方向均使用相同数值填充

- `only({left, top, right ,bottom })`

  可以设置具体某个方向的填充（可以同时指定多个方向）

- `symmetric({vertical, horizontal})`

  用于设置对称方向的填充，`vertical`（垂直方向） 指 top 和 bottom，`horizontal`（水平方向）left 和 right

demo

```dart
class PaddingTestRoute extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Padding(
      //上下左右各添加16像素补白
      padding: EdgeInsets.all(16.0),
      child: Column(
        //显式指定对齐方式为左对齐，排除对齐干扰
        crossAxisAlignment: CrossAxisAlignment.start,
        children: <Widget>[
          Padding(
            //左边添加8像素补白
            padding: const EdgeInsets.only(left: 8.0),
            child: Text("Hello world"),
          ),
          Padding(
            //上下各添加8像素补白
            padding: const EdgeInsets.symmetric(vertical: 8.0),
            child: Text("I am Jack"),
          ),
          Padding(
            // 分别指定四个方向的补白
            padding: const EdgeInsets.fromLTRB(20.0,.0,20.0,20.0),
            child: Text("Your friend"),
          )
        ],
      ),
    );
  }
}
```

## MediaQuery 控件

通常情况下，不会直接使用`MediaQuery` 当作一个控件，而是使用 `MediaQuery.of(context)` 获取当前设备的信息，用法如下：

```dart
final media = MediaQuery.of(context);
```

> 注意：
>
> 此方式必须放在 ``MediaQuery` ` 作用域内，否则会抛出异常。`MateralApp` 和 `WidgetsApp`都引入了  MediaQuery，并且随着屏幕的变化而导致重建，比如旋转屏幕、弹出输入框等。

### MediaQueryData

`MediaQueryData` 是 `MediaQuery.of` 获取的数据类型，参数输入下：

| 属性                  |                             说明                             |
| :-------------------- | :----------------------------------------------------------: |
| size                  | 逻辑像素，并不是物理像素，类似于 Android 中的 dp，逻辑像素会在不同大小的的手机上显示的大基本一样，物理像素 = size*devicePixeRatio |
| devicePixelRatio      |           单位逻辑像素的物理像素数量，即设备像素比           |
| textScaleFactor       |    单位逻辑像素字体像素数，如果设置1.5则比指定的字体大50%    |
| platformBrightness    | 当前设备的亮度模式，比如在Android Pie手机上进入省电模式，所有的App将会使用深色（dark）模式绘制。 |
| viewInsets            | 被系统遮挡的部分，通常指键盘，弹出键盘，`viewInsets.bottom`表示键盘的高度。 |
| padding               |       被系统遮挡的部分，通常指“刘海屏”或者系统状态栏。       |
| viewPadding           | 被系统遮挡的部分，通常指“刘海屏”或者系统状态栏，此值独立于`padding`和`viewInsets`，它们的值从`MediaQuery`控件边界的边缘开始测量。在移动设备上，通常是全屏。 |
| systemGestureInsets   | 显示屏边缘上系统“消耗”的区域输入事件，并阻止将这些事件传递给应用。比如在Android Q手势滑动用于页面导航（ios也一样），比如左滑退出当前页面。 |
| physicalDepth         |            设备的最大深度，类似于三维空间的Z轴。             |
| alwaysUse24HourFormat |                       是否是24小时制。                       |
| accessibleNavigation  | 用户是否使用诸如TalkBack或VoiceOver之类的辅助功能与应用程序进行交互，用于帮助视力有障碍的人进行使用。 |
| invertColors          |                      是否支持颜色反转。                      |
| highContrast          | 用户是否要求前景与背景之间的对比度高， iOS上，方法是通过“设置”->“辅助功能”->“增加对比度”。 此标志仅在运行iOS 13的iOS设备上更新或以上。 |
| disableAnimations     |               台是否要求尽可能禁用或减少动画。               |
| boldText              |                    平台是否要求使用粗体。                    |
| orientation           |                       是横屏还是竖屏。                       |

### 使用场景

- 根据尺寸构建不同的布局

  SafeArea控件就是通过`MediaQuery.of`来实现的，平板和手机的（或者横屏和竖屏）布局可能是不一样的：

  ```dart
  var screenSize = MediaQuery.of(context).size;
  if(screenSize.width>oneColumnLayout){
    //平板布局
  }else{
    //手机布局
  }
  ```

- 系统字体变化

  很多App都有一个功能就是调节字体大小，通过MediaQuery来实现，实现如下：

  字体变大一倍

  ```dart
  @override
    Widget build(BuildContext context) {
      var _data = MediaQuery.of(context).copyWith(textScaleFactor: 2.0);
      return Scaffold(
        appBar: AppBar(
          title: Text('老孟'),
        ),
        body: MediaQuery(
          data: _data,
          child: Text('字体变大'),
        ),
      );
    }
  ```


## 参考学习 Demo

- 布局学习，`Widget` 样式封装，组件封装，自定义登录输入框以及错误提示信息

  https://github.com/abuanwar072/E-commerce-Complete-Flutter-UI

