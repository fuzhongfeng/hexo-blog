---
title: JavaScript-基础
layout: post
date: 2018-06-28 21:54:13
categories: JavaScript
tags: JavaScript
---

## null 和 0 比较

_最近在项目中修改 bug 时发现的一个不太理解的问题：_

```
params.estimatedStudentTime = values.estimatedStudentTime >= 0 ? 
    values.estimatedStudentTime : -1
```
本意是想根据 values.estimatedStudentTime 的值如果存在并且值大于等于零时才赋值给 params.estimatedStudentTime，当 values.estimatedStudentTime 的值不存在时则取 -1 再传递给接口。
乍一看判断没问题啊，然而控制台却发现还是将 null 值传递了出去。因为在某一处给 values.estimatedStudentTime 赋值为 null，但是 values.estimatedStudentTime >= 0 为什么不能过滤掉 null ？？？ 难道 null >= 0 返回true ？？？ 果然 null >= 0 返回 true
```
  console.log(null > 0);   // false
  console.log(null < 0);   // false
  console.log(null >= 0);   // true
  console.log(null <= 0);   // true
  console.log(null == 0);   // false
  console.log(null === 0);    // false
```
why ？？？
首先明确概念：
关系运算符： <、>、>=、<=
相等运算符：==、!=、===、!==

### 关系运算符比较时会尝试将值转为 number ，Number(null)  值为 0

然而 Number(undefined) 值却为 NaN, NaN >= 0 返回 false

## 函数柯里化

* 创建方式：调用另一个函数并为它传入要柯里化的函数和必要参数
```
function curry(fn){
  var args = Array.prototype.slice.call(arguments, 1);
  return function(){
      var innerArgs = Array.prototype.slice.call(arguments);
      var finalArgs = args.concat(innerArgs);
      return fn.apply(null, finalArgs);
  }; 
}
function add(num1, num2){ // 柯里化的函数
    return num1 + num2;
}
var curriedAdd = curry(add, 5);
curriedAdd(3) // 8
```

问题：实现一个 fn 函数返回如下结果
fn('a', 'b', 'c') // ['a', 'b', 'c']
fn('a', 'b')('c') // ['a', 'b', 'c']
fn('a')('b')('c') // ['a', 'b', 'c']
fn('a')('b', 'c') // ['a', 'b', 'c']

<!-- function fn() {
    var _args = [...arguments];
    var _adder = function() {
        _args.push(...arguments);
        return _adder;
    };
    _adder.toString = function () {
        return _args // 返回数组时出现问题
    }
    return _adder;
} -->