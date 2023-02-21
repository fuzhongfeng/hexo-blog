---
title: vue源码阅读
layout: post
date: 2022-02-20 11:07:50
categories: Vue
tags: Vue
---

# vue源码阅读

## 1. 工具方法的学习
```
/**
 * Create a cached version of a pure function.
 * 缓存函数，限定参数为string的情况下
 */
export function cached<F: Function> (fn: F): F {
  const cache = Object.create(null)
  return (function cachedFn (str: string) {
    const hit = cache[str]
    return hit || (cache[str] = fn(str))
  }: any)
}

/**
 * Camelize a hyphen-delimited string.
 * 转换为大驼峰
 */
const camelizeRE = /-(\w)/g
export const camelize = cached((str: string): string => {
  return str.replace(camelizeRE, (_, c) => c ? c.toUpperCase() : '')
})
```

