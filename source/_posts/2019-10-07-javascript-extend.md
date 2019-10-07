---
title: JavaScript-继承
layout: post
date: 2019-10-07 21:21:11
categories: JavaScript
tags: JavaScript
---

# 继承
> es5 实现继承的 6 种方式和优缺点，以及继承的最佳方案

## 1. 原型链

* 实现：
```
function Super() {
    this.property = true
}
Super.prototype.getSuperValue = function() {
    return this.property
}

function Sub(){
    this.subProperty = false
}
Sub.prototype = new Super()

// 替换原型后添加新方法
Sub.prototype.getSubValue = function() {
    return this.subProperty
}

var instance = new Sub()
console.log(instance.getSuperValue())
```

* 原型链的问题: 
1. 包含引用类型值的原型属性会被所有实例共享
```
function Super() {
    this.colors = ["red", "blue", "green"];
    this.num = 1
}

function Sub(){
    this.subProperty = false
}
Sub.prototype = new Super()

var instance1 = new Sub()
instance1.colors.push("black")
instance1.num = 111 // 非引用类型属性不会互相影响
console.log(instance1.colors, instance1.num) // ["red", "blue", "green", "black"]  111

var instance2 = new Sub()
console.log(instance2.colors, instance2.num) // ["red", "blue", "green", "black"] 1
```
2. 无法在不影响所有实例的情况下，给父类的构造函数传递参数


## 借用构造函数
通过借用构造函数，可以解决原型中包含引用类型值所带来的问题。即
在子类构造函数的内部调用父类型构造函数
* 实现：
```
function Super(name){
    this.name = name
    this.colors = ["red", "blue", "green"]
}

function Sub(){
    //继承了 Super
    Super.call(this, 'fuzhongfeng')
    this.age = 29
}

var instance1 = new Sub()
var instance2 = new Sub()
instance1.colors.push("black");
console.log(instance1.name, instance1.age) // 'fuzhongfeng' 29
console.log(instance1.colors);    //"red,blue,green,black"
console.log(instance2.colors);    //"red,blue,green"
```

* 借用构造函数的问题: 
1. 方法都在构造函数中定义，因此函数不能复用


## 组合继承
组合继承，指的是将原型链和借用构造函数的技术组合到一块，从而发挥二者之长的一种继承模式。即使用原型链实现对原型属性和方法的继承，而通过借用构造函数来实现对实例属性的继承
* 实现：
```
function Super(name){
    this.name = name;
    this.colors = ["red", "blue", "green"];
}
Super.prototype.sayName = function(){
    console.log(this.name);
}

function Sub(name, age){
    //继承属性 
    Super.call(this, name);
    this.age = age;
}

Sub.prototype = new Super();
Sub.prototype.constructor = Sub; // constructor 指回自身
Sub.prototype.sayAge = function(){
    console.log(this.age);
};

var instance1 = new Sub("fuzhongfeng", 29);
instance1.colors.push("black");
console.log(instance1.colors); // "red,blue,green,black"
instance1.sayName(); // "fuzhongfeng"
instance1.sayAge(); // 29

var instance2 = new Sub("Feynman", 27);
console.log(instance2.colors); // "red,blue,green"
instance2.sayName(); // "Feynman"
instance2.sayAge(); // 27
```
* 组合继承的问题：
1. 组合继承最大的问题就是无论什么情况下，都会调用两次父类构造函数: 一次是在创建子类原型的时候，另一次是在子类构造函数内部。所以子类的原型上包含父类的实例属性，但在调用子类构造函数时也会包含父类的实例属性。子类实例属性会屏蔽子类原型上的同名属性。


### 原型式继承
创建了一个临时性的构造函数，然后将传入的对象作为这个构造函数的原型，最后返回了这个临时类型的一个新实例。
* 实现：
```
// object()对传入其中的对象执行了一次浅复制
function object(o){
    function F(){};
    F.prototype = o;
    return new F();
}
```
原生方法：
```
Object.create()
```
* 原型式继承的问题：
包含引用类型值的属性始终都会共享相应的值，就像使用原型模式一样。


## 寄生式继承
创建一个仅用于封装继承过程的函数，该 函数在内部以某种方式来增强对象，最后再像真地是它做了所有工作一样返回对象
* 实现：
```
function createAnother(original){ 
    var clone = Object.create(original); // 通过调用函数创建一个新对象
    clone.sayHi = function(){
        console.log("hi");
    };
 }
```
* 寄生式继承的问题：
1. 使用寄生式继承来为对象添加函数，会由于不能做到函数复用而降低效率; 这一点与构造函数模式类似。


## 寄生组合式继承(最佳方案！！！)
不必为了指定子类的原型而调用超类的构造函数，我们所需要的无非就是超类原型的一个副本而已。本质上，就是使用寄生式继承来继承超类的原型，然后再将结果指定给子类的原型
* 实现：
```
function inheritPrototype(sub, super){
    // var prototype = Object.assign({}, super.prototype);
    var prototype = Object.create(super.prototype);
    prototype.constructor = sub;
    sub.prototype = prototype;
}

function Super(name){
    this.name = name;
    this.colors = ["red", "blue", "green"];
}
Super.prototype.sayName = function(){
    console.log(this.name);
};

function Sub(name, age){
    Super.call(this, name);
    this.age = age;
}

inheritPrototype(Sub, Super);

Sub.prototype.sayAge = function(){
    console.log(this.age);
}
```
