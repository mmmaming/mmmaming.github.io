---
layout: default
title: ES7中的decorator
---
# {{page.title}}

## 什么是decorator

decorator，顾名思义，装饰器，用来装饰类或者类的方法，目前在ECMAScript中已经有[提案](https://github.com/tc39/proposal-decorators)加入了这个功能。
decorator主要的作用就是可以用来修改类或者方法的默认行为，增加一些自定义操作，并且不会影响原有的逻辑，如常用的安全检查，日志系统，调试功能等。

## 如何使用decorator

decorator的使用方法有两种，一种是给类添加decorator，另一种是给类的方法添加decorator。（注意decorator不能用于装饰函数，因为函数存在函数提升的情况）
先来看第一种情况。

```
const decorator = (target) => {
  target.isAnimal = true;
};

@decorator
class Cat {

}
console.log(Cat.isAnimal); // true
```

在上面的例子中，我们为Cat类增加了名为decorator的装饰器，意味着我们需要装饰的是这个类，在decorator方法中的参数target指的就是要装饰的类，在例子中是Cat类。为Cat类增加了一个属性 `isAnimal`,则我们的Cat上就会挂载一个isAnimal属性，我们并不需要去修改原有的Cat类，即可修改Cat的默认行为。