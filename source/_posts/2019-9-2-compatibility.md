---
title: Browser- Compatibility
layout: post
date: 2018-05-06 11:07:50
categories: Compatibility
tags: Compatibility
---

## 1. 点击事件问题
### 移动端 click
由于手机端有些浏览器不存在 click 事件，可以在全局这样处理
#### 解决方法1(亲测有效)：
可以规避当click和touchstart同时存在时点击一次但触发两次问题（如 ipad），此方法可以解决大部分问题。
```
var clickEvent = 'click' in document.documentElement ? 'click' : 'touchstart';
```

### 事件委托问题：
此段代码在 ios12 系统下的 safari 和 webview 中显示无效
```
$(document).on(clickEvent, '.slide', function(e) {
  if (!shouldPlayNext(e)) {
    return
  }
  Hj_Slide.playNextStep()
})
```
原因：ios12 下利用 document 和 body 进行事件委托绑定 click 是无效的。

#### 解决办法2(亲测有效)：
* 将 click 事件委托到​​​​​非 document 或 body 的​​父级元素上
* 给​目标元素加一条样式规则 cursor: pointer;
* 将目标​元素换成 <a> 或者 button 等可点击的​元素;