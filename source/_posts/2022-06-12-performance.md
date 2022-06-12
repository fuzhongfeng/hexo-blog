---
title: 性能优化
layout: post
date: 2022-6-12 10:34:00
categories: Performance
tags: Performance
---

# chrome performance 
## performance.timing
![1](/images/timing.webp)
### 白屏
白屏时间 = 地址栏输入网址后回车 - 浏览器出现第一个元素
```
// 一般白屏结束时间定义为body渲染前，此script标签放在body前
<script>
    window.whiteEnd = Date.now()
    // 白屏时间
    console.log(whiteEnd - performance.timing.navigationStart)
</script>
```

## 判断网页内存溢出
问题：做课件撤销重做功能时，用两个数组存储页面的数据，上线一段时间后接到用户反馈页面崩溃的问题。初步猜测是内存溢出，那么如何验证是否为内存溢出呢？
验证方法：Chrome -- More tools -- Performance monitor -- Js heap size 
```
// 控制台手动模拟内存增加，查看 Js heap size 大小
var arr = []
for(let i = 0; i < 100000000; i++) {
    arr.push(i)
}
```
