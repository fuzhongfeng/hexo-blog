---
title: JavaScript-clone
layout: post
date: 2018-05-24 11:02:18
categories: JavaScript
tags: JavaScript
---
# 深拷贝
```
// lodash 中还添加了node中的数据类型，如ArrayBuffer等。
const mapTag = '[object Map]';
const setTag = '[object Set]';
const arrayTag = '[object Array]';
const objectTag = '[object Object]';
const argsTag = '[object Arguments]';

const boolTag = '[object Boolean]';
const dateTag = '[object Date]';
const numberTag = '[object Number]';
const stringTag = '[object String]';
const symbolTag = '[object Symbol]';
const errorTag = '[object Error]';
const regexpTag = '[object RegExp]';
const funcTag = '[object Function]';

const deepTag = [mapTag, setTag, arrayTag, objectTag, argsTag];

/**
 * 判断是否为对象（lodash）
 * isObject({}) // => true
 * isObject([1, 2, 3]) // => true
 * isObject(Function) // => true
 * isObject(null) // => false
 */
function isObject(value) {
  const type = typeof value
  return value != null && (type === 'object' || type === 'function')
}

/**
 * 获取初始化对象
 * @param {*} target 
 */
function getInit(target) {
  const Ctor = target.constructor;
  return new Ctor();
}

/**
 * 获取值类型
 * @param {*} target 
 */
function getType(target) {
  return Object.prototype.toString.call(target);
}

/**
 * 非遍历类型克隆
 * @param {*} target 
 * @param {*} type 
 */
function cloneOtherType(target, type) {
  const Ctor = target.constructor;
  switch (type) {
    case boolTag:
    case numberTag:
    case stringTag:
    case errorTag:
    case dateTag:
      return new Ctor(+target); // 传入时间戳兼容性更好，同lodash
    case regexpTag:
      return cloneRegExp(target);
    case symbolTag:
      return cloneSymbol(target);
    case funcTag:
      // return cloneFunction(target);
      return target; // 函数可以不做处理，返回自身
    default:
      return null;
  }
}

/**
 * 克隆正则表达式（lodash）
 * @param {*} regexp 
 */
function cloneRegExp(regexp) {
  const reFlags = /\w*$/;
  const result = new regexp.constructor(regexp.source, reFlags.exec(regexp));
  result.lastIndex = regexp.lastIndex;
  return result;
}

/**
 * 克隆Symbol(lodash)
 * @param {*} target 
 */
function cloneSymbol(target) {
  return Object(Symbol.prototype.valueOf.call(target));
}

/**
 * 克隆函数(可不处里)
 * @param {*} func 
 */
function cloneFunction(func) {
  const bodyReg = /(?<={)(.|\n)+(?=})/m;
  const paramReg = /(?<=\().+(?=\)\s+{)/;
  const funcString = func.toString();
  if (func.prototype) {
    const param = paramReg.exec(funcString);
    const body = bodyReg.exec(funcString);
    if (body) {
      if (param) {
        const paramArr = param[0].split(',');
        return new Function(...paramArr, body[0]);
      } else {
        return new Function(body[0]);
      }
    } else {
      return null;
    }
  } else {
    return eval(funcString);
  }
}

/**
 * 遍历函数
 * @param {*} array 
 * @param {*} iteratee 
 */
function forEach(array, iteratee) {
  let index = -1;
  const length = array.length;
  while (++index < length) {
    iteratee(array[index], index);
  }
  return array;
}

/**
 * 克隆主函数
 * @param {*} target 被克隆对象
 * @param {*} map 处理循环引用, lodash用构建了一个栈来存储键值对，后续解析
 */
function clone(target, map = new WeakMap()) {
  // 克隆原始类型
  if (!isObject(target)) return target;

  // 初始化
  const type = getType(target);

  // 处理形如 new Number(1) 的原始值
  if (!deepTag.includes(type)) return cloneOtherType(target, type);

  // 原始类型
  if (deepTag.includes(type)) { // 引用类型
    const cloneTarget = getInit(target, type);
    const mapTarget = map.get(target);

    // 防止循环引用
    if (mapTarget) return mapTarget;
    map.set(target, cloneTarget);

    // 克隆set
    if (type === setTag) {
      target.forEach(value => {
        cloneTarget.add(clone(value, map));
      });
      return cloneTarget;
    }

    // 克隆map
    if (type === mapTag) {
      target.forEach((value, key) => {
        cloneTarget.set(key, clone(value, map));
      });
      return cloneTarget;
    }

    // 克隆对象和数组
    const keys = type === arrayTag ? undefined : Object.keys(target);
    forEach(keys || target, (value, key) => {
      if (keys) {
        // 对象结构键不使用索引
        key = value;
      }
      cloneTarget[key] = clone(target[key], map);
    });

    return cloneTarget;
  }
}

export default clone;
```