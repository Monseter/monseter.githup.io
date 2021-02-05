---
title: Cesium基础
typora-root-url: ./
date: 2021-02-05 09:55:45
tags: Cesium
categories: 地图
---

## Cesium 是什么？

- 简介

  Cesium是一个地图可视化框架，只是支持三维场景[渲染三维地球]

  Cesium中的图层分为两种：一种是普通图层，包含影像、线划等普通显示图层；还有一种是地形图层，用于真实的模拟地球表面的场景，Cesium会根据加载到的地形瓦片以三维的方式显示出山川、大海等。

- cesium 安装

  ```javascript
  npm install cesium --save 
  ```

- webpack 配置

  为什么要配置webpack ?  

  使用npm i cesium安装后并不能直接打包编译该库

  原因：该库比较复杂，使用AMD(define定义模块，reqiured引入模块)规范，并且其依赖也是使用的AMD，而且代码中包含多行字符串，不能直接编译，需要配置webpack  

  如果项目目录下没有Vue.config.js文件 自己新建一个

  ```javascript
  const webpack = require('webpack');
  const CopyWebpackPlugin = require('copy-webpack-plugin');
  const cesiumSource = 'node_modules/cesium/Source';
  
  module.exports= {
      lintOnSave: false,  // 取消eslint检测
      configureWebpack: {  // webpack 配置
          amd: {
              // Enable webpack-friendly use of require in Cesium
              // Tells Cesium that the version of AMD webpack uses to evaluate require statements is not compliant with the standard toUrl function
              toUrlUndefined: true
          },
          module: {
              // cesium 3 不加这个配置会报 require 引入警告
              unknownContextCritical: false
          },
          // cesium 4
          plugins: [
              new webpack.DefinePlugin({
                  CESIUM_BASE_URL: JSON.stringify('./cesium')  // 对build生效，拷贝到dist目录下。如：dist/Assets
              }),
              new CopyWebpackPlugin([
  				{
  					from: 'node_modules/cesium/Build/Cesium/Workers',
  					to: 'cesium/Workers'
  				}
  			]),
  			new CopyWebpackPlugin([
  				{
  					from: 'node_modules/cesium/Build/Cesium/ThirdParty',
  					to: 'cesium/ThirdParty'
  				}
  			]),
  			new CopyWebpackPlugin([
  				{ from: 'node_modules/cesium/Build/Cesium/Assets', to: 'cesium/Assets' }
  			]),
  			new CopyWebpackPlugin([
  				{
  					from: 'node_modules/cesium/Build/Cesium/Widgets',
  					to: 'cesium/Widgets'
  				}
  			]),
              new webpack.ProvidePlugin(
                  { Cesium: ["cesium/Cesium"] }  // Cesium对象实例可在每个js中使用而无须import
              )
          ],
          // optimization: {
          //     minimizer: process.env.NODE_ENV === 'production' ? true : false
          // }
      }
  }
  ```

## Cesium 实例对象属性方法

### **创建地图窗口**

可以使用`Cesium.Viewer()`以及`Cesium.CesiumWidget()`

```javascript
// Cesium.Viewer() 创建三维容器，该方法包括了clock等，包含很多图元
var viewer = new Cesium.Viewer("cesiumContainer", {
  terrainProvider: Cesium.createWorldTerrain(),
});
// cesiumContainer 对应的是 div 内 id选择器 id = "cesiumContainer"

// Cesium.CesiumWidget("cesiumContainer") 该方法和 Cesium.Viewer 很像，但是比较纯净不报括别的图元，只是仅仅单单的三维窗口
var widget = new Cesium.CesiumWidget("")
```

### **坐标系**

Cesium中常用的坐标系主要有两种：WGS84坐标系和笛卡尔空间直角坐标系。平时我们常见的某个点的经纬度就是在WGS84坐标系下某个点的坐标，它的坐标原点在椭球的中心；而笛卡尔坐标系主要是用来做空间位置的变化如平移、旋转和缩放等等，它的坐标原点在椭球的中心。

除此之外，还有不常用的坐标系：1、平面坐标系（Cartesian2）；2、笛卡尔空间直角坐标系（Cartesian3）；3、Cartesian4（unknown，在应用中几乎用不到）4、Cartographic（地理坐标系下经纬度的弧度表示），通常情况下通过它和WGS84坐标系之间互转。

- **经纬度坐标 Cartesian3---- new Cesium.Cartesian3(x, y, z）**

```
Cesium.Cartesian3.fromDegrees(longitude, latitude, height, ellipsoid, result) 
```

- **弧度坐标**

```
Cesium.Cartesian3.fromRadians(longitude, latitude, height, ellipsoid, result)
```

另外，经纬度坐标和弧度坐标也可以通过**Cesium.Math**来转换

```
Cesium.CesiumMath.toDegrees(radians) → Number

Cesium.CesiumMath.toRadians(degrees) → Number
```

### **坐标转换**

- **经纬度坐标转为笛卡尔空间直角坐标系**

```javascript
var lat=Cesium.Math.toDegrees(cartograhphic.latitude);
var lng=Cesium.Math.toDegrees(cartograhpinc.longitude);
var alt=cartographic.height;
var ellipsoid=viewer.scene.globe.ellipsoid;
var cartographic=Cesium.Cartographic.fromDegrees(lng,lat,alt);
var cartesian3=ellipsoid.cartographicToCartesian(cartographic);
```

- **笛卡尔空间直角坐标系转为经纬度坐标**

```javascript
var x=cartesian3.x;   var y=cartesian3.y;   var z=cartesian3.z;
var ellipsoid=viewer.scene.globe.ellipsoid;
var cartesian3=new Cesium.cartesian3(x,y,z);
var cartographic=ellipsoid.cartesianToCartographic(cartesian3);
```

### 常见坐标系

- **3857** ：google
- **4326 WGS-84**：是国际标准，GPS坐标（Google Earth使用、或者GPS模块
- **2437 GCJ-02**：中国坐标偏移标准，Google Map、高德、腾讯使用又称火星坐标
- **BD-09**：百度坐标偏移标准，Baidu Map使用，该坐标经过加密。

对于openlayers地图，通过监听地图的相关事件获取的经纬度坐标是经过**加密算法偏移**的数值，因此在使用坐标之前需要将其转化成为正确的坐标：转化API如下：

```javascript
var coordinate = ol.proj.transform( lonlat ,'EPSG:3857' ,'EPSG:4326');
```



### **`new Cesium.HeadingPitchRoll.fromDegrees(pitch、yaw、roll)`理解**

一个用 `pitch`、`yaw`、`roll`三个方向的旋转角度来表达的旋转信息。换句话说，就是在三维空间中，通过实体与笛卡儿坐标系三个坐标轴的的夹角来确定实体在三维空间中的方向。`heading`是实体与z轴的旋转角，`pitch`是实体与y轴的旋转角，`roll`是实体与x轴的旋转角。为了更加形象的说明三者关系，下面结合具体的图示来说明。不过需要说明的是由于GIS中坐标系的不同（大家都知道`GIS中的高斯坐标系x轴是纵轴，y轴是横轴`）,可能会与其他领域的坐标系有所差别。

​		三维空间的右手笛卡尔坐标如下所示
<div style="text-align:center">
<img src="20210205102711643.png" />
</div>

​	可以看出，图示的坐标轴名称与Cesium文档写的略有差别，不过不影响我们理解。

- **pich是X轴旋转，也叫俯仰角，类似于飞机向上或向下飞行与本来直线飞行形成的角度值**

    <div style="text-align:center">
    <img src="748209a3727a6bca13f7df472ad28d42.gif" />
    </div>

- **yaw 是围绕Y轴旋转，也叫偏航角，类似于飞机左右调转方向与本来直线飞行形成的角度值**

    <div style="text-align:center">
    <img src="5ec98be49467c096758036589c1ae815.gif" />
    </div>

- **roll是围绕Z轴旋转，也叫翻滚角，类似于飞机直线飞行过程中做一个旋转的动做形成的旋转角度**

    <div style="text-align:center">
    <img src="71c58c7085f911e265d5f187cf47286e.gif" />
    </div>

由此我们可以看到，在Cesium中，HeadingPitchRoll确定了物体的方向。

```javascript
// 设置具体位置 比如中心点
var initialPosition = Cesium.Cartesian3.fromDegrees(
  -74.01881302800248, // 经度
  40.69114333714821,  // 纬度
  753   // 高度
);

// 设置窗口视角即角度方向
var initialOrientation = new Cesium.HeadingPitchRoll.fromDegrees(
  21.27879878293835,
  -21.34390550872461,
  0.0716951918898415
);

// 设置初始化时候一个窗口界面
viewer.scene.camera.setView({
  destination: initialPosition,
  orientation: initialOrientation,
  endTransform: Cesium.Matrix4.IDENTITY,
});
// 注： 使用Cartesian3确定了坐标，HeadingPitchRoll确定方向，这样我们就能可以在精确的镜头视角下看到该实体的空间情况。
```

### 实体 `Entity`类

- Label

  文字标注，可以设置样式，文字内容，字体，偏移等等

  ```javascript
  label:{
      text : 'Citizens Bank Park',
      font : '14pt monospace',
      style: Cesium.LabelStyle.FILL_AND_OUTLINE,
      outlineWidth : 2,
      verticalOrigin : Cesium.VerticalOrigin.BOTTOM,
      pixelOffset : new Cesium.Cartesian2(0, -9)
  }
  ```

- 模型 model

  常见的模型有GLTF和GLB

  ```
  model: {
      uri : '../../SampleData/models/CesiumGround/Cesium_Ground.gltf'
  }
  ```

- 广告牌

  一个最简单的广告牌就是图片，和显示大小

  ```javascript
  billboard: {
      image : 'http://localhost:81/images/2015/02-02/Philadelphia_Phillies.png',
    	width : 64,
    	height : 64
  }
  ```

- 创建一个实体方法

  ```javascript
  //方法一
  var entity = new Entity({
      id : 'uniqueId'
  });
  viewer.entities.add(entity);
  
  //方法二 简写
  viewer.entities.add({
      id : 'uniqueId'
  });
  
  //方法三
  var entity = viewer.entities.getOrCreateEntity('uniqueId');
  ```

- 实体查询

  ```javascript
  var entity = viewer.entities.getById('uniqueId');
  ```

- 删除实体

  ```javascript
  //方法一，先查后删
  var entity = viewer.entities.getById('uniqueId');
  viewer.entities.remove(entity) 
  //方法二，直接删除
  viewer.entities.removeById('uniqueId')
  //方法三，删除所有
  viewer.entities.removeAll()
  ```

### **Camera 定位方法**

`Cesium` 相机控制场景中的视野。操作相机的方法有很多，如旋转，缩放，平移和飞到目的地。`Cesium`具有默认的鼠标和触摸事件处理程序与相机进行交互，还有一个API以编程方式操纵相机。

我们可以使用该  `setView` 功能设置相机的位置和方向。目的地可以是一个实例 `Cartesian3` 或`Rectangle`，方向可以是航向/俯仰/卷或方向/向上。

```javascript
// Cartesian3确定位置
var widget = new Cesium.CesiumWidget('cesiumContainer');
widget.camera.setView({
    destination: Cesium.Cartesian3.fromDegrees(-117.16, 32.71, 15000.0,
    orientation: {  // 调整角度
        heading : Cesium.Math.toRadians(20.0), // 方向
        pitch : Cesium.Math.toRadians(-90.0),// 倾斜角度
        roll : 0
    }                                          
})
/*
   heading, 可以这样理解从左到右，角度控制，左正右负
   pitch, 可以理解上下角度，上为正，下为负
   roll, 旋转角度一般不设
*/

// Rectangle 
widget.camera.setView({
    destination : Cesium.Rectangle.fromDegrees(west, south, east, north)
})
```

### flyTo

通过调用该方法，可以跳转镜头到指定位置，具体用法和上面类似。

```javascript
view.camera.flyTo({
        destination :  Cesium.Cartesian3.fromDegrees(116.435314,39.960521, 15000.0), // 设置位置
        orientation: {
            heading : Cesium.Math.toRadians(20.0), // 方向
            pitch : Cesium.Math.toRadians(-90.0),// 倾斜角度
            roll : 0
        },
        duration:5, // 设置飞行持续时间，默认会根据距离来计算
        complete: function () {
            // 到达位置后执行的回调函数
            console.log('到达目的地');
        },
        cancle: function () {
            // 如果取消飞行则会调用此函数
            console.log('飞行取消')
        },
        pitchAdjustHeight: -90, // 如果摄像机飞越高于该值，则调整俯仰俯仰的俯仰角度，并将地球保持在视口中。
        maximumHeight:5000, // 相机最大飞行高度
        flyOverLongitude: 100, // 如果到达目的地有2种方式，设置具体值后会强制选择方向飞过这个经度
    });
```

### lookAt

该方法会将视角固定在设置的点位上

### `viewer.flyTo()` 与  `camera.flyTo()` 区别

```javascript
function viewerflyToLonLat(lon,lat,alt) {
        if(entity)
            viewer.entities.remove(entity);
        entity = new Cesium.Entity({
            id : 'flyTmp',
            position : Cesium.Cartesian3.fromDegrees(lon, lat),
            point : {
                pixelSize : 10,
                color : Cesium.Color.WHITE.withAlpha(0.9),
                outlineColor : Cesium.Color.WHITE.withAlpha(0.9),
                outlineWidth : 1
            }
        });
        viewer.entities.add(entity);
    // 关键代码
        viewer.flyTo(entity, {
            offset : {
                heading : Cesium.Math.toRadians(0.0),
                pitch : Cesium.Math.toRadians(-25),
                range : alt // 距离中心的距离
            }
        });
    }

/*
	camera.flyTo定位结果
*/
viewer.camera.flyTo({
            destination : Cesium.Cartesian3.fromDegrees(lon, lat,alt),
            orientation : {
                heading : Cesium.Math.toRadians(0.0),
                pitch : Cesium.Math.toRadians(-25.0),
                roll : 0.0
            }
        });
```

**注**：当相机的pitch不是默认值得时候，就会出现，camera定位的位置并不在屏幕中心位置，这时候使用viewer来定位就能解决问题

### viewer.zoomTo()

该方法一般用于拉近相机与物体距离。

### Cesium坐标拾取

- 屏幕坐标：鼠标点击位置距离`canvas`左上角的像素值

  ```javascript
  var handler= new Cesium.ScreenSpaceEventHandler(viewer.scene.canvas);
  
  handler.setInputAction(function (movement) {
  // movement.position 为屏幕坐标
   console.log(movement.position);
  }, Cesium.ScreenSpaceEventType.LEFT_CLICK);
  ```

- **地图/椭球体表面的坐标**

  pickEllipsoid(windowPosition, ellipsoid, result) → Cartesian3

  ```javascript
   var canvas = viewer.scene.canvas;
   var center = new Cesium.Cartesian2(canvas.clientWidth / 2.0, canvas.clientHeight / 2.0);
   var ellipsoid = viewer.scene.globe.ellipsoid;
   var result = viewer.camera.pickEllipsoid(center, ellipsoid);
  ```

- **地球坐标**：只能求交于地形，不包括模型、倾斜摄影表面，能获取加载地形后的坐标，`pick(ray, scene, result) → Cartesian3|undefined`

  ```javascript
  // 从相机位置到windowPosition 世界坐标中的像素创建射线。
    // 返回Cartesian3射线的位置和方向。
    var ray = viewer.camera.getPickRay(windowCoordinates);
    // 查找射线与渲染的地球表面之间的交点。射线必须以世界坐标给出。
    var intersection = globe.pick(ray, scene);
  ```

- **场景坐标**：根据窗口坐标，从场景的深度缓冲区拾取相应的位置，返回笛卡尔坐标，不仅可以求交地形，还可以求交除地形以外其他所有写深度的物体
  `pickPosition(windowPosition, result) → Cartesian3`

  ```javascript
  var viewer = new Cesium.Viewer('cesiumContainer');
    var handler = new Cesium.ScreenSpaceEventHandler(viewer.scene.canvas);
    handler.setInputAction(function (movement) {
        var pickedObject = viewer.scene.pick(movement.endPosition);
        // 使用时，最好利用pickPositionSupported判断一下浏览器是否支持模型高度拾取 
        if (scene.pickPositionSupported && Cesium.defined(pickedObject)) {
            var cartesian = viewer.scene.pickPosition(movement.endPosition);
         	console.log(cartesian);
        } 
    }, Cesium.ScreenSpaceEventType.LEFT_CLICK);
  ```

- **`globe.pick`**与**`scene.pickPosition`**比较

  - `globe.pick`的结果相对稳定准确，不论地形深度检测开启与否，不论加载的是默认地形还是别的地形数据;

  - `scene.pickPosition`只有在开启地形深度检测，且不使用默认地形时是准确的。

  - **注意点**：

    `globe.pick`只能求交地形；

    `scene.pickPosition`不仅可以求交地形，还可以求交除地形以外其他所有写深度的物体。
    所以使用时可以二者结合来使用。

### `Scene`中`pick`讲解

- `pick：scene.pick`可以通过此方法获取到pick对象，通过`pick.id`即可拾取当前的`entity`对象，也可以获取`Cesium3DTileFeature`对象；
- `drillPick：scene.drillPick(click.position)`是从当前鼠标点击位置获取`entity`的集合，然后通过for循环可以获取当前坐标下的所有entity。