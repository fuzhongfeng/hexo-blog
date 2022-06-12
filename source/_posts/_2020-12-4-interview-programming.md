---
title: JavaScript-programming
layout: post
date: 2020-12-4 16:03:20
categories: JavaScript
tags: JavaScript
---

## 编程题
1. 将字符串 'abce-fgh-ijk' 转为小驼峰：'abceFghIjk'

a. 一次遍历
```
var s = 'abce-fgh-ijk';
var r = '';

for(let i = 0; i < s.length; i++) {
    if (s[i] === '-' && i !== 0) {
        // 关键点在于++i, 可以跳过一次循环
        r += s[++i].toLocaleUpperCase()
    } else {
      r += s[i]
    }
}

console.log(r) // abceFghIjk
```

b.正则
```
s.replace(/(-[A-Za-z]{1})/g, (match, p1) => { // match 表示匹配到的字符串（包含多个括号的内容），p1匹配第一个括号的内容。
    // 因为是全局匹配，所以此函数会执行多次，返回值会替换一次匹配到的字符串。
    return p1.toLocaleUpperCase().slice(1)
})
// abceFghIjk
```

2. 大数相加
// js 的最大安全数为 Number.MAX_SAFE_INTEGER / Math.pow(2, 53) - 1
```
var str1 = "9007199254740991";
var str2 = "1234567899999999999";

function sum(str1, str2) {
    var maxLen = Math.max(str1.length, str2.length);
    var newStr1 = str1.padStart(maxLen, 0);
    var newStr2 = str2.padStart(maxLen, 0);

    var result = ''
    var ten = 0;

    for (let i = maxLen - 1; i >= 0; i--) {
        var sn = Number(newStr1[i]) + Number(newStr2[i]) + ten;
        ten = Math.floor(sn / 10);
        result = sn % 10 + result;
    }

    if (ten === 1) {
        result = 1 + result
    }

    return result;
}

sum(str1, str2)
```

3. 版本号比较
```
var a = '1.0.45'
var b = '1.0.45.1'

function compare(a, b) {
    // 填充数组
    function padEnd(arr, len) {
        var dis = len - arr.length;
        
        while(dis-- > 0) {
            arr.push('0')
        }
    }

    var aArr = a.split('.');
    var bArr = b.split('.');
    // -1 代表小于，0 代表等于，1 代表大于
    var status = 0;
    
    var maxLen = Math.max(aArr.length, bArr.length)
    padEnd(aArr, maxLen)
    padEnd(bArr, maxLen)

    for (let i = 0; i < maxLen; i++) {
        if (Number(aArr[i]) > Number(bArr[i])) {
            return 1;
        } else if (Number(aArr[i]) < Number(bArr[i])) {
            return -1;
        }
    }

    return 0;
}
```

4. 实现 Person 类满足以下要求：
```
/**
 * new Person('Tony').sleep(10).eat('lunch')
 * // hi, I'm Tony
 * // wait for 10 sec
 * // eating lunch
 *
 * new Person('Tony').eat('lunch').sleep(10).eat('dinner')
 * // hi, i'm Tony
 * // eating lunch
 * // wait for 10 sec
 * // eating dinner
 *
 * new Person('Tony').eat('lunch').sleep(10).eat('dinner').sleepFirst(5)
 * // hi, i'm Tony
 * // sleep fisrt for 5 sec
 * // eating lunch
 * // wait for 10 sec
 * // eating dinner
 */
```
```
class Person {
    constructor(name) {
        console.log(`hi, i'm ${name}`)
        this.name = name;
        this.queue = [];
        this.runner();
    }

    runner() {
        Promise.resolve().then(async () => {
            while (this.queue.length > 0) {
                await this.queue.shift()()
            }
        })
    }

    sleepFirst(time) {
        this.queue.unshift(() => {
            return new Promise((resolve) => {
                setTimeout(() => {
                    console.log(`sleep first for ${5} sec`)
                    resolve()
                }, time * 1000)
            })
        })

        return this
    }

    sleep(time) {
        this.queue.push(() => {
            return new Promise((resolve) => {
                setTimeout(() => {
                    console.log(`wait for ${time} sec`)
                    resolve()
                }, time * 1000)
            })
        })

        return this
    }

    eat(thing) {
        this.queue.push(() => {
            return new Promise((resolve) => {
                console.log(`eating ${thing}`)
                resolve()
            })
        })
        return this
    }
}
```

5. 模拟实现一个JSON.stringify()
1、明确各种类型 JSON.stringify 后的返回值：
```
// 基本类型
JSON.stringify('a') // '"a"'
JSON.stringify(6) // '6'
JSON.stringify(null) // 'null'
JSON.stringify(undefined) // undefined
JSON.stringify(Symbol('a')) // undefined
JSON.stringify(true) // 'true'
JSON.stringify(NaN) // 'null'
JSON.stringify(Infinity) // 'null'

// 引用类型
JSON.stringify([1, "false", false]); // '[1,"false",false]'
JSON.stringify({ x: 5 }); // '{"x":5}'
JSON.stringify({x: undefined, y: Object, z: Symbol("")}); // '{}'
JSON.stringify(new Map()) // '{}'
JSON.stringify(new Set()) // '{}'
JSON.stringify(new Function()) // undefined
JSON.stringify(new Error()) // '{}'
JSON.stringify(new Date()) // '"2022-06-05T04:09:16.821Z"'
JSON.stringify(
    Object.create(
        null,
        {
            x: { value: 'x', enumerable: false },
            y: { value: 'y', enumerable: true }
        }
    )
);
// '{"y":"y"}' 不可枚举的对象属性值会被忽略
var a = {}; a.b = a; JSON.stringify(a) // Uncaught TypeError: Converting circular structure to JSON
```

答：
```
const JSONStringify = (obj) => {

  const isArray = (value) => {
    return Array.isArray(value) && typeof value === 'object';
  };

  const isObject = (value) => {
    return typeof value === 'object' && value !== null && !Array.isArray(value);
  };

  const isString = (value) => {
    return typeof value === 'string';
  };

  const isBoolean = (value) => {
    return typeof value === 'boolean';
  };

  const isNumber = (value) => {
    return typeof value === 'number';
  };

  const isNull = (value) => {
    return value === null && typeof value === 'object';
  };

  const isNotNumber = (value) => {
    return typeof value === 'number' && isNaN(value);
  };

  const isInfinity = (value) => {
    return typeof value === 'number' && !isFinite(value);
  };

  const isDate = (value) => {
    return typeof value === 'object' && value !== null && typeof value.getMonth === 'function';
  };

  const isUndefined = (value) => {
    return value === undefined && typeof value === 'undefined';
  };

  const isFunction = (value) => {
    return typeof value === 'function';
  };

  const isSymbol = (value) => {
    return typeof value === 'symbol';
  };

  const restOfDataTypes = (value) => {
    return isNumber(value) || isString(value) || isBoolean(value);
  };

  const ignoreDataTypes = (value) => {
    return isUndefined(value) || isFunction(value) || isSymbol(value);
  };

  const nullDataTypes = (value) => {
    return isNotNumber(value) || isInfinity(value) || isNull(value);
  }

  const arrayValuesNullTypes = (value) => {
    return isNotNumber(value) || isInfinity(value) || isNull(value) || ignoreDataTypes(value);
  }

  const removeComma = (str) => {
    const tempArr = str.split('');
    tempArr.pop();
    return tempArr.join('');
  };


  if (ignoreDataTypes(obj)) {
    return undefined;
  }

  if (isDate(obj)) {
    return `"${obj.toISOString()}"`;
  }

  if(nullDataTypes(obj)) {
    return `${null}`
  }

  if(isSymbol(obj)) {
    return undefined;
  }


  if (restOfDataTypes(obj)) {
    const passQuotes = isString(obj) ? `"` : '';
    return `${passQuotes}${obj}${passQuotes}`;
  }

  if (isArray(obj)) {
    let arrStr = '';
    obj.forEach((eachValue) => {
      arrStr += arrayValuesNullTypes(eachValue) ? JSONStringify(null) : JSONStringify(eachValue);
      arrStr += ','
    });

    return  `[` + removeComma(arrStr) + `]`;
  }

  if (isObject(obj)) {
      
    let objStr = '';

    const objKeys = Object.keys(obj);

    objKeys.forEach((eachKey) => {
        const eachValue = obj[eachKey];
        objStr +=  (!ignoreDataTypes(eachValue)) ? `"${eachKey}":${JSONStringify(eachValue)},` : '';
    });
    return `{` + removeComma(objStr) + `}`;
  }
};
```
