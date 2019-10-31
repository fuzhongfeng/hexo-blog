---
title: 发布一个 npm 包 
layout: post
date: 2019-10-31 11:41:50
categories: npm
tags: npm
---

# 发布一个 npm 包
> 最近在做项目的 SDK，需要将开发好的 SDK 包发布到 npm 私服上，其他项目使用时可以通过 npm install 安装并通过 import 语法引入使用。

## 1. webpack 打包 SDK
> 通过 script 脚本方式引入可以通过在 window 上添加属性来获取，那么如果想通过 import 引入改如何打包呢？这里通过 webpack 配置来打包即可添加模块化

* 将所有需要导出的模块引入到一个文件中并导出：
```
// webpack 配置
module.exports = {
    entry: './src/index.ts', // 项目的入口文件
};
```
```
// ./src/index.ts

import EditorSDK from './editor/index'
import PlayerSDK from './player/index'

export default { EditorSDK, PlayerSDK }
```

* 配置 output.libraryTarget 选项：
```
output: {
    library: 'MyLibrary',
    libraryTarget: 'umd' 
}
```
在所有模块定义下公开库，此时可以在 CommonJS、AMD 规范以及作为全局变量一起使用。打包后的输出如下：
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
module.exports = {
  externals: 'events'
}
```
此选项可以将库中依赖的第三方不包含在生成的 bundle 中。

## 2. npm publish 发布
> 通过 npm publish 发布时只需要将编译过的 dist 目录发布即可。也可以再主项目下通过配置 main 字段指向 dist 下的文件但会将开发代码也发布到 npm。
* 在 dist 下执行 `npm init -y` 生成 package.json 文件：
```
{
  "name": "fuzhongfeng-test-slide-sdk", // npm 包的名称，install 时使用
  "version": "1.0.0", // 每次 npm publish 时需要与线上的版本号不同才能发布
  "description": "slide-sdk publish",
  "main": "bundle.js", // 指定模块ID为程序的主入口。如果包的名称为 foo 并且用户安装了此包，然后又执行 require（"foo")，则返回主模块所导出的对象。
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

* 在 dist 下执行 `npm publish` 发布
* 在其他项目中即可通过 `npm install -i fuzhongfeng-test-slide-sdk` 安装模块，并通过 import 引入

参考：https://webpack.js.org/guides/author-libraries/