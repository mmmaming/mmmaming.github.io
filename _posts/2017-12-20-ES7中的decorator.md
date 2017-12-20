---
layout: default
title: ES7中的decorator
---
# {{page.title}}

## 什么是decorator

decorator，顾名思义，装饰器，用来装饰类或者类的方法，目前在ECMAScript中已经有[提案](https://github.com/tc39/proposal-decorators)加入了这个功能。
decorator主要的作用就是可以用来修改类或者方法的默认行为，增加一些自定义操作，并且不会影响原有的逻辑，如常用的安全检查，日志系统，调试功能等。

## 如何使用decorator

decorator的使用方法有两种，一种是给类添加decorator，另一种是给类的属性添加decorator。（注意decorator不能用于装饰函数，因为函数存在函数提升的情况）
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

接下来再看一个例子，我们想给一个Fruit类增加同样的装饰器。

```
const decorator = (target) => {
  target.isAnimal = true;
};

@decorator
class Fruit {

}
console.log(Fruit.isAnimal); // true
```

很明显，水果并不是动物，那么我们能不能使用装饰器的同时去做一些限制或者说增加一些条件的设定呢？答案是可以的。

```
const decorator = (flag) => (target) => {
  target.isAnimal = flag;
};

@decorator(false)
class Fruit {

}

@decorator(true)
class Cat {

}

console.log(Fruit.isAnimal); // false
console.log(Cat.isAnimal); // true
```

在上面的示例中，我们为装饰器增加了参数，完善了装饰器的功能。通过设定参数，可以更好的控制被装饰类。


接下来看第二种情况，给类的属性增加装饰器

写一个比较常见的情况，限制类的属性让其变为只读

```
const readonly = (target, key, descriptor) => {
  descriptor.writable = false;
  return descriptor;
};

class Cat {
  @readonly
  age = 4;
}

var cat = new Cat();
cat.age = 5;
console.log(cat.age);  // Uncaught TypeError: Cannot assign to read only property 'age' of object '#<Cat>'
```

现在，age属性变为只读属性了，对于age的修改操作都会在控制台提示报错。

同理，如果我们想通过参数去控制此属性是否需要设置成只读呢？同样的，写法类似于上面第一种情况的示例。

```
const readonly = (flag) => (target, key, descriptor) => {
  descriptor.writable = !flag;
  return descriptor;
};

class Cat {
  @readonly(false)
  age = 4;
  @readonly(true)
  name = 'tom';
}

var cat = new Cat();
cat.age = 5;
cat.name = 'jack';
console.log(cat.age); // 5
console.log(cat.name);// Uncaught TypeError: Cannot assign to read only property 'name' of object '#<Cat>'
```