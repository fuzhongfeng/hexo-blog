---
title: Front-end-base
layout: post
date: 2019-1-12 22:06:13
categories: Front-end
tags: Front-end
---

## HTTP，HTML和浏览器

- DOM 事件类型和事件流
- 浏览器的缓存，cookie，sessionStorage，localStorage
- 浏览器的单线程机制
- HTML5的某个API，例如：drag，mouse

## CSS

- 盒模型
- 常用布局方式
- BFC
- 针对某一个具体的 CSS 样式，问具体的使用方法和使用场景。例如：border 这个样式，值的具体类型，值为百分比和值为数字的区别，具体的使用场景，和 ouline 的区别等。

### JavaScript

> 原型与原型链
    **原型**：每一个构造函数都有一个prototype属性，指向另一个对象。这个对象的所有属性和方法，都会被构造函数的实例继承。
    **原型链**：访问一个对象的属性时，先在其基本属性中查找，如果没有再沿着__proto__这条链向上找，这就是原型链。
    ```javascript
    Object.prototype.__proto__ === null
    ```
------

- 作用域与闭包(使用场景)

> js实现继承的方式

    (1)**原型链**：利用原型让一个引用类型继承另一个引用类型的属性和方法（引用类型的原型赋值为另一个构造函数的实例）。 当以读取模式访问一个实例属性时，首先会在实例中搜索该属性。如果没有找到该属性，则会继续搜索实例的原型。在
    通过原型链实现继承的情况下，搜索过程就得以沿着原型链继续向上。
    (2)**借用构造函数**：在子类型构造函数的内部调用超类型的构造函数。
    缺点：方法都在构造函数中定义，因此函数复用就无从谈起了
    ```javascript
    function SuperType(){
        this.colors = ["red", "blue", "green"];
    }
    function SubType(){ //继承了 SuperType
        SuperType.call(this);
    }
    var instance1 = new SubType();
    instance1.colors.push("black");
    alert(instance1.colors); //"red,blue,green,black"
    var instance2 = new SubType();
    alert(instance2.colors); //"red,blue,green"
    ```
    (3)**组合继承**：使用原型链实现对原型属性和方法的继承，而通过借用构造函数来实现对实例属性的继承 
------
    
- 异步回调
- ES6/7
- 跨域
- 前端模块化
> Event Loop
 1. 所有同步任都在主线程上执行，形成一个执行栈
 2. 只要异步任务有了运行结果，就在任务队列中放置一个事件。
 3. 一旦执行栈中的所有同步任务执行完毕，就会读取任务队列，异步任务进入执行栈开始执行
 4. 主线程不断重复上面的第 3 步

```
// 会在 for 循环执行结束之后打印 console，可见只有执行栈中的同步任务执行完毕才会去读取任务队列中的异步任务
setTimeout(() => {
  console.log('完毕！')
}, 0)
console.time('for')
for (let i = 0; i < 10000000000; i++) { // 执行时间约为 11s 
}
console.timeEnd('for')
```  

 除了广义的同步和异步任务，异步任务可以分为以下两类: 
 * macro-task（宏任务）：script, setTimeout, setInterval, requestAnimationFrame, I/O, UI rendering
 * micro-task(微任务)：Promise.then, process.nextTick, MutationObserver
 ------

> 构造函数
    **new操作符**：
    (1)创建一个新对象
    (2)将构造函数的作用域赋值给新对象（因此this指向新对象）
    (3)执行构造函数中的代码（为这个新对象添加属性和方法）
    (4)返回新对象
    **构造函数原型上的方法和属性会出现在实例的 __proto__ 上**
------

## Dom
### 1. 浏览器的回流和重绘，如何进行优化？
#### 回流：
> 当 Render Tree 中部分或全部元素的尺寸、结构、或某些属性发生改变时，浏览器重新渲染部分或全部文档的过程称为回流。回流比重绘的代价要更高。
* 页面首次渲染
* 浏览器窗口大小发生改变
* 元素尺寸或位置发生改变
* 元素内容变化（文字数量或图片大小等等）
* 元素字体大小变化
* 添加或者删除可见的DOM元素
* 激活CSS伪类（例如：:hover）
* 查询某些属性或调用某些方法
一些常用且会导致回流的属性和方法:
* clientWidth、clientHeight、clientTop、clientLeft
* offsetWidth、offsetHeight、offsetTop、offsetLeft
* scrollWidth、scrollHeight、scrollTop、scrollLeft
* scrollIntoView()、scrollIntoViewIfNeeded()
* getComputedStyle()
* getBoundingClientRect()
* scrollTo()

#### 重绘：
>当页面中元素的样式的改变不影响它在页面中的位置时，（如：color，background-color，visibility等），浏览器会将新样式赋予并重新绘制它，这个过程叫做重绘。


#### 如何避免

css:
* 避免使用table布局。
* 尽可能在DOM树的最末端改变class。
* 避免设置多层内联样式。
* 将动画效果应用到position属性为absolute或fixed的元素上。
* 避免使用CSS表达式（例如：calc()）。

js:
* 避免频繁操作样式，最好一次性重写style属性，或者将样式列表定义为class并一次性更改class属性。
* 避免频繁操作DOM，创建一个documentFragment，在它上面应用所有DOM操作，最后再把它添加到文档中。
* 也可以先为元素设置display: none，操作结束后再把它显示出来。因为在display属性为none的元素上进行的DOM操作不会引发回流和重绘。
* 避免频繁读取会引发回流/重绘的属性，如果确实需要多次使用，就用一个变量缓存起来。
* 对具有复杂动画的元素使用绝对定位，使它脱离文档流，否则会引起父元素及后续元素频繁回流。


## 前端框架(React/Vue)
- Virtual Dom 
- 组件实例生命周期
- 组件通信
- React 或 Vue 某个 API 的使用要点，比如：setState，componentDidMount 和 componentDidUpdate 的使用场景和异同，portal

## 其它
- git、webpack、babel 使用
- 前端性能优化与安全性
- 基础算法知识
- http/s协议