---
title: 数据结构
layout: post
date: 2019-07-05 21:31:20
categories: Data Structure And Algorithm
tags: Data Structure And Algorithm
---

# 队列
> 概念：队列是一种线性结构，特点是在某一端添加数据，在另一端删除数据，遵循先进先出的原则。

## 实现方式
1. 单链队列
```
class Queue {
    constructor() {
        this.queue = []
    }
    enQueue(item) {
        this.queue.push(item)
    }
    deQueue() {
        return this.queue.shift()
    }
    getHeader() {
        return this.queue[0]
    }
    getLength() {
        return this.queue.length
    }
    isEmpty() {
        return this.getLength() === 0
    }
}
```
单链队列出的出队操作的时间复杂度为O(n), 然而循环队列的出队操作的时间复杂度为O(1)