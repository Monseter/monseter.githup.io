---
title: Cesium进阶
typora-root-url: ./
date: 2021-02-05 11:28:37
tags: Cesium
categories: 地图
---

## Cesium 点位聚合

实现思路：参考Cesium 中 提供了 `EntityCluster` 类来实现聚合功能，该类有一个事件`clusterEvent` 该事件中能获取到聚合的各种参数（如聚合点位数量），Cesium中`CustomDataSource` 这个api 接口，通过往其中添加entity，显示的效果就是聚类了。如果想定制样式，参考官网修改`customStyle` 中的代码即可（个体样式如果显示不出来，注意在 `clusterEvent` 中设置）。

可以参考官网的点位聚合实现。

- **`EntityCluster` 属性设置**

  在数据加载完成后，设置聚合功能和样式，而聚合的实现，主要有三个步骤：

  （1）设置数据启用聚合图标功能，并设置聚合距离；

  （2）首先创建设置聚合图标来渲染显示加载的数据；

  （3）聚合是实时更新的，所以需要一个绑定事件，把渲染的设置告诉`cesium`。

- **设置启用聚合**

  ```javascript
  this.options.entityData.forEach(entity => {
      this.clusterLayer.entities.add(entity)
  })
  this.clusterLayer.clustering.enabled = this.clusterOption.enabled;
  this.clusterLayer.clustering.pixelRange  = this.clusterOption.pixelRange;
  this.clusterLayer.clustering.minimumClusterSize = this.clusterOption.minimumClusterSize;
  ```

  代码中`pixelRange` 是聚合距离，也就是小于这个距离就会被聚合，以像素为单位；`minimumClusterSize`s 是每个聚合点的最小聚合个数，这个值最好设置为2，因为两个图标也可能叠压。

- **事件绑定**

  通过`clustering` 属性中的`clusterEvent.addEventListener` 方法绑定渲染回调函数，即：

  `Cesium.EntityCluster.newClusterCallback(cluteredEntities,cluster)` , 其中`clusteredEntities` 参数表示的是 `Entities` 数据集，而 `cluster` 是一个包含了`billboard、label、point、等属性对象`。

- **注意：**

  集群后的实体获取，无法并不是通过`entities ` 实体集获取，因为我们把所有的实体存放到`CustomDataSource` 的 `entities` 中，所以通过鼠标事件获取到的`pick` 对象就变了。代码如下：

  ```javascript
  let pick = this.viewer.scene.pick(e.position); // 用于查询点位实体 => Object
  
  // 聚合集群，pick中的 id 为实体，即 pick.id => Entity || [Entity, Entity]
  if (defined(pick)) {
      if (pick.id.id) { // 正常点
          console.log(pick, '存在正常实体')
      } else if (pick.id instanceof Array) { // 聚合实体
          console.log(pick, '存在聚合实体')
          this.flyToEntity(pick) // 聚合实体通过点击可以让它解聚
      }
  }
  ```
