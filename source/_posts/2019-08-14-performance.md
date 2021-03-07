---
title: Performance
layout: post
date: 2019-8-14 21:17:09
categories: Performance
tags: Performance
---

# 缓存
------
良好的缓存策略可以降低资源的重复加载提高网页的整体加载速度，通常浏览器缓存策略分为两种：强缓存和协商缓存

## 强缓存
> 在*响应头*中添加 `Expires: Thu, 28 Dec 2019 05:27:45 GMT` 或 `Cache-Control: public, max-age=2592000` 来控制强缓存。
* Expires 是 HTTP/1.0 的字段，它规定了缓存过期的一个绝对时间，过期后需再次请求。缺点：Expires 使用的是客户端本机时间不准确，所以引入了 Cache-Control:max-age。
* Cache-Control 是 HTTP/1.1 的字段，它规定了缓存过期的一个相对时间。优先级：Cache-Control > Expires。常用指令如下：
public: 任意一方都能缓存该资源(客户端、代理服务器等)。
max-age(秒)：缓存的时长，也是响应的最大的Age值。
s-maxage(秒)：公共缓存服务器响应的最大Age值。
no-cache: 浏览器每次使用url的缓存版本之前都必须与服务器重新验证
no-store: 浏览器和其他中间缓存（如CDN）从不存储任何版本的文件
private: 浏览器可以缓存，但是中间缓存不能
* 强缓存阶段如果本地缓存没有过期的话不会向服务器发送请求，状态码为200 (from disk cache)或是200 OK (from memory cache)

## 协商缓存
> 强缓存未命中将进入协商缓存。在*请求头*中添加 `If-Modified-Since: Tue, 28 Nov 2019 05:14:02 GMT` 或 `If-None-Match: W/"5a1cf09a-63c6"` 开启协商缓存，协商缓存阶段每次都会向浏览器发起请求。
* If-Modified-Since 中携带的信息为该资源上次请求的响应头中的 Last-Modified 字段。If-None-Match 中携带的信息为该资源上次请求的响应头中的 ETag 字段。服务器通过这两个字段来判断资源是否有修改，如果有修改则返回状态码200和新的内容，如果没有修改返回状态码304。
* Last-Modified 是 HTTP/1.0 的字段。在第一次请求改资源时把这个标识放到响应头传到客户端，标记此资源在服务器端最后被修改的时间。缺点：如果某个资源只是修改了，但实际内容并没有发生变化，Last-Modified 无法判断出来。
* ETag 是 HTTP/1.1 的字段。服务器通过某种算法对资源生成一个唯一的标识。在第一次请求该资源时把这个标识放到响应头传到客户端。优先级：ETag > Last-Modified

## 最佳实践：
1. 通过版本化 URL 确定的资源，如 html 中引用的 css、js、图片等资源文件，需要随时更新。
* 通过 webpack contentHash 生成资源地址
* 设置长时间使用强缓存：`Cache-Control: max-age=31536000`
2. 不能通过版本化 URL 确定的资源，如 html 文件。`https://xxx.com/index.2s19fg.html`。
* 响应头设置 `Cache-Control: no-cache` 使用前与服务器重新验证
* 响应头设置 `ETag` 字段来验证过期缓存资源

## 设置及验证：
这里给了一些 node express 服务器的配置、最佳实践方案、及验证方式：https://web.dev/codelab-http-cache

#### 参考：https://web.dev/http-cache/#examples