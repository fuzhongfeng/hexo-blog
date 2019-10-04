---
title: 算法题目
layout: post
date: 2018-05-19 17:27:53
categories: Data Structure And Algorithm
tags: Data Structure And Algorithm
---

## 两数之和

1. 给定一个整数数组和一个目标值，找出数组中和为目标值的两个数的做索引组成的数组。你可以假设每个输入只对应一种答案，且同样的元素不能被重复利用。例子如下：

```
给定 nums = [2, 7, 11, 15], target = 9
因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]    
```
答案：
```
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var twoSum = function(nums, target) {
    let arr = [];
    for (let i = 0; i < nums.length; i++) {
        for (let j = 0; j < nums.length; j++) {
            if ((nums[i] + nums[j] === target) && (i !== j)) {
                arr = [j, i];
            }
        }
    }
    return arr;
};
```