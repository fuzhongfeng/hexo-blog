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

*问题1：实现一个 fn 函数返回如下结果
```
fn('a', 'b', 'c') // ['a', 'b', 'c']
fn('a', 'b')('c') // ['a', 'b', 'c']
fn('a')('b')('c') // ['a', 'b', 'c']
fn('a')('b', 'c') // ['a', 'b', 'c']
```

函数式 柯里化解决：
```
function add (a, b, c) {
    console.log([a, b, c])
}

function curry(func) {
    var argLen = func.length

    return function _fn(...rest) {
         if(rest.length < argLen) {
           return _fn.bind(null, ...rest)
         } else {
             return func.apply(null, rest)   
         }
    }
}

var fn = curry(add)
```

## 实现 Function.prototype.call
```
Function.prototype.myCall = function(obj, ...args) {
    if (typeof this !== 'function') throw new Error('type error')

    // 默认值 window
    obj = obj || window

    // 防止覆盖 obj 已有 fn 属性
    const _fn = Symbol('fn')

    // call 位于函数实例的原型上，因此调用时 call 函数内的 this 指向函数实例自身
    obj[_fn] = this

    const _result = obj[_fn](...args)
    delete obj[_fn]
    
    return _result
}
```
* 测试一下
```
var o = {
    name: 'fu',
}

function test() {
    console.log(this.name)
}

test() // undefined
test.call(o) // 'fu'
test.myCall(o) // 'fu'
```

## 实现 Function.prototype.apply
```
Function.prototype.myApply = function(obj, args) {
    if (typeof this !== 'function') throw new Error('type error')

    // 默认值 window
    obj = obj || window

    // 防止覆盖 obj 已有 fn 属性
    const _fn = Symbol('fn')

    // call 位于函数实例的原型上，因此调用时 call 函数内的 this 指向函数实例自身
    obj[_fn] = this

    const _result = obj[_fn](...args)
    delete obj[_fn]
    
    return _result
}
```
* 测试一下
```
var o = {
    name: 'fu',
}

function test(...args) {
    console.log(this.name, ...args)
}

var arr = [1,2,3]

test(arr) // [1,2,3]
test.apply(o) // 'fu' 1,2,3
test.myApply(o) // 'fu' 1,2,3
```

## 实现 Function.prototype.bind
```
Function.prototype.myBind = function (thisArg, ...args) {
    if (typeof this !== "function") {
      throw Error("type error")
    }

    var self = this
    var fn = function () {
        self.apply(this instanceof self ? this : thisArg, args.concat(Array.prototype.slice.call(arguments)))
    }
    // 继承原型上的属性和方法
    fn.prototype = Object.create(self.prototype);

    return fn;
}
```
* 测试一下
```
var o = {
    name: 'fu',
}

function test(...args) {
    console.log(this.name, args)
}

var t = test.bind(o, 1,2,3)

t() // fu [1,2,3]
t(4) // fu [1,2,3,4]
```