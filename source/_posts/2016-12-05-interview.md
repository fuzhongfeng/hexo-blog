---
title: JavaScript-code
layout: post
date: 2020-10-13 9:18:23
categories: JavaScript
tags: JavaScript
---

## 代码执行结果
1.
```
var a = 1
var obj = {
	a: 2,
	fn: () => {
		console.log(this.a)
	}
}
// 1
```

2.
```
var a = {x: 1};
var b = a;
a.x = a = {n: 1};
console.log(a) // {n: 1}
console.log(b) // {x: {n: 1}}
// 解析：1. 运算符优先级为19先执行，此时b保持着和a的相同引用{x: 1}。
// 2. 赋值运算符从右到左，先执行 a = {n: 1}， 此时a被重新赋值了，不再引用{x: 1}
// 3. a.x 为原来{x: 1}中的x，所以b.x会变为{n: 1}
```

3. 
```
Function.prototype.a = () => alert(1);
Object.prototype.b = () => alert(2);
function A() {};
const a = new A();
a.a() // Uncaught TypeError: a.a is not a function
a.b() // 2
```

4. 
```
let a = 0;
console.log(a); // 0
console.log(b); // VM2529:3 Uncaught ReferenceError: Cannot access 'b' before initialization
let b = 0;
console.log(c); // 此处不会打印 ƒ c() {} 因为上一步已经报错了，阻塞了后面的代码执行
function c() {};
```

5. 
```
var x = 10;
function a (y) {
	var x = 20;
	return b(y)
}

function b (y) {
	return x + y;
}

a(20) // 30
```

6. 
```
typeof typeof typeof [] // "string"
```

7. 
```
[1,2,3,4,5].map(parseInt)
// [1,NaN,NaN,NaN,NaN]
```

8. 
```
console.log(1)

setTimeout(() => {
    console.log(2)
})

process.nextTick(() => {
    console.log(3)
})

setImmediate(() => {
    console.log(4)
})

new Promise((resolve) => {
    console.log(5)
    resolve()
    console.log(6)
}).then(() => {
    console.log(7)
})

Promise.resolve().then(() => {
    console.log(8)
    process.nextTick(() => {
        console.log(9)
    })
})
// 1 5 6 3 7 8 9 2 4
```

9. 
```
typeof new Number(123) // "object"
new Number(123) instanceof Number // true
var a = 123
a instanceof Number // false
Object.prototype.toString.call(a) // "[object Number]"
```

## 编程题
1. 代码输出结果及为什么？如果要每隔1s输出结果，如何改造？（不可改动square方法）
```
const list = [1,2,3]
const square = num => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(num * num)
        }, 1000)
    })
}

function test() {
    list.forEach(async x => {
        const res = await square(x)
        console.log(res)
    })
}
test()

// 答案1:
async function test() {
    for(let i = 0; i < list.length; i++){
        const res = await square(list[i])
        console.log(res)
    }
}
test()
```

2. 实现一个 normalize 函数，能将输入的特定字符串转化为特定的结构化数据，字符串仅由小写字母和[]组成，且字符串不会包含多余的空格。
示例一：'abc' --> {value: 'abc'}
示例二：'[abc[bcd[def]]]' --> {value: 'abc', children: {value: 'bcd', children: {value: 'def'}}}
```
function normalize(str) {
    var arr = str.match(/\w+/g)
    var obj = {}
    var cur = obj // 利用引用添加嵌套结构
    var key = null

    while(key = arr.shift()) {
        cur.value = key
        if (arr.length === 0) break
        cur.children = {}
        cur = cur.children // 当前引用终断，赋值为 children 引用。
    }

    return obj
}

console.log(normalize('[abc[bcd[def]]]'))
```