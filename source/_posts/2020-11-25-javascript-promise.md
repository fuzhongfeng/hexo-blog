---
title: JavaScript-Promise
layout: post
date: 2020-11-25 20:09:16
categories: JavaScript
tags: JavaScript
---

# Promise

### Promise.prototype.catch 

1. 可以捕获 Promise 参数函数和 then 内的错误
```
new Promise((r, j) => {
    throw new Error('err!!!')
}).then(() => {
}).catch((e) => {
    console.log('catch', e) // 触发
})
// catch Error: err!!!

new Promise((r, j) => {
    r()
}).then(() => {
    throw new Error('err!!!')
}).catch((e) => {
    console.log('catch', e) // 触发
})
// catch Error: err!!!
```

2. 不能捕获 Promise 参数函数和 then 内的任何异步函数的错误
```
new Promise((r, j) => {
    setTimeout(() => {
       throw new Error('err!!!')
    }, 3000)
}).then(() => {
}).catch((e) => {
    console.log('catch', e) // 不触发
})
// 无打印，没有捕获到错误
```

3. 如果 then 没有 onReject 函数，则会触发catch。参数为reject传入的值或 throw 的值
```
new Promise((r, j) => {
    j('reject!!!')
}).then(() => {
}).catch((e) => {
    console.log('catch', e)
})
// catch reject!!!

new Promise((r, j) => {
    j('reject!!!')
}).then(() => {
}, (e) => {
  console.log('onReject', e)
}).catch((e) => {
    console.log('catch', e) // 不会执行到
})
// onReject reject!!!

new Promise((r, j) => {
    throw new Error('错了')
}).then(() => {}, (e) => { 
    console.log('reject', e) // 执行
}).catch((e) => {
    console.log('catch', e) // 不执行
})
// reject 错了
```

4. resolve 后的 throw Error 会被忽略
```
new Promise(function(r, j) {
  r();
  console.log('resolve after');
  throw 'resolve 后 throw';
}).catch(function(e) {
   console.log(e); // 不会执行
});
// resolve after
```

### 源码实现
https://www.jianshu.com/p/43de678e918a

### 练习
> 练习1
```
JS实现一个带并发限制的异步调度器Scheduler，保证同时运行的任务最多有两个。完善代码中Scheduler类，使得以下程序能正确输出
class Scheduler {
  add(promiseCreator) { ... }
  // ...
}

const timeout = (time) => new Promise(resolve => {
  setTimeout(resolve, time)
})

const scheduler = new Scheduler()

const addTask = (time, order) => {
  scheduler.add(
      () => timeout(time)
  ).then(() => console.log(order))
}

addTask(1000, '1')
addTask(500, '2')
addTask(300, '3')
addTask(400, '4')// output: 2 3 1 4
// 一开始，1、2两个任务进入队列
// 500ms时，2完成，输出2，任务3进队
// 800ms时，3完成，输出3，任务4进队
// 1000ms时，1完成，输出1
// 1200ms时，4完成，输出4

答案：
class Scheduler {
  constructor() {
    this.limit = 2
    this.list = []
    this.count = 0
  }

  add(promiseCreator) {
    return new Promise((resolve, reject) => {
      this.list.push([promiseCreator, resolve]);
      this.process();
    })
  }

  process() {
    if (this.count >= this.limit) return;

    if (this.list.length > 0) {
      var item = this.list.shift();

      item[0]().then(() => {
        this.count--;
        item[1]();

        this.process()
      })

      this.count++
    }
  }
}
```

> 练习2
```
async function async1() {
  console.log('async1 start');
  await async2();
  console.log('async1 end');
}

async function async2() {
  console.log('async2');
}

console.log('script start');

setTimeout(function() {
    console.log('setTimeout');
}, 0);  

async1();

new Promise(function(resolve) {
    console.log('promise1');
    resolve();
    console.log('promise1 resolve after')
    reject()
  }).then(function() {
    console.log('promise2');
}).catch((e) => {
    console.log('catch')
});

console.log('script end');

// script start
// async1 start
// async2
// promise1
// promise1 resolve after
// script end
// async1 end
// promise2
// setTimeout
```

## 实现一个 Promise.all
```
Promise.myAll = function(promiseArr) {
  return new Promise((resolve, reject) => {
    var result = [];
    var count = 0;

    promiseArr.forEach((p, i) => {
      if (Object.prototype.toString.call(p) !== '[object Promise]') {
        count++;
        result[i] = p
      } else {
        p.then((d) => {
          count++;
          result[i] = d

          if (count === promiseArr.length) {
            resolve(result)
          }
        }).catch((e) => {
          reject(e)
        })
      }
    })
  })
}

var promise1 = Promise.resolve(3);
var promise2 = 42;
var promise3 = new Promise((resolve, reject) => {
  setTimeout(resolve, 5000, 'foo');
});

Promise.myAll([promise1, promise2, promise3]).then((data) => {
  console.log(data)
})
```