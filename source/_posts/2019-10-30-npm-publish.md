---
title: 发布一个 npm 包 
layout: post
date: 2019-10-31 11:41:50
categories: npm
tags: npm
---

# 发布一个 npm 包
> 最近在做项目的 SDK，需要将开发好的 SDK 包发布到 npm 私服上，其他项目使用时可以通过 npm install 安装并通过 import 语法引入使用。

## webpack 打包 SDK
> 通过 script 脚本方式引入可以通过在 window 上添加属性并获取脚本内容，那么如果想通过 import、require 引入该如何打包呢？其实通过 webpack 配置来打包即可实现

1. 将所有需要导出的模块引入到一个文件中并导出：
```
// ./src/index.ts 文件
import EditorSDK from './editor/index'
import PlayerSDK from './player/index'

export default { EditorSDK, PlayerSDK }
```

2. 配置 entry 和 output.libraryTarget 选项：
```
module.exports = {
    entry: './src/index.ts', // 项目的入口文件
    output: {
        library: 'MyLibrary',
        libraryTarget: 'umd' 
    }
};
```
* 将模块包通过所有模块化方式暴露，因此可通过 CommonJS、AMD、ES2015 规范或作为全局变量使用。打包后的输出如下：
```
(function webpackUniversalModuleDefinition(root, factory) {
  if(typeof exports === 'object' && typeof module === 'object')
    module.exports = factory();
  else if(typeof define === 'function' && define.amd)
    define([], factory);
  else if(typeof exports === 'object')
    exports['MyLibrary'] = factory();
  else
    root['MyLibrary'] = factory();
})(typeof self !== 'undefined' ? self : this, function() {
  return _entry_return_;
});
```

* 配置 externals 选项：
```
// 此选项可以将库中依赖的第三方不包含在打包生成的 bundle 文件中，从而减小发布包的体积
module.exports = {
  externals: 'events'
}
```

## 2. npm publish 发布
> 通过 npm publish 发布时只需要将编译过的 dist 目录发布即可。此时可配置 package.json 下的 files 字段。（此时只会将 dist 文件夹以及根目录下的 README.md 发布）
```
{
  "name": "fuzhongfeng-test-slide-sdk", // npm 包的名称，install 时使用
  "version": "1.0.0", // 每次 npm publish 时需要与线上的版本号不同才能发布
  "description": "slide-sdk publish",
  "main": "/dist/bundle.js", // 指定模块ID为程序的主入口。如果包的名称为 foo 并且用户安装了此包，然后又执行 require（"foo")，则返回主模块所导出的对象。
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "files": ["dist"], // 指定需要发布的文件夹目录，这样不会将开发代码也发布上去
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

* 根目录下执行 `npm publish` 发布

* 在其他项目中执行 `npm install -i fuzhongfeng-test-slide-sdk` 安装模块，并可通过 import、require 方式引入

本文参考：https://webpack.js.org/guides/author-libraries/