---
title: uni-app总结
typora-root-url: ./
date: 2021-01-28 16:05:24
tags: uni-app
categories: 移动端框架
---
#### 1、如果使用HBuilderX创建的项目，根目录会没有package.json文件

使用如下命令：

```
npm init -y    
```

#### 2、uni-app tabs搭配swiper 使用

注意：1、uni-app  **swiper** 组件高度默认**150**，而我们要的效果是自适应

代码如下

```vue
<swiper
    :style="{height: swiperItemHeight + 'px'}"  // 动态绑定样式
    :current="swiperCurrent"
    :disable-touch="false"
    @change="handleSwiperChange"
    @transition="transition"
    @animationfinish="animationfinish"
>
    <swiper-item>
        <TaskPage ref="page" class="component"></TaskPage>
    </swiper-item>
    <swiper-item>
        <view class="component">
            已延期
        </view>
    </swiper-item>
    <swiper-item>
        <view class="component">
            已完成
        </view>
    </swiper-item>
</swiper>
```

```javascript
setSwiperHeight() {
    let query = uni.createSelectorQuery().in(this); // uni-app 获取dom节点api
    query.selectAll(".component").boundingClientRect();
    query.exec((res) => {
        this.swiperItemHeight = res[0][this.current].height;
        console.log(res[0][this.current].height,'swiperHeight+++++++=');
    })
},
// methods
```

最后，需要在mounted时候调用这个方法就好了，这样可以使得swiper 切换的时候都可以获取各自高度，给到swiper动态绑定。

#### 3、uni-app 页面跳转，嵌套层级不能太深否则无法跳转页面

也就是官方文档所说的，“页面跳转路径有层级限制，不能无限跳转新页面”

意思就是，这个index 页面可以跳转，里面如果在嵌套文件，想要跳转的话，是不可以跳转的。切记！

如何突破层级限制跳转，那么只有this.$emit('navigatioTo'),不断回调，让顶层初始界面去回调跳转！

#### 4、uView 上传图片后台返回url后放入image中图片不显示问题

对于上传到后端返回的图片路径，如果带有转义符号,uniapp不支持，例如返回的数据为 `upload\\img\\QNXLpic\\2015001789a494592eb4433891bd45c9035be58\\test.png`

**解决方法** 

后台返回的数据里面有反斜杠，解决方法就是把`\`替换为`/`

```javascript
使用String的replace方法back.url.replace(/[\\]/g,'/')
```

#### 5、CSS文本换行 通常文本换行我们会使用word-break属性，有两个值供我们选择

```
word-break: break-all;
word-break: break-word; // 最好用这个，可以不受符合影响
```

如何区分两个值 

[博客]: https://www.cnblogs.com/wuxianqiang/p/10556367.html	"文本换行"

#### 6、时间无法转换不兼容问题

使用 new Date(str) 在浏览器上显示是没有问题的，然后在安卓及ios系统上显示是NAN。

原因是安卓及  IOS系统只识别 **" / "** 不识别 **" - "**。

后台返回来的时间类型一般有三种 时间、时间戳、字符串。我们就研究一下这三种类型怎么 处理才能兼容安卓系统和IOS系统吧。

- **字符串类型 2019-8-14 10:03:45** 

  ```
  var date = "2019-8-14 10:03:45"
  
  //不兼容代码
  var newDate = new Date(date);
  
  //解决
  var newDate = new Date(date.replace(/-/g, '/'));
  ```

- **时间戳类型 1565776169000 （毫秒） 最后显示 2019/08/14 17:49:29**

  ```
  var date = 1565776169000; //必须是数值类型，必须是13位数
  var newDate = new Date(date);
  var newDateTime = dateFormater.formatTime(newDate) // 2019/08/14 17:49:29
  ```

- **时间类型 2019-08-09T18:23:27.000+0800**

  ```
  var date = '2019-08-09T18:23:27.000+0800';
  var data = date.substr(0, 19); //'2019-08-09T18:23:27'
  var newDate = new Date(data.replace(/T/g, ' ').replace(/-/g, '/'));
  ```

#### 7、页面返回传参问题

使用的是页面栈获取的方法，根据页面栈来对上页面进行赋值操作

注意坑：通常在浏览器上测试H5界面时,如下操作是可以的

```javascript
let pages = getCurrentPages();
let prevPage = pages[pages.length - 2];  // 获取上一页面栈
prevPage.isInput = true;   // 对上一页面栈中的data内 数据赋值
```

但是当我们拿到手机调试的时候页面返回传参就失败了，解决方法如下：

```javascript
prevPage.$vm.isInput = true;  // $vm 方法，准确的获取上一页面栈所有数据及方法 建议使用
prevPage.isInput = true; // 虽然可以，但是手机端无效，不精确
```

#### 8、swiper卡顿问题

当与tabs配合使用的时候，左右切换滚动会出现卡顿问题。通过测试，如果swiper-item内的数据是静止死的数据，那么就不会出现卡顿问题，如果我们在切换swiper-item的时候，动态渲染swiper-item内数据时候，这时就会变得卡顿。

解决办法：当进入页面后，定义tabs数组对象，每个tab对象内有一个content变量，将这个content给定到swiper-item。一开始切换tabs的时候，如果为空进行赋值，达到一个初始化的效果。随后的tabs切换时候，swiper-item都是有固定静态数据了，卡顿效果也就解决了。

#### 9、uView  image 组件使用问题

注意：页面加载的时候会有“闪”下的缺省图，影响美观。使用uniapp官方的image图片组件就好了。