---
layout: default
title: JS原型和继承
---
# {{page.title}}


![原型](https://github.com/mmmaming/mmmaming.github.io/blob/master/images/prototype.png?raw=true)

### 原型

对象分为普通对象和函数对象，函数对象有一个属性是原型对象`prototype`。普通对象没有`prototype`，但是有`__proto__`;
构造函数的.prototype对象也有`__proto__`属性，它指向创建它的函数对象（Object）的prototype `Person.prototype.__proto__ === Object.prototype`
通常在构造函数中的属性方法不会被共享，所以提出`prototype`，能让每个实例对象都共享构造函数的属性。

理解原型最好的一句话
每个对象都有一个`__proto__`属性，指向创建该对象的函数的`prototype`。
只有一个特例 `Object.prototype.__proto__ === null`

```
// 看这么一个例子
function A(){};
var a = new A();
var b = {};

a.__proto__ === A.prototype;

A.prototype.__proto__ === Object.prototype

A.__proto__ === Function.prototype

b.__proto__ === Object.prototype

a.__proto__.__proto__ === Object.prototype

Function.prototype.__proto__ === Object.prototype

Function.__proto__ === Function.prototype

Object.__proto__ === Function.prototype

// 以上输出结果全是 true
/*
说几个比较特殊的 Object.__proto__ === Function.prototype 
Object是函数，所以函数的__proto__指向创建该函数的函数的prototype，即Function.prototype
*/

/*
    Function.prototype.__proto__ === Object.prototype 
    Function.prototype是一个对象，所以对象的__proto__就指向Object.prototype,因为对象时通过Object函数创建的
*/

/*
Function.__proto__ === Function.prototype
创建Function的函数是Function
*/
```

### 继承

目前有很多种继承方式，原型继承，构造继承，实例继承，组合继承等等很多种方式，各有各的优缺点，在此不一一列举，直接说目前最通用，优点最多，缺点最少的一个方案，寄生组合继承。


```
function Cat(age) {
  this.tag = 'Cat';
  this.age = age.toFixed(2);
  this.breed = '猫科动物';
}

Cat.prototype.eat = function() {
  console.log('食肉动物');
};

function Tiger(name, age) {
  Cat.call(this, age); // 通过调用父类构造函数，将父类的实例属性复制给子类的实例
  this.name = name;
  this.tag = 'Tiger';
}

/*
    这里创建一个空函数，让空函数的原型等于父类的原型，并让子类的原型等于空函数的实例。
    这样做的好处是子类在继承多个父类时，可以在此集中父类的属性和方法，子类只需访问一层就可以继承父类。
    并且可以避免初始化两次父类构造函数的情况。
    
    在组合继承中，通过`Tiger.prototype = new Cat();`的方式继承父类，这样会多访问一次父类构造器，
    并且在后续的初始化子类的过程中（`Cat.call(this, age); `），覆盖掉这次继承的父类实例属性。
    子类实例屏蔽了原型上的实例。
    
*/
function Fun() {

}
Fun.prototype = Cat.prototype;
Tiger.prototype = new Fun();
Tiger.prototype.constructor = Tiger; // 最后记得将Tiger的构造函数重新指给Tiger，否则由于Tiger的原型被修改的原因，导致Tiger的constructor指向Cat.



Tiger.prototype.loveFood = function() {
  console.log('羚羊');
}
var tiger = new Tiger('老虎', 12);
var cat = new Cat(24);

/*
一个简单的判断实例是属于父类还是子类构造出来的方法
*/
function judge(obj, proto) {
  return obj.__proto__ === proto.prototype
}

```

寄生组合继承避免了上述许多继承方式的缺点，如多继承、函数复用、继承原型方法、多次调用父类构造器等问题，不失为目前比较好的一种继承方式。