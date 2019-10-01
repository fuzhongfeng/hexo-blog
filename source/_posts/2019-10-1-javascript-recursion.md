---
title: JavaScript-recursion
layout: post
date: 2019-10-1 9:36:30
categories: JavaScript
tags: JavaScript
---
## 递归
>递归函数是在一个函数通过名字调用自身的情况下构成的

### 看一个经典的递归阶乘函数
```
// 复杂度 O(n)
function factorial(num) {
  if (num === 1) {
    return num
  } else {
    return num * factorial(num - 1)
  }
}
```

### 递归函数调用自身问题
* 递归调用的函数被重置后会出现问题
```
var anotherFactorial = factorial
factorial = null
anotherFactorial(4) //出错!
```

* arguments.callee 是一个指向正在执行函数的指针，因此可以用它来实现对函数的递归调用。但在严格模式下，不能通过脚本访问 arguments.callee，访问这个属性会导致错误
```
function factorial(num) {
  if (num === 1) {
    return num
  } else {
     return num * arguments.callee(num - 1)
  }
}
```

* 使用命名函数表达式来达成相同的结果
```
var factorial = (function f() {
  if (num === 1) {
    return num
  } else {
     return num * f(num - 1)
  }
})
```
函数f是不对外暴露的，所以执行过程中不会被重置，这种方式在严格模式和 非严格模式下都行得通。

### 尾递归
* 尾调用是指函数的最后一步是调用另一个函数。尾调用只保留内层函数的调用记录，可释放掉多余的内存占用。
* 尾调用自身，就称为尾递归。递归非常耗费内存，很容易发生"栈溢出"错误（stack overflow）。尾调用指保存一个调用记录，所以不会发生栈溢出的问题。

### 利用尾递归重写阶乘函数
```
// 复杂度 O(1)
function factorial(num, total = 1) {
  if (num === 1) {
    return total
  } else {
    total = num * total
    return factorial(num - 1, total)
  }
}
factorial(5)
```
只要函数执行的最后一步为自身函数调用即可。上面例子中的 num * factorial(num - 1) 也不算尾调用，因为一直保存着对 num 的引用。

### 但浏览器一定会识别尾递归的优化吗？

* 下面在chrome控制台执行
```
// 经典阶乘递归函数
function factorial(num) {
  if (num === 1) {
    return num
  } else {
    return num + factorial(num - 1)
  }
}
factorial(100000)
// 此时栈溢出：Maximum call stack size exceeded
```
* 下面再使用尾递归就不会栈溢出了吗？
```
// 尾递归
function factorial(num, total) {
  if (num === 1) {
    return total
  } else {
    return factorial(num - 1, num + total)
  }
}
factorial(100000)
// 此时依然会栈溢出！！！：Maximum call stack size exceeded
```
* 查询后发现各大浏览器（除了safari）根本就没部署尾调用优化，因为尾调用优化依旧有隐式优化（函数是否符合尾调用而被消除了尾递归很难被开发员自己辨别）和调用栈丢失的问题。

* node 低版本中本中可以开启尾递归如：
```
// node 6.6.0 版本通过指定参数开启：
// PTC.js
'use strict';
function f(n, sum = 1) {
    if (n <= 1) {
        return sum;
    }
    return f(n - 1, sum + n);
}
const result = f(100000);
console.log(result);

$node --harmony_tailcalls PTC.js  // 5000050000
```
