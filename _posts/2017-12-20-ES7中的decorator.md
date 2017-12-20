---
layout: default
title: ES7中的decorator
---
# {{page.title}}

### 什么是decorator

decorator，顾名思义，装饰器，用来装饰类或者类的方法，目前在ECMAScript中已经有[提案](https://github.com/tc39/proposal-decorators)加入了这个功能。
decorator主要的作用就是可以用来修改类或者方法的默认行为，增加一些自定义操作，并且不会影响原有的逻辑，如常用的安全检查，日志系统，调试功能等。