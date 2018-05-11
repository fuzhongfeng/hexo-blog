---
title: JavaScript-基础2
layout: post
date: 2018-05-06 11:07:50
categories: JavaScript
tags: JavaScript
---

setTimeout 之前也有过很多次接触，这次统一梳理一下，其中关于`最小延迟`和`超时延迟`还是不太理解

## 1. setTimeout （参考：小红书 p203，[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/setTimeout#Reasons_for_delays_longer_than_specified)）
### setTimeout 的基本概念：
- setTimeout 方法是 window 对象下的。
- JavaScript 是一个单线程序的解释器，因此一定时间内只能执行一段代码。为了控制要执行的代码，就有一个 JavaScript 任务队列。这些任务会按照将它们添加到队列的顺序执行。setTimeout()的第二个参数告诉 JavaScript 再过多长时间把当前任务添加到队列中。如果队列是空的，那么添加的代码会立即执行；如果队列不是空的，那么它就要等前面的代码执行完了以后再执行。
- 接收多个参数（function, delay, param1...paramN）这里的 parma 会在 delay 延迟后自动作为参数传入 function里，但在 IE9 及以下不支持这种写法。

        setTimeout(function(p1, p2) {
        	console.log(p1, p2); // param1 param2
        }, 1000, "param1", "param2");
        
- setTimeout()会返回一个表示超时调用的数值ID。这个超时调用 ID 是计划执行代码的唯一标识符，可以通过它来取消超时调用（clearTimeout()）。
- 超时延迟

        function foo() {
          console.log('foo has been called');
        }
        setTimeout(foo, 0);
        console.log('After setTimeout');
        // After setTimeout, foo has been called
        出现这个结果的原因是，尽管hsetTimeout 以0ms的延迟来调用函数，但这个任务已经被放入了队列中并且等待下一次执行；并不是立即执行；队列中的等待函数被调用之前，当前代码必须全部运行完毕，因此这里运行结果并非预想的那样。
- 最小延迟

 最小延时 >=4ms（MDN说法），但是看下面的例子却是 2ms 甚至不到 2ms ？？？
        
        // 转换为时间戳看一下
        console.log(new Date().getTime());
        setTimeout(function() {
        	console.log(new Date().getTime());
        }, 0);
        
        // 1525534082199, 1525534082201
        
        //  使用 console.time() 和 console.timeEnd() 来统计时间
        function testTimeout() {
            //  启动任务计时器
            console.time("Timeout")
            //  启动浏览器定时任务 
            setTimeout(function() {
                //  统计任务消耗的时间
                console.timeEnd("Timeout")
            }, 0)
        }
        testTimeout(); 
        // Timeout: 1.34130859375ms
- 最大延时值
浏览器包括 IE, , Chrome, Safari, Firefox 以32个bit字节存储整数。这就会导致如果一个整数大于 2147483647 (大约24.8 天)时就会溢出，导致定时器将会被立即执行

### 看下面的例子：

        for (var i = 0; i < 10; i++) {
           setTimeout(function(){
               console.log(new Date, i);
           }, 1000) 
        }
        console.log(new Date, i);
打印结果：
* last Sat May 05 2018 21:39:42 GMT+0800 (CST) 10 //  setTimeout 由于添加到队列的时间较晚（delay），所以会等到队列中其他任务结束之后再执行。
* last Sat May 05 2018 21:39:43 GMT+0800 (CST) 10 // 十次,注意这里不是重复打印而是分别打印共十次， for 循环里面的 setTimeout 只是在 delay 时间后将其添加到队列中，所以 setTimeout 并不会阻碍 for 循环的时间

#### 下面是转成时间戳的打印：

        for (var i = 0; i < 10; i++) {
           setTimeout(function(){
               console.log(new Date().getTime(), i);
           }, 1000) 
        }
        console.log("last", new Date().getTime(), i);
        
    
打印结果：
* last 1525527917027 10
* (3)1525527918030 10
* (3)1525527918031 10
* (4)1525527918032 10

以上结果中的重复打印次数不是固定的，在执行一次也可能是：2，2，6；
