---
title: 算法题目
layout: post
date: 2018-05-19 17:27:53
categories: Data Structure And Algorithm
tags: Data Structure And Algorithm
---

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

2. 实现数组乱序算法，要求每个数字出现在每个位置的概率是平均的。
* 验证 random 方法是否完全随机
```
const test = (random) => {
  let n = 100000; 
  // 保存每个元素在每个位置上出现的次数
  const countArr = Array.from({length:10}).fill(0)
  for (let i = 0; i < n; i ++) {
      let arr = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j'];
      random(arr);
      countArr[arr.indexOf('a')]++;
  }
  console.log(countArr);
}
```
> 使用 sort 的简单实现并不能真正的随机，并且时间复杂度为O(n^2)，不能随机的原因在于两个元素之间比较次数不够。
```
// Math.random() [0, 1)
const random = (arr) => arr.sort((a, b) => Math.random() - 0.5)
test(random) // [19361, 13712, 8985, 7444, 6726, 7530, 8240, 8483, 9280, 10239] 可见并不随机
```

> 改写 sort 方法
```
const random = (arr) => {
    let newArr = arr.map(n => ({v: n, r: Math.random()}));
    newArr.sort((a, b) => a.r - b.r);
    arr.splice(0,arr.length,...newArr.map(o => o.v));
    return arr;
};
test(random) // [10052, 10066, 9959, 9912, 10040, 9995, 9996, 10088, 9903, 9989] 可实现随机
```

> 通过洗牌算法实现，复杂度为 O(n)。强烈推荐！！！
```
const random = (arr) => {
    let len = arr.length
    while(len > 1) {
        let index = Math.floor(Math.random() * len--);
        [arr[len], arr[index]] = [arr[index], arr[len]];
    }
    return arr
}
test(random) // [10135, 9962, 10090, 10051, 9963, 9781, 10063, 10053, 10038, 9864] 
```