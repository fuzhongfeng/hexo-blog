---
title: Performance
layout: post
date: 2019-8-14 21:17:09
categories: Performance
tags: Performance
---

#性能
======

## 缓存
良好的缓存策略可以降低资源的重复加载提高网页的整体加载速度，通常浏览器缓存策略分为两种：强缓存和协商缓存

### 强缓存
* 在*响应头*中添加 `Expires: Thu, 28 Dec 2019 05:27:45 GMT` 或 `Cache-Control: public, max-age=2592000` 来控制强缓存。
* Expires 是 HTTP/1.0 的字段，它规定了缓存过期的一个绝对时间，过期后需再次请求。缺点：Expires 使用的是客户端本机时间不准确，所以引入了 Cache-Control:max-age。
* Cache-Control 是 HTTP/1.0 的字段，它规定了缓存过期的一个相对时间。优先级：Cache-Control > Expires。常用指令如下：
public: 任意一方都能缓存该资源(客户端、代理服务器等)。
max-age(秒)：缓存的时长，也是响应的最大的Age值。
s-maxage(秒)：公共缓存服务器响应的最大Age值。
* 强缓存阶段如果本地缓存没有过期则服务端返回200 (from disk cache)或是200 OK (from memory cache)

### 协商缓存
* 在*请求头*中添加 `If-Moified-Since: Tue, 28 Nov 2019 05:14:02 GMT` 或 `If-None-Match: W/"5a1cf09a-63c6"` 开启协商缓存。
* If-Moified-Since 中携带的信息为该资源上次请求的响应头中的 Last-Modified 字段。If-None-Match 中携带的信息为该资源上次请求的响应头中的 ETag 字段。服务器通过这两个字段来判断资源是否有修改，如果有修改则返回状态码200和新的内容，如果没有修改返回状态码304。
* Last-Modified 是 HTTP/1.0 的字段。在第一次请求改资源时把这个标识放到响应头传到客户端，标记此资源在服务器端最后被修改的时间。缺点：如果某个资源只是修改了，但实际内容并没有发生变化，Last-Modified 无法判断出来。
* ETag 是 HTTP/1.1 的字段。服务器通过某种算法对资源生成一个唯一的标识。在第一次请求改资源时把这个标识放到响应头传到客户端。优先级：ETag > Last-Modified

### 常见问题：
1. 请求被缓存，导致新代码未生效？
* 服务端响应添加Cache-Control:no-cache,must-revalidate指令
* 修改请求头If-modified-since:0或If-none-match:""
* 修改请求URL，请求URL后加随机数，随机数可以是时间戳，哈希值，比如：http://xxxx.com?a=123412323
