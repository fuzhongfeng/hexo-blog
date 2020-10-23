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
单链队列的出队操作的时间复杂度为O(n), 然而循环队列的出队操作的时间复杂度为O(1)

# 树
> 树里的每一个节点有一个值和一个包含所有子节点的列表。从图的观点来看，树也可视为一个拥有 N 个节点和 N-1 条边的一个有向无环图。

## 二叉树
> 是一种更为典型的树状结构。如它名字所描述的那样，二叉树是每个节点最多有两个子树的树结构，通常子树被称作“左子树”和“右子树”。

### 遍历
前中后序都是只根节点的顺序

* 前序遍历
> 前序遍历首先访问根节点，然后遍历左子树，最后遍历右子树。

* 中序遍历
> 中序遍历是先遍历左子树，然后访问根节点，然后遍历右子树。

* 后序遍历
> 后序遍历是先遍历左子树，然后遍历右子树，最后访问树的根节点。
