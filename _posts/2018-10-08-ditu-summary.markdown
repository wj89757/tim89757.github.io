---
layout:     post
title:      "Solr"
subtitle:   "各种地图探究-百度地图/谷歌地图/OpenStreetMap"
date:       2018-10-08 17:20:00
author:     "Tim"
header-img: "img/baidu-map.png"
tags:
    - 地图
    - 百度地图
    - 谷歌地图
    - OpenStreetMap
---

> 各种地图探究-百度地图/谷歌地图/OpenStreetMap

### 1.简介

​	主要总结地图使用上的一些技巧，还有一些踩到的一些坑和一些比较好用的第三方工具。针对百度地图marker，polygon等一些常用到的类进行使用上的见解，和分享一些总结一些封装好的util。

![baidu-map](/img/baidu-map.png)

### 2.百度地图

> http://lbsyun.baidu.com/index.php 【具体参考->百度地图开放平台】
>
> http://lbsyun.baidu.com/index.php?title=jspopular/openlibrary 【百度地图JavaScript开源库，是一套基于百度地图API二次开发的开源的代码库】

#### 1.覆盖物

​	**Overlay：**覆盖物的抽象基类，所有覆盖物均继承基类的方法。此类不可实例化。

​	

#### 2.画图工具

​	应用场景：一些地块描边，自定义画圈搜索该区域内的点

1. 引入百度地图http://api.map.baidu.com/api?v=2.0&ak=<你自己的ak>
2. 引入http://api.map.baidu.com/library/TextIconOverlay/1.2/docs/symbols/BMapLib.html#constructor中的js

```javascript
//实例化鼠标绘制工具
var drawingManager = new BMapLib.DrawingManager(map, {
    isOpen: false, //是否开启绘制模式
    drawingToolOptions: {
        offset: new BMap.Size(5, 5), //偏离值
        drawingTypes: [BMAP_DRAWING_POLYGON]
    },
    polygonOptions: <styleOptions>, //多边形的样式
});
// 不计算面积
drawingManager.disableCalculate(); 
//添加鼠标绘制工具监听事件，用于获取绘制结果
drawingManager.addEventListener('polygoncomplete', polygonOverlayComplete); 
// 开始绘制
$('<element>').click(function () {
    drawingManager.close();
    map.clearOverlays();
    drawingManager.open();
});
// 结束绘制
var polygonOverlayComplete = function (e) {
    var path = e.getPath(); // 描完边的点
    drawingManager.close();
    // todo do something
};
```

#### 3.行车等距离服务



#### 4.总结工具类

```javascript
/**
 * 设置marker的备注
 * @param marker marker对象
 * @param lat 纬度
 * @param lng 经度
 * @param content 备注
 */
function setMarkerLabel(marker, lat, lng, content) {
    if (!content) {
        return;
    }
    var opts = {
        position: new BMap.Point(lng, lat),
        offset:new BMap.Size(20,-10)
    };
    var label = new BMap.Label(content, opts);
    label.setStyle({
        color: "red",
        fontSize: "12px",
        height: "20px",
        lineHeight: "20px",
        fontFamily: "微软雅黑",
    });
    marker.setLabel(label);
}

/**
 * 在地图上添加marker点
 * @param marker map对象
 * @param lat 纬度
 * @param lng 经度
 * @param content 备注
 */
function addMarkerInMap(map, lat, lng, labelContent) {
    var thisMarker = new BMap.Marker(new BMap.Point(lng, lat));
    map.addOverlay(thisMarker);
    setLabels(thisMarker, point, labelContent)
}

/**
 * 删除百度地图上的点
 * @returns {boolean}
 */
function deletePoint(map, lat, lng) {
    var allOverlay = map.getOverlays();
    for (var i = 0; i < allOverlay.length; i++) {
        if (allOverlay[i].getPosition === null || allOverlay[i].getPosition === undefined) {
            continue;
        }
        //删除指定经度的点
        if (allOverlay[i].getPosition().lng === lng && allOverlay[i].getPosition().lat === lat) {
            map.removeOverlay(allOverlay[i]);
            return true;
        }
    }
}

/**
 * 生成一个百度地图上的button
 * @param name
 * @returns {Element}
 */
function generateButton(name) {
    // 创建一个DOM元素
    var div = document.createElement("div");
    // 添加文字说明
    div.appendChild(document.createTextNode(name));
    div.style.cursor = "pointer";
    div.style.border = "1px solid #8fa9de";
    div.style.height = "20px";
    div.style.lineHeight = "20px";
    div.style.fontSize = "12px";
    div.style.width = "40px";
    div.style.textAlign = "center";
    div.style.borderRadius = "2px";
    div.style.backgroundColor = "white";
    return div;
}

/**
 * 描边polygon
 * @param arrPointArr 数组点,形如[new BMap.Point(116,23), new BMap.Point(117,21)]，这里可以调用下面的getPolygonArrayAndCenter函数更方便得到
 * @param param
 * @returns {*}
 */
function getPolygon(arrPointArr, param) {
    return new BMap.Polygon(arrPointArr, {
        strokeColor: param.strokeColor,
        strokeWeight: param.strokeWeight,
        strokeOpacity: param.strokeOpacity,
        fillColor: param.fillColor
    });
}

/**
 * 分割polygon点集合,返回polygon中心点和分割点集合
 * @param boundary
 * @returns {{}}
 */
function getPolygonArrayAndCenter(boundary) {
    var result = {};
    var arrPointInit = [];
    var latList = [];
    var lngList = [];
    if (boundary !== "" && boundary !== undefined) {
        var arrPointStr = boundary.split(";");
        for (var i = 0; i < arrPointStr.length; i++) {
            var arrPointLL = arrPointStr[i].split(',');
            if (arrPointLL[0] !== "" && arrPointLL[1] !== "") {
                var yipoi = new BMap.Point(parseFloat(arrPointLL[0]), parseFloat(arrPointLL[1]));
                latList.push(parseFloat(arrPointLL[1]));
                lngList.push(parseFloat(arrPointLL[0]));
                arrPointInit.push(yipoi);
            }
        }
    }
    result.points = arrPointInit;
    result.center = findAvg(findMin(latList), findMin(lngList), findMax(latList), findMax(lngList));
    return result;
}

function findMin(latList) {
    return Math.min.apply(Math,latList)
}

function findMax(latList) {
    return Math.max.apply(Math,latList)
}

function findAvg(minLat, minLng, maxLat, maxLng) {
    var point = {};
    point.lat = (maxLat + minLat) / 2;
    point.lng = (maxLng + minLng) / 2;
    return point;
}
```