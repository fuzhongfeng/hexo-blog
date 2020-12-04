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