---
title: JavaScript-类型判断
layout: post
date: 2019-03-08 20:05:21
categories: JavaScript
tags: JavaScript
---

## 类型判断
> 说到 javascript 中的类型判断方法有很多。如 typeof、instanceof、Array.isArray()、Object.prototype.toString.call()，下面会详细讲解优缺点及具体用法以及最佳方案。

### typeof 
> typeof 适用于检测基本数据类型

* typeof 操作符可能返回下列字符串:
"undefined"——如果这个值未定义
"boolean"——如果这个值是布尔值
"string"——如果这个值是字符串
"number"——如果这个值是数值
"object"——如果这个值是对象或null
"function"——如果这个值是函数

* 看下面的返回值
```
typeof [] // "object"
typeof new Date() // "object"
typeof new RegExp() // "object"
```

### instanceof
> instanceof 适用于检测引用类型的值

* 如果变量 variable 是引用类型 constructor 的实例，则返回 true
```
variable instanceof constructor;
```
* 看下面的返回值
```
[] instanceof Array // true
```

* 但如果不在同一个全局环境中执行，instanceof 就不能正常使用，因为不同的全局环境下的构造函数时不同的。看下面的例子：
```
// index.html 文件
<body>
    <iframe src="./inner.html"></iframe>
    <script>
    window.onload = function () {
        var arr = [1, 2, 3];
        var f = function() {}
        window.frames[0].func(arr, f);
    }
    console.log(window.Array === window.frames[0].Array) // false
    </script>
</body>

// inner.html
<body>
    <script>
        var func = function(arr, f) {
            console.log(arr instanceof Array) // false
            console.log(f instanceof Function) // false
        }
    </script>
</body>
```
1. 这里可以关于数组的判断可以使用 Array.isArray() 来判断。支持浏览器的方法有 IE9+、Firefox4+、Safari5+、Opera10.5+、chrome。
2. 从父级中传入的函数 f 无法判断。

### 最佳方案 
> Object.prototype.toString.call()
* 在任何值上调用 Object 原生的方法 toString() 方法，都会返回一个[object NativeConstructorName]格式的字符串。每个类在内部都有一个[[Class]]属性，这个属性中就指定了上述字符串中的构造函数名。
```
// 字符串
Object.prototype.toString.call('a') // "[object String]"
// 数字
Object.prototype.toString.call(1) // "[object Number]"
// Boolean
Object.prototype.toString.call(true) // "[object Boolean]"
// undefined 
Object.prototype.toString.call(undefined) // "[object Undefined]"
// null
Object.prototype.toString.call(null) // "[object Null]"
// Symbol
Object.prototype.toString.call(Symbol()) // "[object Symbol]"
// 数组
Object.prototype.toString.call([]) // "[object Array]"
// 对象
Object.prototype.toString.call({}) // "[object Object]"
// 函数
Object.prototype.toString.call(function() {}) // "[object Function]"
// RegExp
Object.prototype.toString.call(/a/) // "[object RegExp]"
// Date
Object.prototype.toString.call(new Date()) // "[object Date]"
// Map
Object.prototype.toString.call(new Map()) // "[object Map]"
// Set
Object.prototype.toString.call(new Set()) // "[object Set]"
// Math
Object.prototype.toString.call(Math) // "[object Math]"
// JSON
Object.prototype.toString.call(JSON) // "[object JSON]"
// Error
Object.prototype.toString.call(new Error()) // "[object Error]"

```
* 兼容性也很好：Edge12+、Firefox1+、Firefox for Android4+、及其他浏览器
