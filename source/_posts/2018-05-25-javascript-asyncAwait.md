---
title: JavaScript-async/await
layout: post
date: 2018-05-24 11:02:18
categories: JavaScript
tags: JavaScript
---
# async/await 双刃剑

## async/await：继续 promise、Generator 之后 es7 提出的异步操作的解决方案

### 我们之前写回调是这样的

        // 通常写法
        a(() => {
            b(() => {
                c()
            })
        })

        // promise
        new Promise(function (resolve, reject) {
            resolve()
        }).then(function () {
            return new Promise(function (resolve, reject) {
                resolve()
            });
        }).then(function () {
        });

### async/await 写法

        // 是不是很清新
        (async () => {
            await a()
            await b()
            await c()
        })()

### async/await 描述
- 当调用一个 async 函数时，会返回一个 Promise 对象。当这个 async 函数返回一个值时，Promise 的 resolve 方法会负责传递这个值；当 async 函数抛出异常时，Promise 的 reject 方法也会传递这个异常值。（可以用.then 接收 return 值 或 遇到 reject直接用 .catch 抓取）

- async 函数中可能会有 await 表达式，这会使 async 函数暂停执行，等待表达式中的 Promise 解析完成后继续执行 async 函数并返回解决结果

### 一些优点：
- 使用Async/Await明显节约了不少代码。我们不需要写.then，不需要写匿名函数处理 Promise 的 resolve值，也不需要定义多余的 data 变量，还避免了嵌套代码。这些小的优点会迅速累计起来，这在之后的代码示例中会更加明显
- Generator 函数的执行必须靠执行器，所以才有了co 模块，而async函数自带执行器。也就是说，async函数的执行，与普通函数一模一样，只要一行。
- 更好的语义，更广的适用性，await 命令后面，可以跟 Promise 对象和原始类型的值（数值、字符串和布尔值，但这时等同于同步操作）

## 一些使用中的问题：

### 下面是随处可见的前端代码

        // 先写ABCD4个列表请求

        function getAList() {
            return new Promise((resolve) => {
                setTimeout(() => {
                    resolve()
                }, 3000)
            }).then(() => {
                console.log("getAList")
            })
        }

        function getBList() {
            return new Promise((resolve) => {
                setTimeout(() => {
                    resolve()
                }, 3000)
            }).then(() => {
                console.log("getBList")
            })
        }

        function getCList() {
            return new Promise((resolve) => {
                setTimeout(() => {
                    resolve()
                }, 3000)
            }).then(() => {
                console.log("getCList")
            })
        }

        function getDList() {
            return new Promise((resolve) => {
                setTimeout(() => {
                resolve()
                }, 3000)
            }).then(() => {
                console.log("getDList")
            })
        }

        // 一个页面发送 getAList getBList 两个请求之后进行的渲染
        (async () => {
            console.time("Time")
            await getAList()
            await getBList()
            console.log("finish")
            console.timeEnd("Time")
        })()
        // 用时 6ms

- 当 getAList 和 getBList 之间没有依赖的时候顺序的 await 会最多让执行时间增加一倍的 getAList 函数时间（如果 getBList 的执行时间大于 getAList 的执行时间）,因为 getAList 与 getBList 应该并行执行。


### 正确的做法应该是先同时执行函数，再 await 返回值，这样可以并行执行异步函数:

        (async () => {
            console.time("Time")
            const getAListResult = getAList()
            const getBListResult = getBList()
            await getAListResult
            await getBListResult
            console.log("finish")
            console.timeEnd("Time")
        })()
        // 用时 3ms


        // Promise.all 写法并发执行多个 promise
        (async () => {
            console.time("Time")
            Promise.all([getAList(), getBList()]).then(() => {
            console.log("finish")
            console.timeEnd("Time")
            })
        })()
        // 用时 3ms

## *看来不要随意的 await，它很可能让你的代码性能降低！！！*


### async/await 语法糖只能实现一部分回调支持的功能，也就是仅能方便应对层层嵌套的场景。其他场景，就要动一些脑子了，比如两对回调：

        a(() => {
            b()
        })

        c(() => {
            d()
        })

#### 1. 如果一不留神写成下面的方式，虽然一定能保证功能实现，但却是最低效的执行方式：

        await a();
        await b();
        await c();
        await d();

        // 因为翻译成回调，就变成了下面这样，async/await 会让我们倾向于在 b 执行完后，再执行 c
        a(() => {
            b(() => {
                c(() => {
                    d()
                });
            });
        });

#### 2. 我们想到上面的例子，可以优化一下性能：

        const resA = a();
        const resC = c();
        await resA;
        b();
        await resC;
        d();

        // 但是，d 原本只要等待 c 执行完，现在如果 a 执行时间比 c 长，就变成了这样：
        a(() => {
            d();
        });

#### 3. 看来只有完全隔离成两个函数：

        (async () => {
            await a();
            b();
        })()

        (async () => {
            await c();
            d();
        })();

#### 4. 或者利用 Promise.all
        const ab = async () => {
            await getAList();
            getBList();
        }
        const cd = async () => {
            await getCList();
            getDList();
        }
        Promise.all([ab(), cd()])

### 现在我们回头来看一下原来两对的回调函数：
        a(() => {
            b()
        })

        c(() => {
            d()
        })

### 现在来看想使用好 async/await 真的会那么简单吗？回调方式这么简单的过程式代码，换成 async/await 居然写完还要反思一下，再反推着去优化性能，先不考虑写法上的繁琐与否如果一不留神影响性能这简直比回调还要可怕。希望本次分享能让大家在使用 async/await 的时候多一些思考，谢谢大家
