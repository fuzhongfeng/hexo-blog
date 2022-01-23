---
title: JavaScript-programming
layout: post
date: 2020-12-4 16:03:20
categories: JavaScript
tags: JavaScript
---

## 编程题
1. 将字符串 'abce-fgh-ijk' 转为小驼峰：'abceFghIjk'

a. 一次遍历
```
var s = 'abce-fgh-ijk';
var r = '';

for(let i = 0; i < s.length; i++) {
    if (s[i] === '-' && i !== 0) {
        // 关键点在于++i, 可以跳过一次循环
        r += s[++i].toLocaleUpperCase()
    } else {
      r += s[i]
    }
}

console.log(r) // abceFghIjk
```

b.正则
```
s.replace(/(-[A-Za-z]{1})/g, (match, p1) => { // match 表示匹配到的字符串（包含多个括号的内容），p1匹配第一个括号的内容。
    // 因为是全局匹配，所以此函数会执行多次，返回值会替换一次匹配到的字符串。
    return p1.toLocaleUpperCase().slice(1)
})
// abceFghIjk
```

2. 大数相加
// js 的最大安全数为 Number.MAX_SAFE_INTEGER / Math.pow(2, 53) - 1
```
var str1 = "9007199254740991";
var str2 = "1234567899999999999";

function sum(str1, str2) {
    var maxLen = Math.max(str1.length, str2.length);
    var newStr1 = str1.padStart(maxLen, 0);
    var newStr2 = str2.padStart(maxLen, 0);

    var result = ''
    var ten = 0;

    for (let i = maxLen - 1; i >= 0; i--) {
        var sn = Number(newStr1[i]) + Number(newStr2[i]) + ten;
        ten = Math.floor(sn / 10);
        result = sn % 10 + result;
    }

    if (ten === 1) {
        result = 1 + result
    }

    return result;
}

sum(str1, str2)
```

3. 版本号比较
```
var a = '1.0.45'
var b = '1.0.45.1'

function compare(a, b) {
    // 填充数组
    function padEnd(arr, len) {
        var dis = len - arr.length;
        
        while(dis-- > 0) {
            arr.push('0')
        }
    }

    var aArr = a.split('.');
    var bArr = b.split('.');
    // -1 代表小于，0 代表等于，1 代表大于
    var status = 0;
    
    var maxLen = Math.max(aArr.length, bArr.length)
    padEnd(aArr, maxLen)
    padEnd(bArr, maxLen)

    for (let i = 0; i < maxLen; i++) {
        if (Number(aArr[i]) > Number(bArr[i])) {
            return 1;
        } else if (Number(aArr[i]) < Number(bArr[i])) {
            return -1;
        }
    }

    return 0;
}
```

4. 实现 Person 类满足以下要求：
```
/**
 * new Person('Tony').sleep(10).eat('lunch')
 * // hi, I'm Tony
 * // wait for 10 sec
 * // eating lunch
 *
 * new Person('Tony').eat('lunch').sleep(10).eat('dinner')
 * // hi, i'm Tony
 * // eating lunch
 * // wait for 10 sec
 * // eating dinner
 *
 * new Person('Tony').eat('lunch').sleep(10).eat('dinner').sleepFirst(5)
 * // hi, i'm Tony
 * // sleep fisrt for 5 sec
 * // eating lunch
 * // wait for 10 sec
 * // eating dinner
 */
```
```
class Person {
    constructor(name) {
        console.log(`hi, i'm ${name}`)
        this.name = name;
        this.queue = [];
        this.runner();
    }

    runner() {
        Promise.resolve().then(async () => {
            while (this.queue.length > 0) {
                await this.queue.shift()()
            }
        })
    }

    sleepFirst(time) {
        this.queue.unshift(() => {
            return new Promise((resolve) => {
                setTimeout(() => {
                    console.log(`sleep first for ${5} sec`)
                    resolve()
                }, time * 1000)
            })
        })

        return this
    }

    sleep(time) {
        this.queue.push(() => {
            return new Promise((resolve) => {
                setTimeout(() => {
                    console.log(`wait for ${time} sec`)
                    resolve()
                }, time * 1000)
            })
        })

        return this
    }

    eat(thing) {
        this.queue.push(() => {
            return new Promise((resolve) => {
                console.log(`eating ${thing}`)
                resolve()
            })
        })
        return this
    }
}
```