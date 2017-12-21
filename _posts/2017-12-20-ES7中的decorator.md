---
layout: default
title: ES7中的decorator
---
# {{page.title}}

## 什么是decorator

`decorator`，顾名思义，装饰器，用来装饰类或者类的方法，目前在ECMAScript中已经有[提案](https://github.com/tc39/proposal-decorators)加入了这个功能。
`decorator`主要的作用就是可以用来修改类或者方法的默认行为，增加一些自定义操作，并且不会影响原有的逻辑，如常用的安全检查，日志系统，调试功能等。

## 如何使用decorator

decorator的使用方法有两种，一种是给类添加`decorator`，另一种是给类的属性添加`decorator`(普通的对象字面量属性也可以添加装饰器)。（注意`decorator`不能用于装饰函数，因为函数存在函数提升的情况）
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

在上面的例子中，我们为`Cat`类增加了名为`decorator`的装饰器，意味着我们需要装饰的是这个类，在`decorator`方法中的参数target指的就是要装饰的类，在例子中是`Cat`类。为`Cat`类增加了一个属性 `isAnimal`,则我们的`Cat`上就会挂载一个`isAnimal`属性，我们并不需要去修改原有的`Cat`类，即可修改`Cat`的默认行为。

接下来再看一个例子，我们想给一个`Fruit`类增加同样的装饰器。

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

现在，`age`属性变为只读属性了，对于`age`的修改操作都会在控制台提示报错。

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

我们注意到，第二种情况和第一种情况的写法不太一样，多了两个参数，如果仔细看的话，会发现，是不是和`Object.defineProperty`的参数很像？或者说是一模一样。

实际上它的执行就是
```
descriptor = readonly(Cat.prototype, 'age', descriptor) || descriptor
```
通过重新定义对象的属性，包装一个新的属性描述符安装到对应属性上来达到装饰原有属性的行为。

至此，`decorator`的两种使用方法，都做了介绍。

## decorator的应用场景

说了什么是`decorator`以及怎么写一个`decorator`之后，接下来要说的就是`decorator`的应用场景了。一个比较常见的场景是，一个记录操作的日志系统。

这里我们写一个数码宝贝中亚古兽的进化过程。

```
class Agumon {
  name = '亚古兽';
  startEvolution(name) {
    this.name = name;
  }

  currentName() {
    console.log(this.name);;
  }
}

var agumon = new Agumon();
agumon.currentName(); // 亚古兽

agumon.startEvolution('暴龙兽');
agumon.currentName(); // 暴龙兽

agumon.startEvolution('战斗暴龙兽');
agumon.currentName(); // 战斗暴龙兽
```

如果我们想记录亚古兽的每次进化，则可以写一个日志系统来记录亚古兽的每次进化。

```
const evolutionLog = (target, name, descriptor) => {
  const oldName = descriptor.value;
  descriptor.value = function(...arg) {
    console.log(`进化：  ${arg}`);
    return oldName.apply(this, arg);
  }
};

class Agumon {
  name = '亚古兽';

  @evolutionLog
  startEvolution(name) {
    this.name = name;
  }

  currentName() {
    console.log(this.name);
  }
}

var agumon = new Agumon();
agumon.currentName();

agumon.startEvolution('暴龙兽');
agumon.currentName();

agumon.startEvolution('战斗暴龙兽');
agumon.currentName();

// 亚古兽
// 进化：  暴龙兽
// 暴龙兽
// 进化：  战斗暴龙兽
// 战斗暴龙兽
```