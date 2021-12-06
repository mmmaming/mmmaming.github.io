---
layout: default
title: Performance使用指南
---
# {{page.title}}


### FPS
![FPS](https://developer-chrome-com.imgix.net/image/admin/rLbUyQ0Y0p6xIiwy85SK.svg)
图中部分表示FPS，红色表示FPS低，绿色表示FPS高。

### CPU
![CPU](https://developer-chrome-com.imgix.net/image/admin/XFtPfdKzTPBXeQC9g9OC.svg)
图中部分表示CPU的使用情况，CPU图标的颜色和下面的 **Summary** 是一个颜色，当CPU的颜色块显示的越多，表示CPU使用率越高


### 实时FPS
打开控制台后，按下Command+Shift+P 打开命令菜单，输入Rendering，选择 **Show Rendering**
，在**Rendering**tab中选择FPS Meter

### Main
![Main](https://developer-chrome-com.imgix.net/image/admin/SqkREdHdAzgeXavWwMtC.svg)
图中表示主线程的事件。 X轴表示每个event以及event花费的时间。y轴表示调用栈，如果有多个event堆叠在一起，表示上面的event引起的下面的event

![Main](https://developer-chrome-com.imgix.net/image/admin/IeZiW7iMHzE1MwxqFPAg.png?auto=format&w=845)
当看到event的右上角有个红色的三角形时，表明这里可能有一个性能问题。点击红色三角形，在Summary Tab会显示关于这个event的信息，会提示引起问题的对应代码。例如图中的app.js94


## 优化
使用requestAnimationFrame而不是setTimeout或setInterval实现动画效果。
requestAnimationFrame的好处是，可以在屏幕进行重绘前进行调用，也就是正好在帧的开头，不会像setTimeout一样出现丢帧的情况。
callback接受一个参数time,该参数与performance.now()的返回值相同，它表示requestAnimationFrame() 开始去执行回调函数的时刻。
```
function animation(time) {
   // xxx; 执行动画
    // 持续调用此动画逻辑，可以在这里加入动画停止的逻辑判断
    requestAnimationFrame(animation);
}
requestAnimationFrame(animation);

```

将长时间运行的JavaScript从主线程移到Web Worker。

使用微任务来执行对多个帧的DOM更改。

降低CSS选择器的复杂性，使用以类为中心的方法，例如BEM。

减少必须计算其样式的元素数量。

提高性能的九个技巧
有一些技巧，可以降低浏览器重新渲染的频率和成本。

第一条是上一节说到的，DOM 的多个读操作（或多个写操作），应该放在一起。不要两个读操作之间，加入一个写操作。

第二条，如果某个样式是通过重排得到的，那么最好缓存结果。避免下一次用到的时候，浏览器又要重排。

第三条，不要一条条地改变样式，而要通过改变class，或者csstext属性，一次性地改变样式。

```
// bad
var left = 10;
var top = 10;
el.style.left = left + "px";
el.style.top  = top  + "px";

// good 
el.className += " theclassname";

// good
el.style.cssText += "; left: " + left + "px; top: " + top + "px;";
```
第四条，尽量使用离线DOM，而不是真实的网面DOM，来改变元素样式。比如，操作**Document Fragment**对象，完成后再把这个对象加入DOM。再比如，使用 cloneNode() 方法，在克隆的节点上进行操作，然后再用克隆的节点替换原始节点。

第五条，先将元素设为display: none（需要1次重排和重绘），然后对这个节点进行100次操作，最后再恢复显示（需要1次重排和重绘）。这样一来，你就用两次重新渲染，取代了可能高达100次的重新渲染。

第六条，position属性为absolute或fixed的元素，重排的开销会比较小，因为不用考虑它对其他元素的影响。

第七条，只在必要的时候，才将元素的display属性为可见，因为不可见的元素不影响重排和重绘。另外，visibility : hidden的元素只对重绘有影响，不影响重排。

第八条，使用虚拟DOM的脚本库，比如React等。

第九条，使用 window.requestAnimationFrame()、window.requestIdleCallback() 这两个方法调节重新渲染

第十条 使用GPU进行硬件加速，比如translate3d、translateZ等
