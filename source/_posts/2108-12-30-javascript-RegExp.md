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


2. 用正则获取 url 参数
```
function getQueryString(name) {
    var source = window.location.search.substr(1);
    var reg = new RegExp('(^|&)' + name + '=([^&]*)(&|$)', 'i');
    var r = source.match(reg);

    if (r != null) {
        return decodeURIComponent(r[2]);
    }
    return null;
}
// '(^|&)' 这里的引号用于拼接字符串，^|& 表示字符串以空字符串或&开头。
// ([^&]*) ^ 在方括号表达式中使用时表示不匹配方括号内的字符集合；这里表示不匹配 &。* 表示零或多次
// (&|$) 表示以&或空字符串结尾
// i 不区分大小写
// r[0] 为匹配到的所有字符，r[1] 为第一个()匹配的内容，r[2] 为第二个()匹配的内容即=后的结果。
// decodeURIComponent 解析被 encodeURIComponent 的内容
```

3. 匹配Unicode编码中的汉字范围
```
var reg = /^[\u2E80-\u9FFF]+$/;
```

4. 将 '10000000000' 形式的字符串, 以每3位进行分隔展示 '10.000.000.000'
```
'10000000000'.replace(/(\d)(?=(\d{3})+\b)/g, '$1.')
// 定位符 \b 表示匹配一个单词边界，即字与空格间的位置。\b 字符的位置是非常重要的。如果它位于要匹配的字符串的开始，它在单词的开始处查找匹配项。如果它位于字符串的结尾，它在单词的结尾处查找匹配项。
// 非捕获元 ?=， exp1(?=exp2) 表示查找 exp2 前面的 exp1。
// $1 这里的为 replace 的第二个参数，此参数为字符串参数，$n 为100以内的正整数，为特殊变量。表示第 n 个括号内的匹配。
```

5. 替换 dom 字符串中的图片地址
* 向 dom 字符串中的 background-image url() 和 img src 追加参数。（使用jquery初始化会发送请求）
```
const domStr = '<div class="slide " style="width: 1200px; height: 900px; background: url(&quot;https://storage.aixuexi.com/u/89JTeFCY291?imageView2/2/w/1000/q/50&amp;ps=-s25q25&quot;) center center / cover no-repeat;"><div class="a0"><img style="width: 100%;height: 100%;" src="https://storage.aixuexi.com/u/fb0NXFhfe92?ps=-s25q25"></div></div>'
const _quality = 'ps=-s25q25'

// 背景图
domStr.replace(
    /(url\(&quot;)(.*?)(&quot;\))/g,
    (match, p1, p2, p3) => {
        const _p2 = `${p2}${p2.includes('?') ? `&amp;${_quality}` : `?${_quality}`}`;

        return `${p1}${_p2}${p3}`;
    }
);

// 图片组件
domStr.replace(
    /(<img)(.*?)(>)/g,
    (match, p1, p2, p3) => {
        const _p2 = p2.replace(
        /(src=")(.*?)(")/g,
        (match, $1, $2, $3) => {
            const _$2 = `${$2}${$2.includes('?') ? `&${_quality}` : `?${_quality}`}`

            return `${$1}${_$2}${$3}`
        });

        return `${p1}${_p2}${p3}`;
    }
);

* ? 非贪婪限定符，保证匹配到一个 <img /> 标签, .* 会匹配到最后一个 > 不准确
* replace 第二个参数接受函数，需返回 string，match 参数表示当前正则所匹配到的完整字符串，p(n) 参数对应正则中的第 n 括号的内容
```

6. 实现字符串的 trim 方法
```
String.prototype.myTrim = function() {
    return this.replace(/(^\s+)|(\s+$)/g,'')
}
```