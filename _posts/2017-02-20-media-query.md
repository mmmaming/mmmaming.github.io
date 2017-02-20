---
layout: default
title: 媒体查询
---

# {{page.title}}

### 语法:

```css
@media mediatype and|not|only (media feature) {
    CSS-Code;
}
```


```
<!--满足条件时，使用mystylesheet.css文件-->

<link rel="stylesheet" media="mediatype and|not|only (media feature)" href="mystylesheet.css">

```

```css
@import url("mystylesheet.css") screen;
@import url("mystylesheet.css") print;
```

### media type

| value	| description |
|:---- |:------|
|aural|	已废弃。用于语音和声音合成器|
|braille|	已废弃。 应用于盲文触摸式反馈设备|
|embossed|	已废弃。 用于打印的盲人印刷设备|
|handheld|	已废弃。 用于掌上设备或更小的装置，如PDA和小型电话|
|print|	用于打印机和打印预览|
|projection|	已废弃。 用于投影设备|
|screen|	用于电脑屏幕，平板电脑，智能手机等。|
|speech|	应用于屏幕阅读器等发声设备|
|tty|	已废弃。 用于固定的字符网格，如电报、终端设备和对字符有限制的便携设备|
|tv|	已废弃。 用于电视和网络电视|


### media feature

| value | description |
|:------|:------------|
| aspect-ratio|	定义输出设备中的页面可见区域宽度与高度的比率|
| color|	定义输出设备每一组彩色原件的个数。如果不是彩色设备，则值等于0|
| color-index|	定义在输出设备的彩色查询表中的条目数。如果没有使用彩色查询表，则值等于0|
| device-aspect-ratio|	定义输出设备的屏幕可见宽度与高度的比率。|
| device-height|	定义输出设备的屏幕可见高度。|
| device-width|	定义输出设备的屏幕可见宽度。|
| grid|	用来查询输出设备是否使用栅格或点阵。|
| height|	定义输出设备中的页面可见区域高度。|
| max-aspect-ratio|	定义输出设备的屏幕可见宽度与高度的最大比率。|
| max-color|	定义输出设备每一组彩色原件的最大个数。|
| max-color-index|	定义在输出设备的彩色查询表中的最大条目数。||
| max-device-aspect-ratio|	定义输出设备的屏幕可见宽度与高度的最大比率。||
| max-device-|	定义输出设备的屏幕可见的最大高度。||
| max-device-width|	定义输出设备的屏幕最大可见宽度。||
| max-height|	定义输出设备中的页面最大可见区域高度。||
| max-monochrome|	定义在一个单色框架缓冲区中每像素包含的最大单色原件个数。||
| max-resolution|	定义设备的最大分辨率。||
| max-width|	定义输出设备中的页面最大可见区域宽度。||
| min-aspect-ratio|	定义输出设备中的页面可见区域宽度与高度的最小比率。||
| min-color|	定义输出设备每一组彩色原件的最小个数。||
| min-color-index|	定义在输出设备的彩色查询表中的最小条目数。||
| min-device-aspect-ratio|	定义输出设备的屏幕可见宽度与高度的最小比率。||
|min-device-width|	定义输出设备的屏幕最小可见宽度。||
|min-device-height|	定义输出设备的屏幕的最小可见高度。||
|min-height|	定义输出设备中的页面最小可见区域高度。||
|min-monochrome|	定义在一个单色框架缓冲区中每像素包含的最小单色原件个数||
|min-resolution|	定义设备的最小分辨率。||
|min-width|	定义输出设备中的页面最小可见区域宽度。||
| monochrome|	定义在一个单色框架缓冲区中每像素包含的单色原件个数。如果不是单色设备，则值等于0||
| orientation|	定义输出设备中的页面可见区域高度是否大于或等于宽度。||
| resolution|	定义设备的分辨率。如：96dpi, 300dpi, 118dpcm||
| scan|	定义电视类设备的扫描工序。||
| width|	定义输出设备中的页面可见区域宽度。||