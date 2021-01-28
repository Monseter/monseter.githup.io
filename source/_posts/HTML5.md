---
title: HTML5
date: 2021-01-27 17:57:26
tags: HTML5
updated: true
categories: 前端
---
## HTML5 建立的一些规则

- 新特性应该基于 HTML、CSS、DOM、以及JavaScript
- 减少对外部插件的需求（比如 Flash）
- 更优秀的错误处理
- 更多取代脚本的标记
- HTML5 应该独立于设备
- 开发进程对公众透明

## 语义化标签

以下常用标签

- header : 头部标签
- nav: 导航标签
- article: 内容标签
- section: 定义文档某个区域
- aside: 侧边栏标签
- footer:  底部标签

**注意**：

- 这种语义化标签是针对**搜索引擎**的
- 这些标签可以在页面中使用**多次**
- 在IE9中，需要把这些元素转化为**块级元素**

## video标签

描述：大多数视频是通过插件（比如Flash）来显示的，然而并非所有的浏览器都拥有同样的插件。HTML5 规定来一种通过 video 元素来包含视频的标准方法。

- 视频编码格式

|  格式  |  IE  | Firefox | Opera | Chrome | Safari |
| :----: | :--: | :-----: | :---: | :----: | :----: |
|  Ogg   |  No  |  3.5+   | 10.5+ |  5.0+  |   No   |
| MPEG 4 | 9.0+ |   No    |  No   |  5.0+  |  3.0+  |
|  WebM  |  No  |  4.0+   | 10.6+ |  6.0+  |   No   |

Ogg = 带有 Theora 视频编码和 Vorbis 音频编码的 Ogg 文件

MPEG4 = 带有 H.264 视频编码和 AAC 音频编码的 MPEG4 文件

WebM = 带有 VP8 视频编码和 Vorbis 音频编码的 WebM 文件

- **如何使用**

    ```html
    <video src="movie.ogg" width="320" height="240" controls="controls">
        Your browser does not support the video tag.
    </video>
    ```

video 元素允许多个 source 元素。source 元素可以链接不同的视频文件。浏览器将使用第一个可识别的格式：

    ```html
    <video width="320" height="240" controls="controls">
    <source src="movie.ogg" type="video/ogg">
    <source src="movie.mp4" type="video/mp4">
    Your browser does not support the video tag.
    </video>
    ```

- **video 标签属性**

|   属性   |    值    |                             描述                             |
| :------: | :------: | :----------------------------------------------------------: |
| autoplay | autoplay |              添加此属性，则视频在就绪后马上播放              |
| controls | controls |          添加此属性，则向用户显示控件，比如播放按钮          |
|  height  |  pixels  |                     设置视频播放器的高度                     |
|   loop   |   loop   |                    添加此属性，会重复播放                    |
| preload  | preload  | 添加此属性，则视频在页面加载时进行加载，并预备播放，如果使用“autuoplay”,则忽略该属性 |
|   src    |   url    |                       要播放视频的url                        |
|  width   |  pixels  |                     设置视频播放器的宽度                     |

## canvas

- 基本使用

    描述：html 中使用的标签一般加一个id,因为需要在js中操控这个标签来渲染不同的内容。

    ```html
        <canvas id="canvas" style="border:1px sold #aaa;display:block;margin：50px auto">
        浏览器不支持canvas,请更换浏览器</canvas>
    ```

```javascript
var canvas = document.getelementById('canvas')
// 可以在这里操做canvas大小,没有px单位的
canvas.width = 1024;
canvas.height = 768;

// 获取画布一个上下文环境
var context = canvas.getContent('2d') //这个2d 是w3c已经做好的图像处理对象，以后也可能用到3d

//判断浏览器是否兼容canvas，做个处理
if(canvas.getContext('2d')) {
    // 使用context绘制
} else {
    alert('当前浏览器不支持Canvas，请更换浏览器后再试')
}
说明：该操做判断是否能获取到上文环境，有则继续，无则提示
```

**总结**：需要了解canvas 三个方法:
- 基础使用

​	canvas.width  // 画布宽

​	canvas.height  // 画布高

​	canvas.getContext('2d')  // 获取上下文环境

- 绘制直线、多边形等

```javascript
context.moveTo(100,100) // 可以想象画笔起点位置
context.lineTo(700,700) // 画笔终点位置
// 前两行代码，可以这样理解为人的想象，但是还未执行
context.stroke() //想好了是吧，那就开始下笔画了

描述：canvas是基于我们所设的状态，然后调用函数去执行的，前两行代码为状态，后一行为调用函数执行操作绘制。这里坐标点（x,y）是基于画布左上角开始往右为正x轴，往下正y轴。

context对象再列举几个属性：
context.lineWidth = 5 //画笔线宽
context.strokeStyle = "#005588" //线条样式，主要是颜色可以用css样式设置

例子：画一个多边形
context.moveTo(100,100)
context.lineTo(700,700)
context.lineTo(100,700)
context.lineTo(100,100)
//只要把各顶点坐标考虑好就行了不停地lineTo绘制就好

//下面填充颜色，两行代码
context.fillStyle = "rgb(2,100,30)" //同样是字符串形式css
context.fill() //调用填充函数执行

// 同样可以配合使用stroke画线，画个多边形的边框
context.lineWidth = 5;
context.strokeStyle = "red";
context.stroke() // 调用画笔函数执行

例子2：陷阱区易犯错区
情景：当我们在该画布中画一个多变形，再画一个别的直线或者多边形的时候。
context.moveTo(100,100)
context.lineTo(700,700)
context.lineTo(100,700)
context.lineTo(100,100)
context.lineWidth = 5;
context.strokeStyle = "red";
context.stroke() // 调用画笔函数执行

// 另起再画的时候，从新调用下moveTo()函数就好了
context.moveTo(200,200)
context.lineTo(700,700)
context.strokeStyle = "black"
context.stroke()

说明：由于canvas绘图是基于状态来绘制的，虽然写了两次stroke()但是，起作用的是最后一次stroke函数，它会将上面的状态绘制下来。可以说污染影响吧，在这里只有将它们隔离开样式才会保留住。
//解决方法
引入context方法：beginPath()  closePath()应用在状态开始前与状态结束后
context.beginPath() // 起始
context.moveTo(100,100)
context.lineTo(700,700)
context.lineTo(100,700)
context.lineTo(100,100)
context.closePath() // 状态结束
context.lineWidth = 5;
context.strokeStyle = "red";
context.stroke() // 调用画笔函数执行

// 另起再画的时候，从新调用下moveTo()函数就好了
context.beginPath() // 状态起始
context.moveTo(200,200)
context.lineTo(700,700)
context.closePath() // 状态结束
context.strokeStyle = "black"
context.stroke()
```

**注**：需要记住context一些绘图方法

​		context.moveTo(x,y) // 起点

​		context.lineTo(x,y)  //终点位置

​		context.lineWidth = number  //线宽

​		context.strokeStyle = "css样式" //线样式主要颜色

​		context.fillStyle = "css样式"  //填充背景色样式

画直线与填充颜色执行方法

​	context.fill()  // 执行填充背景色

​	context.stroke() //执行绘制直线

如果需要画多个需要隔离开的情况下

​		context.beginPath()  //放在moveTo前

​		context.closePath()  // 放在状态结束末尾lineTo

- 绘制圆与圆弧
```javascript
context.arc(
    //三个参数为圆心坐标x,y以及半径radius
	centerx,centery,radius,
    //两个参数为起始弧度，与结束弧度
    startingAngle,endingAngle,
    //这个可选参数为是否为顺时针绘制，默认false为顺时针
    anticlockwise = false
)
```
描述：startingAngel与endingAngle:上图可知是从最右边0pi 开始，然后顺时针 0.5pi --> 1pi -->1.5pi --> 2pi 回到原点。需要注意的是，不管是顺时针还是逆时针这四个位置（0pi,0.5pi,1pi,1.5pi）永远不会变的

例1

使用closePath()

未使用closePath() 所以就不会闭合

```javascript
for(let i = 0; i < 10; i++) {
      context.beginPath()
      context.arc(
        50+i*40,100,30,0,2*Math.PI*(i+1)/10
      )
      context.closePath()
      context.stroke()
    }
补充下：closePath()会闭合图形，只要加这函数将会自动闭合，当然也可以分开用，不一定非要beginPath 与 closePath 一起运用
```

- **时钟demo**

    （1）点阵算法

        描述：一个数字是由7*10组成的点阵，所以一个数字的宽度就是7*（2R+1）

    （2）Animation 动画效果

        canvas使用的是计时器来进行动画效果，基本架构，三个参数，一个匿名函数

    ```javascript
        setInterval(
            fuction() {
    	        render() // 一般作为绘制
		        update() // 数据调整
        }，
    50 //间隔50毫秒，每一帧所要做的事
    )

    描述：这时绘制图形时候，需要引入新的函数，clearRect()清楚下画布内容，避免叠加
    ```

    （3）屏幕自适应

    body样式需要撑起来以及canvas标签 需要设置高度100%
    ```html
        <body style="height: 100%;">
        <canvas id="canvas" style="height: 100%;">
            当前浏览器支持canvas，请更换浏览器后再试</canvas>
        </body>
    ```

## a 标签详解

| HTML5属性 |      值       |                  描述                   |
| :-------: | :-----------: | :-------------------------------------: |
| download  |   filename    |        规定被下载的超链接目标。         |
|   media   | *media_query* | 规定被链接文档是为何种媒介/设备优化的。 |
|   type    |  *MIME type*  |     规定被链接文档的的 MIME 类型。      |

- **图片下载**

    描述：通过创建 `a` 标签，绑定`download`属性，最后事件处理达到下载效果。

    ```javascript
    // 下载
    handleDownload(item) {
        let downloadLink = document.createElement('a');
        downloadLink.href = item;
        downloadLink.download = item;
        downloadLink.click();
        if (document.body.contains(downloadLink)) {  // BUG 存在即删除该节点
            document.body.removeChild(downloadLink);
        }
        this.$nextTick(() => {
            this.$Message.success('下载成功！')
        })
    }
    ```


