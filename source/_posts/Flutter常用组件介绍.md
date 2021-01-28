---
title: Flutter常用组件
typora-root-url: ./
date: 2021-01-28 15:07:44
tags: [Flutter,组件]
categories: 移动端框架
---
## Flutter 图片组件

### 图片组件简介

图片组件是显示图像的组件，Image 组件有很多构造函数，这里我们只给大家讲两个

Image.asset， 本地图片
Image.network 远程图片

**Image 组件的常用属性**

|          名称          |   类型    |                             说明                             |
| :--------------------: | :-------: | :----------------------------------------------------------: |
|       alignment        | Alignment |                        图片的对齐方式                        |
| color 和 colorBlenMode |           | 设置图片的背景颜色，通常和 colorBlendMode 配合一起<br/>使用，这样可以是图片颜色和背景色混合。上面的图片就<br/>是进行了颜色的混合，绿色背景和图片红色的混合 |
|          fit           |  BoxFit   | tfit 属性用来控制图片的拉伸和挤压，这都是根据父容器来<br/>的。<br/>BoxFit.fill:全图显示，图片会被拉伸，并充满父容器。<br/>BoxFit.contain:全图显示，显示原比例，可能会有空隙。<br/>BoxFit.cover：显示可能拉伸，可能裁切，充满（图片要<br/>充满整个容器，还不变形）。 |

```dart
return Center(
	child: Container(
		child: Image.network( 										              "http://pic.baike.soso.com/p/20130828/20130828161137-1346445960.jpg",
  alignment: Alignment.topLeft, 
  color: Colors.red, 
  colorBlendMode: BlendMode.colorDodge, // repeat: ImageRepeat.repeatX, fit: BoxFit.cover, ), width: 300.0, height: 400.0, decoration: BoxDecoration(
color: Colors.yellow
), ), );
```

### Flutter 引入本地图片

![1582009813774](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1582009813774.png)

然后，打开 pubspec.yaml 声明一下添加的图片文件，**注意要配置对**

![1582009855601](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1582009855601.png)

最后，在代码中就可以用了

```dart
child: Container(
	child:Image.asset(
    	"images/a.jpeg",
        fit:BoxFit.cover
    )
)
```

### Flutter实现圆角以及实现圆形图片

实现圆形图片

```dart
// 实现圆角图片
        child: ClipOval(
          child: Image.network(
            "https://dss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=1694681277,1453280371&fm=26&gp=0.jpg",
            width: 100.0,
            height: 100.0,
          ),
        ),
```

## Flutter 列表组件

### 列表组件概述

列表布局时我们项目开发中最常用的一种布局方式。Flutter 中我们可以通过ListView 来定义列表项，支持垂直和水平方向展示。通过一个属性就可以控制列表的显示方向。列表有以下分类：

（1）垂直列表

（2）垂直图文列表

（3）水平列表

（4）动态列表

（5）矩阵式列表

### Flutter 列表参数

| 名称             | 类型               | 说明                                              |
| ---------------- | ------------------ | ------------------------------------------------- |
| scrollDirection  | Axis               | Axis.horizontal水平列表    Axis.vertical 垂直列表 |
| padding          | EdgeInsetsGeometry | 内边距                                            |
| resolve          | bool               | 组件反向排序                                      |
| children<widget> | ListTile           | <widget>[]                                        |

```dart
// 一般用ListTile 于 Subtitle 配合着使用  一个主标题一个子标题
return ListView(
      children: <Widget>[
        ListTile(
          leading: Image.network("http://img.cloud.ahwang.cn/a/thumb/10001/201909/e05e9808057fdea990c8d442c121767e.png!w270_h170.png"),
          title: Text(
            "滁州无新增新冠肺炎确诊病例和疑似病例",
            style: TextStyle(fontSize: 20,),
            ),
          subtitle: Text("新安晚报 安徽网 大皖客户端讯 2月18日，据滁州市卫健委消息，2020年2月17日0时至24时, 滁州市无新增新冠肺炎确诊病例和疑似病例。"),
        ),
```

不用ListTile ,我么可以用Container 这个组件搭配着使用,里面在嵌套着ListView都是可行的，每个Container对应如css 中 div,这样去想就明白很多了

```dart
 return ListView(
      children: <Widget>[
        Container(
          padding: EdgeInsets.fromLTRB(10, 10, 0, 0),
          height: 100,
          decoration: BoxDecoration(color: Colors.blue),
          child: ListView(
           scrollDirection: Axis.horizontal, 
            children: <Widget>[
              Container(
                width: 50,
                decoration: BoxDecoration(color: Colors.indigo),
              ),
```

###  Flutter ListView 列表组件、动态列表（动态循环数据）

**方法一**：自定义方法

```dart
// 自定义方法,私有的只有内部才能访问方法，返回值类型必须时widget
  List<Widget> _getData () {
    var templist = listData.map((value) {
      return ListTile(
        leading: Image.network(value["imageUrl"]),
        title: Text(value["title"]),
        subtitle: Text(value["author"]),
      );
    });
    return templist.toList();
  }

@override
  Widget build(BuildContext context) {
    // TODO: implement build
    return ListView(
      children: this._getData(),
    );
  } 
```

**方法二**：ListView.builder

```dart
return ListView.builder(
      itemCount: listData.length,
      itemBuilder: (context,index) {
        return ListTile(
          leading: Image.network(listData[index]["imageUrl"]),
          title: listData[index]["title"],
          subtitle: listData[index]["author"],
        );
      },
    ); 
```

## Flutter GridView 网格布局组件

当数据量很大的时候用矩阵方式排列比较清晰。此时我们可以用网格列表组件 GridView 实
现布局。

GridView 创建网格列表有多种方式，下面我们主要介绍两种。

### 常用属性

| 名称             | 类型                                            | 说明                                   |
| ---------------- | ----------------------------------------------- | -------------------------------------- |
| scrollDirection  | Axis                                            | 滚动方法                               |
| padding          | EdgeInsetGeometry                               | 内边距                                 |
| resolve          | bool                                            | 组件反向排序                           |
| crossAxisSpacing | double                                          | 水平子widget之间间距                   |
| mainAxisSpacing  | double                                          | 垂直子Widget之间间距                   |
| crossAxisCount   | int                                             | 一行的Widget数量                       |
| childAspectRatio | double                                          | 子Widget宽高比列                       |
| childern         |                                                 | <widger>[]                             |
| grdDelegate      | SliverGridDelegateWithFixedCrossAxisCount(常用) | 控制布局样式，主要用在GridView.builder |

##### （1）可以通过 GridView.count 实现网格布局 （方法与listview差不多）

```dart
 @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return GridView.count(
      crossAxisCount: 2,
      crossAxisSpacing: 10, // 水平子间距
      mainAxisSpacing: 10, // 垂直子间距
      padding: EdgeInsets.fromLTRB(10, 10, 10, 0),
      children: this._getData()
    );

// 自定义方法
  List<Widget> _getData () {
    var templist = listData.map((e){
      return Container(
          decoration: BoxDecoration(
            border: Border.all(color: Colors.grey)
          ),
          child: Column(
            children: <Widget>[
              Image.network(e["imageUrl"]),
              SizedBox(height: 20,),
              Text(e["title"])
            ],
          ),
        );
    });
    return templist.toList();
  }
```

**（2）GridView.Bulider**

基本上与ListView 第二个方法差不多



## Flutter 页面布局  Padding  Row  Column   Expanded组件

### Flutter Padding 组件

在 html 中常见的布局标签都有 padding 属性，但是 Flutter 中很多 Widget 是没有 padding 属
性。这个时候我们可以用 Padding 组件处理容器与子元素直接的间距。

| 属性    | 说明                               |
| ------- | ---------------------------------- |
| padding | padding值，EdgeInsetss设置填充的值 |
| child   | 子组件                             |

```dart
Padding(
	padding: EdgeInsets.fromLTRB(10, 10, 0, 0), 
    child: 		     Image.network('https://www.itying.com/images/flutter/2.png', 
    fit: BoxFit.cover
  ), 
),
```

### Flutter   Row 水平布局组件

| 属性               | 说明           |
| ------------------ | -------------- |
| mainAxisAlignment  | 主轴的排序方式 |
| crossAxisAlignment | 次轴的排序方式 |
| children           | 子组件元素     |

```dart
return Container(
	height: 700, 
    width: 500, 
    color: Colors.black26,
    child: Row(
		crossAxisAlignment: 			CrossAxisAlignment.center,   mainAxisAlignment:MainAxisAlignment.spaceEvenly, // crossAxisAlignment: CrossAxisAlignment.center, children: <Widget>[
IconContainer(Icons.home,color:Colors.red), IconContainer(Icons.search,color:Colors.blue), IconContainer(Icons.send,color:Colors.orange), ], ),
```

### Flutter Column  垂直布局组件

| 属性               | 说明             |
| ------------------ | ---------------- |
| mainAxisAlignment  | 主轴的的排序方式 |
| crossAxisAlignment | 次轴的排序方式   |
| children           | 子组件元素       |

```dart
Container(
  height: 700, 
  width: 500, 
  color: Colors.black26, 
  child: Column(
    crossAxisAlignment:      CrossAxisAlignment.center, mainAxisAlignment: MainAxisAlignment.spaceEvenly, // crossAxisAlignment: CrossAxisAlignment.center, children: <Widget>[
IconContainer(Icons.home,color:Colors.red), IconContainer(Icons.search,color:Colors.blue), IconContainer(Icons.send,color:Colors.orange), ],
```

### Flutter Expanded  类似Web中的 Flex 布局

Expanded 可以用在 Row 和 Column 布局中

| 属性  | 说明                          |
| ----- | ----------------------------- |
| flex  | 元素站整个父Row/Coulumn的比列 |
| child | 子元素                        |

```dart
children: <Widget>[
  Expanded(flex:2,child:    IconContainer(Icons.home)),   SizedBox(width: 10), Expanded(flex: 3,child: IconContainer(Icons.search)), // SizedBox(width: 10), // Expanded(child: IconContainer(Icons.send))
],
```

## Flutter Stack 堆叠组件

Stack 表示堆的意思，我们可以用 Stack 或者 Stack 结合 Align 或者 Stack 结合 Positiond 来实现页面的定位布局

### Flutter  Stack Align 组件

Stack 组件中结合 Align 组件可以控制每个子元素的显示位置

| 属性      | 说明                     |
| --------- | ------------------------ |
| alignment | 配置所有子元素的显示位置 |
| children  | 子组件                   |

```dart
Stack(
      alignment: Alignment.center,
      children: <Widget>[
        Container(
          height: 300,
          width: 300,
          color: Colors.yellow,
        ),
        Text("这是一行文本"),
      ],
    );
```



###  Flutter  Stack   Positioned

Stack 组件中结合 Positioned 组件也可以控制每个子元素的显示位置

| 属性   | 说明                 |
| ------ | -------------------- |
| top    | 子元素距离顶部的距离 |
| bottom | 子元素距离底部的距离 |
| left   | 子元素距离左侧距离   |
| right  | 子元素距离右侧距离   |
| child  | 子组件               |

## Flutter   AspecRatio、Card 卡片组件

商品图文列表demo

```dart
ListView(
      padding: EdgeInsets.all(10),
      children: <Widget>[
        Card(
          child: Column(
            children: <Widget>[
              AspectRatio(  // 用于设定子元素宽高比例组件
                aspectRatio: 10/5,
                child: Image.network("https://www.itying.com/images/flutter/1.png",fit: BoxFit.cover,),
              ),
              ListTile(
                leading: CircleAvatar( // 专门处理头像组件
                  backgroundImage: NetworkImage("https://www.itying.com/images/flutter/1.png"),
                ),
                title: Text("xxxxxxxxxxx"),
                subtitle: Text("xxxxxxxxxx"),
              )
            ],
          ),
        )
      ],
    );
```

### Card 组件

相当于一个容器，起到一个包裹起来的外官效果如同卡片一样，所有的组件放置其 **child**下面

### AspectRatio 组件

该组件是对于子组件可以设定宽高比例使用  **aspectRatio**属性设定比列值，使用**child**定义子组件。它这个宽高比列是依赖父元素宽度

### CircleAvatar 组件

该组件专门处理头像，比 Cval 组件更实用。**使用backgroundImage** 属性引入图片。

## Flutter  Wrap 流布局

可以实现 **Row与 Column** 达不到的效果，自动换行。

| 属性              | 说明                                                         |
| :---------------- | :----------------------------------------------------------- |
| direction         | 主轴的方向，默认水平                                         |
| alignment         | 主轴的对齐方式                                               |
| spacing           | 主轴方向上的间距                                             |
| textDiretion      | 文本方向                                                     |
| verticalDirection | 定义了children摆放顺序，默认是down，详情见Flex相关属性介绍   |
| runAlignment      | run的对齐方式，可以理解为新的行或者列，如果是水平方向布局的话，run可以为新的一行 |
| runSpacing        | run的间距                                                    |

Wrap 流布局如下图

![1582115349634](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1582115349634.png)

```dart
return Wrap(
	spacing: 10, 
    runSpacing: 10, 
    alignment: WrapAlignment.spaceEvenly,     children: <Widget>[
		MyButton("第 1 集"), 
        MyButton("第 2 集"),
        MyButton("第 3 集"), 
        MyButton("第 4 集"), 
        MyButton("第 5 集"), 
        MyButton("第 6 集第 6 集"), 			MyButton("第 7 集"),
        MyButton("第 8 集第 6 集"), 			MyButton("第 9 集"), 
        MyButton("第 10 集"),
        MyButton("第 11 集"), ], );

class MyButton extends StatelessWidget{
	final String text;
	const MyButton(this.text,{Key key}) : 		super(key: key);
@override
Widget build(BuildContext context) {
// TODO: implement build
return RaisedButton(
    child: Text(this.text), 
    textColor: Theme.of(context).accentColor, 
    onPressed: (){},
);
```

## Flutter StatefulWidget 有状态组件，页面上绑定数据、改变页面数据

#### 1、Flutter  中自定义有状态组件

**vscode安装fullWidget这个插件**

在 Flutter 中自定义组件其实就是一个类，这个类需要继承 StatelessWidget/StatefulWidget。
StatelessWidget 是无状态组件，状态不可变的 widget
StatefulWidget 是有状态组件，持有的状态可能在 widget 生命周期改变。通俗的讲：如果我们想改变页面中的数据的话这个时候就需要用到 StatefulWidget

### Demo

```dart
class HomeContent extends StatefulWidget {
  HomeContent({Key key}) : super(key: key);

  @override
    // 这是个抽象类，调用其接口
  _HomeContentState createState() => _HomeContentState();
}
// 继承 state 类，为保存其状态
class _HomeContentState extends State<HomeContent> {
  int count = 0;
  @override
  Widget build(BuildContext context) {
    return Container(
       child: Column(
         children: <Widget>[
           SizedBox(height: 100,),
           Chip(label: Text("${this.count}")),
           SizedBox(height: 50,),
           RaisedButton(
             child: Text("计数器"),
             onPressed: () {
                print(this.count);
                 // 只有stateFulWidget才存在这个方法，这个存储状态，相当于vue里的数据双向绑定思想
               setState(() {
                 this.count ++;
               });
             },
           ),
         ],
       ),
    );
  }
}
```

## Flutter  BottomNavigationBar  自定义底部导航条、以及实现页面切换

### Flutter BottomNavigationBar  组件

BottomNavigationBar 是底部导航条，可以让我们定义底部 Tab 切换，bottomNavigationBar  是 Scaffold 组件的参数。

![1582174100157](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1582174100157.png)

##### BottomNavigationBar  常见的属性

| 属性名       | 说明                                            |
| ------------ | ----------------------------------------------- |
| items        | List<BottomNavigationBarItem>底部导航条按钮集合 |
| iconSize     | icon                                            |
| currentIndex | 默认选中第几个                                  |
| onTap        | 选中回调函数                                    |
| fixedColor   | 选中的颜色                                      |
| type         | BottomNavigationBarType.fixed  多个按钮并排     |

```dart
 bottomNavigationBar: BottomNavigationBar(
        currentIndex: this._currentIndex,
        onTap: (int index) {
          setState(() {
            this._currentIndex = index;
          });
        },
        items: [
        BottomNavigationBarItem(
            icon: Icon(Icons.home),
            title: Text("home")
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.category),
            title: Text("分类")
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.settings),
            title: Text("设置")
          ),
      ],),
```

## Flutter 中的普通路由、普通路由传值、命名路由、命名路由传值

Flutter 中的路由通俗的讲就是页面跳转。在 Flutter 中通过 Navigator 组件管理路由导航。
并提供了管理堆栈的方法。如：Navigator.push 和 Navigator.pop
Flutter 中给我们提供了两种配置路由跳转的方式：1、基本路由 2、命名路由

### Flutter  普通路由使用

比如我们现在想从 HomePage 组件跳转到 SearchPage 组件。

**步骤一：需要在 HomPage 中引入 SearchPage.dart**

```dart
import '../SearchPage.dart';
```

**步骤二：在HomPage中通过下面方法跳转**

```dart
RaisedButton(
	child: Text("跳转到搜索页面"), 	onPressed: (){
	Navigator.of(context).push(
	MaterialPageRoute(
		builder: (BuildContext 					context){
		return SearchPage();
	  }
	)
   );
},
color:Theme.of(context).accentColor, 
textTheme: ButtonTextTheme.primary
```

### 普通路由跳转传值

比如我们现在想从 HomePage 组件跳转到 SearchPage 组件传值。

**步骤一：需要在HomPage中引入SearchPage.dart**

```dart
import '../SearchPage.dart';
```

**步骤二：在HomPage中通过下面方法跳转**

```dart
RaisedButton(
	child: Text("跳转到搜索页面"), 	onPressed: (){
	   Navigator.of(context).push(
         MaterialPageRoute(
           builder: (BuildContext              context){
    return SearchPage(title:"表单"); //传值
}
)
);
},
```

**自定义返回方法**

```dart
Scaffold(
      appBar: AppBar(title: Text("${this.title}"),),
      floatingActionButton: FlatButton( // 自定义悬浮按钮在最底部
        child: Text("返回"),
        onPressed: () {
          Navigator.of(context).pop(); // 返回方法
        },
      ),
    );
```

### 命名路由及路由跳转传值适用于中大型项目

**步骤一：配置路由**

```dart
// 新建路由文件夹，里面在新建个routes文件，将所需跳转页面导入进去

// 导入页面
import 'package:flutter/material.dart';
import '../pages/Tabs.dart';
import '../pages/Search.dart';
import '../pages/Form.dart';

// 配置路由
final routes = {
  // 无需传参路由
  '/': (context) => TapsPage(),
  // 传参路由，arguments固定写法
  '/form': (context,{arguments}) => FormPage(arguments:arguments),
  '/search': (context,{arguments}) => SearchPage(arguments:arguments),
};

// 固定写法，可以不用记主要用来传参
var onGenerateRoute=(RouteSettings settings) {
  // 统一处理
  final String name = settings.name;
  print(name);
  final Function pageContentBuilder = routes[name];
  if (pageContentBuilder != null) {
    if (settings.arguments != null) {
      final Route route = MaterialPageRoute(
          builder: (context) =>
              pageContentBuilder(context, arguments: settings.arguments));
      return route;
    }else{
        final Route route = MaterialPageRoute(
          builder: (context) =>
              pageContentBuilder(context));
        return route;
    }
  }
};
```

**步骤二：路由跳转方法**

Navigator.pushNamed()

```dart
// 有参数，即可使用arguments参数传参，arguments值类型为Map类型
onPressed: () {          Navigator.pushNamed(context,     '/search',arguments: {"id": "搜索页"});
},
```

**步骤三：跳转的组件接受值方法**

无状态组件   StatelessWidget

```dart
class SearchPage extends StatelessWidget {
  // 声明参数类型，arguments固定写法，Map类型
  Map arguments;
 // 构造函数里写进参数{} 可选参数类型
  SearchPage({this.arguments});
```

有状态组件  StatefulWidget

```dart
class FormPage extends StatefulWidget {
// 在这里定义final arguments 参数同样固定写法  否则警告
  final arguments;
 // 构造函数里传入参数
  FormPage({Key key,this.arguments}) : super(key: key);

  @override
  _FormPageState createState() => 
// 传入参数arguments 
_FormPageState(arguments:this.arguments);
}

class _FormPageState extends State<FormPage> {
// 这个组件里也要定义 argumenst map类型参数，用于接受传参
  Map arguments;
// 构造函数里传入参数，可选参数    
  _FormPageState({this.arguments});
```

## Flutter 中的路由   路由替换  返回到根路由

### Flutter  中返回到上一级页面

```dart
Navigator.of(context).pop()
```

### Flutter 中替换路由

比如我们从用户中心页面跳转到registerFirst 页面，然后从 registerFirst 页面通过
pushReplacementNamed 跳转到了 registerSecond 页面。这个时候当我们点击 registerSecond
的返回按钮的时候它会直接返回到用户中心。

```dart
Navigator.of(context).pushReplacementNamed('/registerSecond');
```

### Flutter 返回到根路由

比如我们从用户中心跳转到 registerFirst 页面，然后从 registerFirst 页面跳转到 registerSecond
页面，然后从 registerSecond 跳转到了 registerThird 页面。这个时候我们想的是 registerThird
注册成功后返回到用户中心。 这个时候就用到了返回到根路由的方法。

```dart
Navigator.of(context).pushAndRemoveUntil(
new MaterialPageRoute(
  // taps是个list多页面，根据索引直接返回一个页面  
    builder: (context) => new Tabs(index:1)),
    (route) => route == null
);
```

## Flutter AppBar 自定义顶部导航按钮、图标、颜色 以及TapBar 定义顶部Tap 切换

### Flutter AppBar 自定义顶部按钮图标、颜色

| 属性            | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| leading         | 在标题前面显示的一个控件，在首页通常显示应用<br/>的 logo；在其他界面通常显示为返回按钮 |
| title           | 标题，通常显示为当前界面的标题文字，可以放组<br/>件          |
| actions         | 通常使用 IconButton 来表示，可以放按钮组                     |
| bottom          | 通常放 tabBar，标题下面显示一个 Tab 导航栏                   |
| backgroundColor | 导航背景颜色                                                 |
| iconTheme       | 图标样式                                                     |
| textTheme       | 文字样式                                                     |
| centerTitle     | 标题是否居中显示                                             |

```dart
class AppBardemoPage extends StatelessWidget {
@override
Widget build(BuildContext context) {
return Scaffold(
   appBar: AppBar(
       backgroundColor:Colors.red,
       leading:IconButton(
       icon: Icon(Icons.menu),            tooltip: "Search",           onPressed: (){
print('menu Pressed');
}
),
       title: Text('FlutterDemo'),        actions: <Widget>[
          IconButton(
          icon:     Icon(Icons.search), tooltip: "Search", onPressed: (){
print('Search Pressed');
}
) , IconButton(
icon: Icon(Icons.more_horiz), tooltip: "more_horiz", onPressed: (){
print('more_horiz Pressed');
}
)
], ),body: Text('这
```



### Flutter AppBar 中自定义 TabBar 实现顶部 Tab 切换

**TabBar 常见属性**

| 属性                 | 描述                                                         |
| -------------------- | ------------------------------------------------------------ |
| tabs                 | 显示的标签内容，一般使用 Tab 对象,也可以是其他<br/>的 Widget |
| controller           | TabController 对象                                           |
| isScrollable         | 是否可滚动                                                   |
| indicatorColor       | 指示器颜色                                                   |
| indicatorWeight      | 指示器高度                                                   |
| indicatorPadding     | 底部指示器的 Padding                                         |
| indicator            | 指示器 decoration，例如边框等                                |
| indicatorSize        | 指示器大小计算方式，TabBarIndicatorSize.label 跟文<br/>字等宽,TabBarIndicatorSize.tab 跟每个 tab 等宽 |
| labelColor           | 选中 label 颜色                                              |
| labelStyle           | 选中 label 的 Style                                          |
| labelPadding         | 每个 label 的 padding 值                                     |
| unselectedLabelColor | 未选中 label 颜色                                            |
| unselectedLabelStyle | 未选中 label 的 Style                                        |

**Scaffold,可以嵌套！！！！**

如果嵌套Scaffold会引发个问题，那就是出现两个导航条，因为我们这个组件是挂载到上一个组件上的，而上个组件是有个AppBar的，这里再写一个AppBar则会多出一个。

解决方法：要想解决这个问题，我们可以在当前这个组件AppBar **title**  放个组件，不用text文本组件，从而替换这个导航条。具体实现如下：

```dart
DefaultTabController(
      length: 4, 
      child: Scaffold(
        appBar: AppBar(
          title: Row(
            children: <Widget>[
              Expanded(
                child: TabBar(
                  labelColor: Colors.red, // 选中颜色
                  unselectedLabelColor: Colors.white, // 未选中颜色
                  tabs: <Widget>[
                    Tab(text:"关注"),
                    Tab(text:"推荐"),
                    Tab(text:"热门"),
                    Tab(text:"视频"),
                  ],
                )
              ),
            ],
          ),
        ),
      )
    );
```

### 自定义TabBar ，利用TabController组件实现顶部导航

该方法，必须在StateFulWidget 中使用，且该方法可以处理一些业务逻辑，监听一些事件。

```dart
class AppBardemoPage extends StatefulWidget {
   AppBardemoPage({Key key}) : super(key: key); _AppBardemoPageState createState() => _AppBardemoPageState();
}
// 需要多继承一个组件SingleTickProviderStateMixin
class _AppBardemoPageState extends State<AppBardemoPage> with SingleTickerProviderStateMixin {

 // 定义一下TabController
  TabController _tabController;
@override
    
// 组件销毁时生命周期函数
  void dispose() {         _tabController.dispose();
     super.dispose();
}
  // 组件初始化生命周期函数  
  void initState() {
super.initState();
 // 实列化下，两个参数，前者固定写法，后者length参数为 tab 项数目     
  _tabController = new TabController(vsync: this, length: 3);
}
    
@override
Widget build(BuildContext context) {
// 这里面都需实例化
return new Scaffold(
appBar: new AppBar(
title: new Text('顶部 tab 切换'), bottom: new TabBar(
 // 必须要加的
   controller:this._controller,
    
 tabs: <Widget>[
    new Tab(
icon: new Icon(Icons.directions_bike), ),
    new Tab(icon:
 new Icon(Icons.directions_boat), ),
    new Tab(icon: new Icon(Icons.directions_bus), ),

body: new TabBarView(
    
   // 这里必须要加的 
   controller: _tabController,     children: <Widget>[
     new Center(child: new Text('自行车')), 
     new Center(child: new Text('船')), 
     new Center(child: new Text('巴士')), ], ),
```

## Flutter  Drawer 侧边栏、以及侧边栏内容布局

### Flutter Drawer 侧边栏

在 Scaffold 组件里面传入 drawer 参数可以定义左侧边栏，传入 endDrawer 可以定义右侧边栏。侧边栏默认是隐藏的，我们可以通过手指滑动显示侧边栏，也可以通过点击按钮显示侧边栏。

```dart
Scaffold(
	appBar: AppBar(
	title: Text("Flutter App"), ),
    
    //在Scaffold里定义 与appBar 并排参数
    drawer: Drawer( // 左侧栏
	child: Text('左侧边栏'), ),
    // 右侧栏
    endDrawer: Drawer(
		child: Text('右侧侧边栏'), ), );
```

### Flutter DrawerHeader组件

**常见属性**：

| 属性       | 描述             |
| ---------- | ---------------- |
| decoration | 设置顶部背景颜色 |
| child      | 配置子元素       |
| padding    | 内边距           |
| margin     | 外边距           |

定义抽屉组件头部

### Flutter  UserAccountsDrawerHeader

| 属性                  | 描述                         |
| --------------------- | ---------------------------- |
| decoration            | 设置顶部背景颜色             |
| accountNaem           | 账户名称                     |
| accountEmail          | 账户邮箱                     |
| currentAccountPicture | 用户头像                     |
| otherAccountPictures  | 用来设置当前账户其它账户头像 |
| margin                |                              |

```dart
UserAccountsDrawerHeader(
              accountName: Text("清欢"),
              accountEmail: Text("17355437218@163.com"),
              currentAccountPicture: CircleAvatar(
                backgroundImage: NetworkImage("https://www.itying.com/images/flutter/3.png"),
              ),
              decoration: BoxDecoration(
                color: Colors.orange,
                image: DecorationImage(image: NetworkImage("https://www.itying.com/images/flutter/2.png"),fit: BoxFit.cover),
              ),
            ),
```

实线分割层级关系 **Divider()** 组件

```dart
ListTile(
              title: Text("用户中心"),
              leading: CircleAvatar(
                child: Icon(Icons.people),
              ),
              onTap: () {
                Navigator.of(context).pop();
                Navigator.pushNamed(context, '/userCenter');
              },
            ),
            Divider(),
```

抽屉页面，跳转到用户界面后返回时直接返回主页面

```dart
onTap: () {
 // 关键就是这句              
 Navigator.of(context).pop();
                Navigator.pushNamed(context, '/userCenter');
              },
```

## Flutter  常见的按钮组件  以及自定义按钮组件

### Flutter  中的按钮组件介绍

Flutter 里有很多的 Button 组件很多，常见的按钮组件有：RaisedButton、FlatButton、IconButton、OutlineButton、ButtonBar、FloatingActionButton 等。

**RaisedButton** ：凸起的按钮，其实就是 Material Design 风格的 Button
**FlatButton** ：扁平化的按钮
**OutlineButton**：线框按钮
**IconButton** ：图标按钮
**ButtonBar**:按钮组
**FloatingActionButton**:浮动按钮

### Flutter 按钮组件中的一些属性

| 属性名称          | 值类型                        | 属性值                                                       |
| ----------------- | ----------------------------- | ------------------------------------------------------------ |
| onPressed         | VoidCallback,一般接受一个方法 | 必填参数，按下按钮时触发的回调，接收一个<br/>方法，传 null 表示按钮禁用，会显示禁用相关<br/>样式 |
| child             | Widget                        | 文本控件                                                     |
| textColor         | Color                         | 文本颜色                                                     |
| color             | Color                         | 按钮的颜色                                                   |
| disabledColor     | Color                         | 按钮禁用时的颜色                                             |
| disabledTextColor | Color                         | 按钮禁用时的文本颜色                                         |
| splashColor       | Color                         | 点击按钮时水波纹的颜色                                       |
| highlightColor    | Color                         | 点击（长按）按钮后按钮的颜色                                 |
| elevation         | double                        | 阴影的范围，值越大阴影范围越大                               |
| padding           |                               | 内边距                                                       |
| shape             |                               | 设置按钮的形状<br/>shape: RoundedRectangleBorder(<br/>borderRadius:<br/>BorderRadius.circular(10),<br/>)<br/>shape: CircleBorder(<br/>side: BorderSide(<br/>color: Colors.white,<br/>)<br/>) |

### 自定义按钮组件

更多的参数可以自己定义

```dart
import 'package:flutter/material.dart';

class MyButton extends StatelessWidget {
  final double height;
  final double width;
  final text;
  final onpressed;

  const MyButton({this.height,this.width,this.onpressed,this.text});

  @override
  Widget build(BuildContext context) {
    return Container(
      height: this.height,
      width: this.width,
      child: RaisedButton(
        child: Text("${this.text}"),
        onPressed: this.onpressed,
      ),
    );
  }
}
```

## Flutter FloatingActionButton实现类似咸鱼App 底部导航凸起按钮

### Flutter FloatingActionButton 介绍

**FloatingActionButton 简称 FAB** ,可以实现浮动按钮，也可以实现类似闲鱼 app 的地步凸起导航

| 属性名称           | 属性值                              |
| ------------------ | ----------------------------------- |
| child              | 子视图，一般为 Icon，不推荐使用文字 |
| tooltip            | FAB 被长按时显示，也是无障碍功能    |
| backgroundColor    | 背景颜色                            |
| elevation          | 未点击的时候的阴影                  |
| hignlightElevation | 点击时阴影值，默认 12.0             |
| onPressed          | 点击事件回调                        |
| shape              | 可以定义 FAB 的形状等               |
| mini               | 是否是 mini 类型默认 false          |

### FloatingActionButton 实现咸鱼app底部凸起按钮

不管是**drawer**组件 还是 **floatingButton** 组件以及 **floatingActionButtonLocation**组件 位置属性都是在Scaffold组件内部对齐方式的。不存在谁包含谁。

```dart
// 悬浮图标
      floatingActionButton: Container(
        margin: EdgeInsets.only(top:10),
        child: FloatingActionButton(
          child: Icon(Icons.add),
          onPressed: null,
        ),
      ),
      // 必须写在与floatButton对齐，centerDocked表示位于最底部中心
      floatingActionButtonLocation: FloatingActionButtonLocation.centerDocked,
```

![1582281434674](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1582281434674.png)

## Flutter 常用表单介绍

Flutter 中常见的表单有 TextField 单行文本框，TextField 多行文本框、CheckBox、Radio、Switch、CheckboxListTile、RadioListTile、SwitchListTile、Slide.

### TextField 文本框组件

**TextField 表单常见属性：**

| 属性        | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| maxLines    | 设置参数可以把文本框改为多行文本框                           |
| onChange    | 文本框改变的时候触发的事件                                   |
| decoration  | **hintText** 类似 html 中的 placeholder<br/>**border** 配置文本框边框 **OutlineInputBorder** 配合使用<br/>**labelText** lable 的名称<br/>**labelStyle** 配置 lable 的样式 |
| obscureText | 把文本框改为密码框                                           |
| controller  | controller 结合 TextEditingController()可以配置表单默认显示的内容 |

```dart
TextField(
                  decoration: InputDecoration(hintText: "用户名"),
                  onChanged: (value) {
                    setState(() {
                      this._inputName = value;
                    });
                  }),
              TextField(
                obscureText: true,
                decoration: InputDecoration(hintText: "密码"),
                onChanged: (v) {
                  this._inputPwd = v;
                },
              ),
```

### Checkbox、CheckboxListTile 多选框组件

**Checkbox 常见属性：**

| 属性        | 描述                                |
| ----------- | ----------------------------------- |
| value       | true或者false                       |
| onChanged   | 改变的时候触发的事件                |
| activeColor | 选中的颜色、背景颜色                |
| checkColor  | 选中的颜色、Checkbox 里面对号的颜色 |
| selected    | 选中状态                            |

**CheckboxListTile 常见属性：**

| 属性        | 描述                           |
| ----------- | ------------------------------ |
| value       | true 或者 false                |
| onChanged   | 改变的时候触发的事件           |
| activeColor | 选中的颜色、背景颜色           |
| title       | 标题                           |
| subtitle    | 二级标题                       |
| secondary   | 配置图标或者图片               |
| selected    | 选中的时候文字颜色是否跟着改变 |

**单选checkbox** demo:

```dart
Checkbox(
	value: _isSelected,
		onChanged: (v) {
			// print(v);
			setState(() {
			this._isSelected=v;
		});
	},
	activeColor: Colors.red,
	checkColor:Colors.blue
)
```

**结合了列表标题的可以勾选项**

```dart
CheckboxListTile(
   value: _isSelected,
   title: Text("nodejs 视频教程"),
   subtitle: Text("egg.js 视频教程"),
   onChanged: (v){
       setState(() {
          this._isSelected=v;
    });
   },
   activeColor: Colors.red,
   secondary:
Image.network("https://www.itying.com/images/flutter/1.png"),
   selected:_isSelected
)
```

### Radio、RadioListTile 单选按钮组件

**Radio 常用属性：**

| 属性        | 描述                 |
| ----------- | -------------------- |
| value       | 单选的值             |
| onchanged   | 改变时触发           |
| activeColor | 选中的颜色、背景颜色 |
| groupValue  | 选择组的值           |

**RadioListTile 常用属性：**

| 属性        | 描述                 |
| ----------- | -------------------- |
| value       | true或false          |
| onChanged   | 改变的时候触发的事件 |
| activeColor | 选中的颜色、背景颜色 |
| title       | 标题                 |
| subtitle    | 二级标题             |
| secondary   | 配置图标或图片       |
| groupValue  | 选择组的值           |

```dart
// 必须定义一个groupValue 变量
int _groupValue=1;
   Radio(
      value: 0,
      onChanged: (v) {
         setState(() {
          this._groupValue=v;
       });
    },
     activeColor: Colors.red,
     // 定义的变量为同一个变量
     groupValue:_groupValue ,
),
  Radio(
     value: 1,
     onChanged: (v) {
        setState(() {
            this._groupValue=v;
		});
	},
      activeColor: Colors.red,
      // 定义的变量为同一个变量
      groupValue:_groupValue ,
```

## Flutter 日期和时间戳、格式、第三方时间库

### Flutter 日期和时间戳

**日期转化成时间戳**

```dart
var now = new DateTime.now();
// 转化成时间戳
print(now.millisecondSinceEpoch)

```

**时间戳转化成日期**

```dart
var now = new DateTime.now();
var a = now.millisecondSinceEpoch // 时间戳
print(DateTime.fromMillsecondSinceEpoch(a)) // 转化日期格式
```

## 调用第三方库

**链接地址**：pub.dev

**安装方法**：

...

最后，Ctrl+S 保存下就安装第三方库了

在所需库的组件内引入

```dart
import 'package:date_format/date_format.dart';
```

### 详情demo

**异步获取日期值方法一**

```dart
var time = new DateTime.now();  
  _showDatePicker () {
    // 引入时间日期组件,异步类型future
    showDatePicker(
      context: context,
      initialDate: time,
      firstDate: DateTime(1996),
      lastDate: DateTime(2100)
    ).then((value) => print(value)); // 异步获取值方法
  }
```

**异步获取日期值方法二**

async 配合着 await  使用

```dart
_showDatePicker () async{
    var result = await showDatePicker(
      context: context,
      initialDate: time,
      firstDate: DateTime(1996),
      lastDate: DateTime(2100)
    );
    print(result);
  }
```

**InkWell水纹按钮组件监听事件**

```dart
InkWell( // 水纹按钮可以监听事件
   child: Row(
       mainAxisAlignment: MainAxisAlignment.center,
         children: <Widget>[
           Text("2018-12-20"),
        con(Icons.arrow_drop_down)
              ],
            ),                onTap:this._showDatePicker,
 ),
```

## Flutter_swipper 轮播图组件

同样也是第三方库的引用方法

链接地址：pub.dev 可以看下文档使用

使用方法和日期组件都差不多，异步

## Flutter  Diaglog 组件

```dart
onTap: () {
    // 取消，或者退出对话框功能
    navigitor.pop(context,"参数")
}
```

##   Flutter 网络请求

### Flutter Json 字符串和Map 类型的转化

 ```dart
import 'dart:convert' // 导入这个库

var mapData = {"naem":"颤三","age": "20"};
var strData = '{"naem":"颤三","age": "20"}';
print(json.encode(mapData)) // Map 类型转化Json 字符串
print(json.decode(strData)) // Json字符串 转化成 Map 类型
 ```

### Http 网络请求第三方库

直接在pub.dev  直接搜 Http 库，都是异步操作的，所以需要配合使用 `async` 与  `await`

 **Get用于获取服务端数据**

**Post用于提交数据**



