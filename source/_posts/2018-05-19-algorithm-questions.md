---
title: 算法题目
layout: post
date: 2018-05-19 17:27:53
categories: Data Structure And Algorithm
tags: Data Structure And Algorithm
---


## 1. 排序算法
1. 冒泡排序: 双层循环，两两比较交换位置。复杂度 O(n^2)
```
const bubbleSort = (arr) => {
  const len = arr.length;
  for (let i = 0; i < len; i ++) {
    for (let j = 0; j < len - 1 - i; j ++) { // 内循环每一轮结束后最后一个数为最大，此处可以减少多余的比较
      if (arr[j] > arr[j + 1]) {
        [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]]
      }
    }
  }
}
```

2. 快速排序: 时间复杂度 O(nlogn)，空间复杂度 O(nlogn)
```
  const quick = function(array, left, right) {
    if (array.length <= 1) return;

    // 循环一次后将数组分为两部分，但不同冒泡无法保证任何一个元素的准确位置。
    let index = partition(array, left, right);

    // 因为 partition 函数中 left > right，所以 left 到 index - 1 确定一个子数组
    if (left < index - 1) {
      quick(array, left, index - 1);
    }

    if (index < right) {
      quick(array, index, right);
    }
  };

  // 分区
  const partition = function(array, left, right) {
    const pivot = array[Math.floor((left + right) / 2)];

    while (left <= right) {
      while (array[left] < pivot) {
        left++;
      }
      while (array[right] > pivot) {
        right--;
      }

      if (left <= right) {
        swap(array, left, right);

        left++;
        right--;
      }
    }
    return left;
  };

  const swap = (arr, i, j) => {
    [arr[i], arr[j]] = [arr[j], arr[i]];
  }

  // quick(array, 0, array.length - 1);
```

// 快速排序的另一种写法
```
function qsort(arr, low, high) {
    if (low < high) {
        var mid = partition(arr, low, high);
        qsort(arr, low, mid - 1);
        qsort(arr, mid + 1, high);
    } 
}

function partition(arr, low, high) {
    var i = low - 1;
    var j = high;
    var pivot = arr[high];    

    while(1) {
        while(arr[++i] < pivot);
        while(arr[--j] > pivot);
        if (i < j) {
            swap(arr, i, j)
        } else {
            break;
        }
    }

    swap(arr, i, high)

    return i;
}

function swap(arr, i, j) {
    [arr[i], arr[j]] = [arr[j], arr[i]]
}
```

3. 堆排序, O(nlogn)
```
  function heapSort(array) {
    var heapSize = array.length;

    buildHeap(array);
    while (heapSize > 1) {
      heapSize--;
      swap(array, 0, heapSize);
      heapify(array, heapSize, 0);
    }
  };

  function buildHeap(array) {
    var heapSize = array.length;
    for (var i = Math.floor(array.length / 2); i >= 0; i--) {
      heapify(array, heapSize, i);
    }
  };

  function heapify(array, heapSize, i) {
    var left = i * 2 + 1,
      right = i * 2 + 2,
      largest = i;
    if (left < heapSize && array[left] > array[largest]) {
      largest = left;
    }
    if (right < heapSize && array[right] > array[largest]) {
      largest = right;
    }
    if (largest !== i) {
      swap(array, i, largest);
      heapify(array, heapSize, largest);
    }
  }

  function swap(arr, i, j) {
    [arr[i], arr[j]] = [arr[j], arr[i]];
  }
```

## 2. 给定一个整数数组和一个目标值，找出数组中和为目标值的两个数的做索引组成的数组。你可以假设每个输入只对应一种答案，且同样的元素不能被重复利用。例子如下：

```
给定 nums = [2, 7, 11, 15], target = 9
因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]    
```
答案：
```
// 时间复杂度 O(1)
var twoSum = function(nums, target) {
    const map = new Map();
    let arr = []
    for (let i = nums.length - 1; i >= 0; i--) {
        const j = map.get(target - nums[i]);
        if (j !== undefined) {
          arr = [i, j];
          break;
        }
        map.set(nums[i], i);
    }
    return arr
};
```

## 3. 实现数组乱序算法，要求每个数字出现在每个位置的概率是平均的。
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

## 4. 一维数组转树形结构
* 实现无限制层级的文件夹菜单树，数据库返回的一维数组结构如下：
```
// 顺序不确定
const arr = [
  {"id":"1", "pId":"0", "name":"节点1 - 展开", "open":true},
  {"id":"1-1", "pId":"1", "name":"节点1-1 - 折叠"},
  {"id":"1-2", "pId":"1", "name":"节点1-2 - 折叠"},
  {"id":"1-3", "pId":"1", "name":"节点1-3 - 折叠", "open":true},
  {"id":"1-3-1", "pId":"1-3", "name":"节点1-3-1 - 没有子节点"},
  {"id":"2", "pId":"0", "name":"节点2 - 折叠"},
  {"id":"2-1", "pId":"2", "name":"节点2-1 - 展开", "open":true},
  {"id":"2-2", "pId":"2", "name":"节点2-2 - 折叠"},
  {"id":"2-3", "pId":"2", "name":"节点2-3 - 折叠"},
  {"id":"3", "pId":"0", "name":"节点3 - 没有子节点"}
];
```

* 需要生成如下树转结构便于进行展示：
```
const data = [
  {
    "id": "1",
    "pId": "0",
    "name": "节点1 - 展开",
    "open": true,
    "children": [{
      "id": "1-1",
      "pId": "1",
      "name": "节点1-1 - 折叠",
      "children": []
    },
    {
      "id": "1-2",
      "pId": "1",
      "name": "节点1-2 - 折叠",
      "children": []
    },
    {
      "id": "1-3",
      "pId": "1",
      "name": "节点1-3 - 折叠",
      "open": true,
      "children": [{
        "id": "1-3-1",
        "pId": "1-3",
        "name": "节点1-3-1 - 没有子节点",
        "children": []
      }]
    }
    ]
  },
  {
    "id": "2",
    "pId": "0",
    "name": "节点2 - 折叠",
    "children": [{
      "id": "2-1",
      "pId": "2",
      "name": "节点2-1 - 展开",
      "open": true,
      "children": []
    },
    {
      "id": "2-2",
      "pId": "2",
      "name": "节点2-2 - 折叠",
      "children": []
    },
    {
      "id": "2-3",
      "pId": "2",
      "name": "节点2-3 - 折叠",
      "children": []
    }
    ]
  },
  {
    "id": "3",
    "pId": "0",
    "name": "节点3 - 没有子节点",
    "children": []
  }
]
```

* 实现方式如下：
/**
 * 可重命名字段
 */
```
const getTree = (data, rootId, id = 'id', pId = 'pId', children = 'children') => {
    const getNode = (p_id) => {
        const node = [];
        for (let i = 0; i < data.length; i++) {
            if (data[i][pId] === p_id) {
                data[i][children] = getNode(data[i][id]);
                node.push(data[i]);
            }
        }
        return node;
    }
    return getNode(rootId)
};
getTree(data, '0');
```

## 扑克牌顺序问题：
有一堆扑克牌, 将牌堆第一张放到桌子上，再将牌堆的第一张放到牌底，如此往复；最后桌子上的牌顺序为：（牌底）1,2,3,4,5,6,7,8,9,10,11,12,13(牌顶)；
问：原来那堆牌的顺序，用函数实现。
```
// 分析与抽象：
// 1. 最重要的想清楚这件事：每次将牌从牌头放到牌底和桌子上的这一系列操作是可逆的！
// 如桌子上已经有了一张牌、那我一共做了两个操作即：a.将牌从牌头放到牌底 b. 将牌从牌头放到桌子上
// 逆向还原时先执行b，再执行a即可还原为最初状态。多张牌还原相当于还原多次逆向操作。
// 还原函数即为手拿扑克牌还原的过程。
// 2. 牌堆可以看成数组，牌底对应数组尾部，牌顶对应数组头部。
// 3. 最后牌堆抽象为数组为: var final = [13,12,11,10,9,8,7,6,5,4,3,2,1]
// 4. 原来牌堆抽象可以抽象为一个新数组：var initial = []

  var final = [13,12,11,10,9,8,7,6,5,4,3,2,1];
  function reverse(arr) {
    var result = [];
    for (var i = 0; i < arr.length; i++) {
      result.length > 0 && result.unshift(result.pop());
      result.unshift(arr[i]);
    }
    
    return result;
  }

  console.log(reverse(final)) // [1, 12, 2, 8, 3, 11, 4, 9, 5, 13, 6, 10, 7]
```
