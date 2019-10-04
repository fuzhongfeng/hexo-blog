---
title: JavaScript-节流和防抖
layout: post
date: 2019-10-01 10:02:09
categories: JavaScript
tags: JavaScript
---

## 防抖
1. 延迟执行的防抖
* 如在搜索时希望用户输入结束后再进行接口调用，也就是在一连串时间间隔小于指定时间的函数调用后触发一次。

* 实现：
```
var debounce = function(func, wait = 100) {
	var timer = null
	
	return function(...args) {
		timer &&  clearTimeout(timer)
        timer = setTimeout(function() {
            func.apply(this, args)
        }, wait)
	}
}
// 使用
function test() {
	console.log(111)
}
var f = debounce(test, 500)
for(var i = 0; i < 10; i++) {
	f()
}
// 111（只输出一次）
```

2. 立即执行的防抖
* 我不希望非要等到事件停止触发后才执行，我希望立刻执行函数，然后等到停止触发 n 秒后，才可以重新触发执行。
如：点击一个保存按钮时，希望第一次就可以发送请求即立即执行更优，相比于延迟执行体验更好。
```
var debounce = function(func, wait, immediate) {
	var timer = null

	return function (...args) {
        timer &&  clearTimeout(timer)
		if(immediate) {
            var callNow = !timer
			timer = setTimeout(() => {
                timer = null
            }, wait)
            callNow && func.apply(this, args)
		} else {
			timer = setTimeout(() => {
                func.apply(this, args)
			}, wait)
        }
	}
}
// 使用
function test() {
	console.log(111)
}
var f = debounce(test, 2000, true)
```