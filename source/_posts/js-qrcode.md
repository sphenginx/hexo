title: 二维码图片生成器QRCode.js
date: 2015-9-27 16:03:01
categories: 日薪越亿
tags: [js, qrcode]
---

## 二维码是什么？
百度百科上是这样介绍二维码的：二维码（Quick Response Code），又称二维条码，它是用特定的几何图形按一定规律在平面（二维方向）上分布的黑白相间的图形，是所有信息数据的一把钥匙。在现代商业活动中，可实现的应用十分广泛，如：产品防伪/溯源、广告推送、网站链接、数据下载、商品交易、定位/导航、电子商务应用、车辆管理、信息传递等。如今智能手机扫一扫（简称313）功能的应用使得二维码更加普遍，随着国内物联网产业的蓬勃发展，更多的二维码技术应用解决方案被开发，二维码成为移动互联网入口真正成为现实。 

## QRCode.js说明
QRCode.js是一个实现生成二维码(QRCode)的js插件。 QRCode.js有着良好的跨浏览器兼容性（高版本使用HTML5的 Canvas，低版本IE使用table元素绘制），而且QRCode.js没有任何依赖。只需要引用一个QRCode.js。

## 使用QRCode.js

### 引入qrcode.js

``` bash
<script src=”qrcode.js” type=”text/javascript”></script>
```

### HTML代码

``` bash
<div id=”qrcode”></div>
```


### JS代码

``` bash
//初始化QRCode对象
var qrcode = new QRCode(document.getElementById(“qrcode”));

//也可以在初始化QRCode对象，传入更多参数
var qrcode = new QRCode(document.getElementById(“qrcode”),{
	width: 128,
	height: 128,
	colorDark : “#000000″,
	colorLight : “#ffffff”,
	correctLevel : QRCode.CorrectLevel.H
});

//需要生成二维码的字符串
qrcode.makeCode(“http://www.leixuesong.cn”);

//清除二维码
qrcode.clear();
```

### 参数说明

|参数|类型|说明|
|-----|-----|-----|-----|
|render    |string | 配置用哪个节点元素画二维码，选项有table、svg和canvas，默认的选择顺序为 canvas -> svg -> table
|text    |string | 要编码的字符串，默认为空
|width   |number | 二维码的长，单位是px  需要注意的是，当使用table或者svg绘制二维码时，会适当减小，使得能够整除二维码矩阵的维度。  默认：256
|height   |number | 同width
|correctLevel    |number | 纠错级别，可取0、1、2、3，数字越大说明所需纠错级别越大，默认：3
|background  |color | 背景色，默认：`#FFFFFF`
|foreground  |color | 前景色，默认：`#000000`

## 浏览器兼容性

几乎支持所有浏览器： IE6~10, Chrome, Firefox, Safari, Opera, Mobile Safari, Android, Windows Mobile.

到这里，js生成二维码插件-QRCode.js就介绍完了，QRCode.js非常的方便好用。需要注意的的是QRCode初始化传入DOM对象时，必须是js原生的DOM对象，不能是jQuery的DOM对象的，否则就会报错。

## 附
GitHub地址: [https://github.com/davidshimjs/qrcodejs](https://github.com/davidshimjs/qrcodejs)
二维码生成原理：[http://www.thonky.com/qr-code-tutorial/](http://www.thonky.com/qr-code-tutorial/)