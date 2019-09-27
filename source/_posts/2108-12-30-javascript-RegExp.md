---
title: JavaScript-RegExp
layout: post
date: 2018-12-30 19:55:01
categories: JavaScript
tags: JavaScript
---

虽然把正则表达式划分到了 JavaScript 里但要明确的一点是，正则在多种语言中都是通用的，js 仅仅是通过 RegExp 类型来支持正则表达式。对于正则表达式刚接触的时候会自己手写一些，但当遇到一些通用的需求（如手机号、邮箱等）之后就会习惯于直接套用网上现成的正则表达式，但是使用过程中最近感受到了正则的强大和自己会写正则的重要之处，下面举一个实际场景

## 1. 需求场景：
- 判断如下 html 字符串中是否含有“答案”按钮，标准是找到 div 标签上有 answer-btn class 同时此 div 的子元素 div 含有 x9 的子类：
```
var str = '<div class="a0 answer-btn" style="width:94px;height:53px;z-index:2;" data-uid="096c171c6f4647fcb6b27d61332cd55d" data-control-btn="{&quot;event&quot;:&quot;click&quot;,&quot;type&quot;:&quot;show&quot;,&quot;target&quot;:[[&quot;1e8d5b4003fa4273966813c8a6fd43a2&quot;]]}"><div class="ub dh w3 ds eo x9" style=""><span>答案</span><span class="xb3" style=""></span></div></div>'
```

## 2.思路：
- 乍一想可以用 includes 方法实现
```
    if (str.includes('answer-btn') && str.includes('x9')) {
        // 执行操作
    }
```
但明显逻辑不严谨，如果字符串的复杂性足够大，那个包含"answer-btn" 和 "x9"但有不是 class 的情况很容易出现
- 此时最优方案明显是正则表达式
```
    
```

## 正则表达式
  1. 正则表达式的匹配模式支持下列 3 个标志：
  g：表示全局（global）模式，即模式将被应用于所有字符串，而非在发现第一个匹配项时立即停止
  i：表示不区分大小写（case-insensitive）模式，即在确定匹配项时忽略模式与字符串的大小写
  m：表示多行（multiline）模式，即在到达一行文本末尾时还会继续查找下一行中是否存在与模式匹配的项。

  2. 模式中所有元字符都必须转义，正则中的元字符：
  ```
  ( [ { \ ^ $ | ) ? * + . ] }
  ```
  3. 特殊字符
    所谓特殊字符就是一些有特殊含义的字符
    - ?  匹配前面的字符串零次或者一次，或指明一个非贪婪限定符。要匹配 ? 字符串请使用 \?

    - (?:pattern) 匹配 pattern 但不获取匹配结果，也就是说这是一个非获取匹配，不进行存储供以后使用。这在使用'或'字符(|)来组合一个模式的各个部分是很有用的。例如 industr(?:y|ies) 就是一个比 'industry|industries' 更简洁的表达式。

    - .(小数点）匹配除换行符之外的任何单个字符。
    例如，/.n/将会匹配 "nay, an apple is on the tree" 中的 'an' 和 'on'，但是不会匹配 'nay'。

    * 匹配前面的一个表达式0次或者多次，等价于{0,}
    例如，/bo*/会匹配 "A ghost boooooed" 中的 'booooo' 和 "A bird warbled" 中的 'b'，但是在 "A goat grunted" 中将不会匹配任何东西

### 常用正则
1. 将 17346547310 替换为 173****7310
```
"17346547310".replace(/(\d{3})\d{4}(\d{4})/g, "$1****$2") // "173****7310"
```
$1、$2 为正则表达式()里的内容，此处分别代表前三位和后四位