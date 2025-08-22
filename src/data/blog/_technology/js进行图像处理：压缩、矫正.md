---
title: js进行图像处理：压缩、矫正
author: Leohoo
pubDatetime: 2023-04-28 15:11:26
slug: javascript-image-processing-compress-rotate
featured: false
draft: false
tags:
  - JavaScript
description: JavaScript图像处理实战指南，使用Canvas和EXIF.js解决移动端图片压缩和拍照旋转问题的完整方案。
---

## Table of contents

## 问题由来-背景

项目中用到了`antd mobile` 的 ImagePicker，用来选择图片。其中有一个onChange属性，files 值发生变化触发的回调函数

> operationType 操作类型有添加，移除，如果是移除操作，则第三个参数代表的是移除图片的索引;
类型：(files: Object, operationType: string, index: number): void
>

## 现象

将得到的base64值赋予图片的src，并在屏幕上显示。

1. 图片太大，响应时间长
2. 直接选择相册照片能正常显示，但通过摄像头拍取的照片会发生90度的旋转

## 解决方案

利用canvas进行压缩

<aside>
💡 核心：canvas.toDataURL

</aside>

```jsx
/**
 * 判断图片是否需要压缩
 * @param image          HTMLImageElement
 * @param imageSize      int
 * @returns {*}          HTMLImageElement
 */
function judgeCompress(image,imageSize) {

    //判断图片是否大于 300000 bit
    var threshold = 300000;//阈值,可根据实际情况调整
    console.log('imageSize:'+imageSize)
    if(imageSize>threshold){
        var imageData = compress(image);

        var newImage = new Image()
        newImage.src = imageData
        return newImage;
    }else {
        return image;
    }
}

/**
 *压缩图片
 * @param image         HTMLImageElement
 * @returns {string}    base64格式图像
 */
function compress(image) {
    console.log('compress');
    console.log(image)

    var canvas = document.createElement('canvas')
    var ctx = canvas.getContext('2d');

    var imageLength = image.src.length;
    var width = image.width;
    var height = image.height;

    canvas.width = width;
    canvas.height = height;

    ctx.drawImage(image, 0, 0, width, height);

    //压缩操作
    var quality = 0.1;//图片质量  范围：0<quality<=1 根据实际需求调正
    var imageData = canvas.toDataURL("image/jpeg", quality);

    console.log("压缩前：" + imageLength);
    console.log("压缩后：" + imageData.length);
    console.log("压缩率：" + ~~(100 * (imageLength - imageData.length) / imageLength) + "%");
    return imageData;
}
```

利用exif旋转图片

<aside>
💡 核心：获取到图片后使用 exif.js 的 EXIF.getDate(image, callback) API 获取图片的拍摄方向，根据拍摄方向进行不同角度的旋转。

</aside>

```jsx
/**
 * 旋转图片
 * @param image         HTMLImageElement
 * @returns newImage    HTMLImageElement
 */
function rotateImage(image) {
    console.log('rotateImage');

    var width = image.width;
    var height = image.height;

    var canvas = document.createElement("canvas")
    var ctx = canvas.getContext('2d');

    var newImage = new Image();

    //旋转图片操作
    EXIF.getData(image,function () {
            var orientation = EXIF.getTag(this,'Orientation');
            // orientation = 6;//测试数据
            console.log('orientation:'+orientation);
            switch (orientation){
                //正常状态
                case 1:
                    console.log('旋转0°');
                    // canvas.height = height;
                    // canvas.width = width;
                    newImage = image;
                    break;
                //旋转90度
                case 6:
                    console.log('旋转90°');
                    canvas.height = width;
                    canvas.width = height;
                    ctx.rotate(Math.PI/2);
                    ctx.translate(0,-height);

                    ctx.drawImage(image,0,0)
                    imageDate = canvas.toDataURL('Image/jpeg',1)
                    newImage.src = imageDate;
                    break;
                //旋转180°
                case 3:
                    console.log('旋转180°');
                    canvas.height = height;
                    canvas.width = width;
                    ctx.rotate(Math.PI);
                    ctx.translate(-width,-height);

                    ctx.drawImage(image,0,0)
                    imageDate = canvas.toDataURL('Image/jpeg',1)
                    newImage.src = imageDate;
                    break;
                //旋转270°
                case 8:
                    console.log('旋转270°');
                    canvas.height = width;
                    canvas.width = height;
                    ctx.rotate(-Math.PI/2);
                    ctx.translate(-height,0);

                    ctx.drawImage(image,0,0)
                    imageDate = canvas.toDataURL('Image/jpeg',1)
                    newImage.src = imageDate;
                    break;
                //undefined时不旋转
                case undefined:
                    console.log('undefined  不旋转');
                    newImage = image;
                    break;
            }
        }
    );
    return newImage;
}
```

## 文档

- [canvas](https://www.w3school.com.cn/tags/html_ref_canvas.asp)
- [exif.js](http://code.ciaoca.com/javascript/exif-js)
