---
title: 发布一个 npm 包 
layout: post
date: 2019-10-31 11:41:50
categories: npm
tags: npm
---

# 发布一个 npm 包
> 最近在做项目的 SDK，需要将开发好的 SDK 包发布到 npm 私服上，其他项目使用时可以通过 npm install 安装并通过 import 语法引入使用。

## webpack 打包 SDK（不推荐）
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
        libraryTarget: 'umd',
        globalObject: 'this', // node 和 浏览器都能获取到
        libraryExport: "default", // 将模块的default默认暴露，如果不指定自参数则需要获取window.MyLibrary.default（对应上文 export default），指定后window.MyLibrary即可获取到。
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

## rollup 打包（推荐）
rollup 对于 tree-shaking 支持较好，一些前端流行都是用 rollup 打包，下面罗列一些开发中遇到的点：
1. rollup 支持 umd 和 es 两种打包方式分别对应 package.json 中的 main 和 module 字段，直接 import 时会使用 es 文件对 tree-shaking 执行更好。
2. d.ts 声明文件如何生成？
可以通 tsconfig.json 中的 compilerOptions.declarationDir 字段指定文件夹目录如：dist/types。
通过 rollup-plugin-typescript2 插件生成，通过配置 useTsconfigDeclarationDir: true 读取。
注意如果使用了rollup-plugin-typescript2不指定声明文件目录也会生成声明文件但目录结构比较混乱。
3. external 排除引用的第三方包减少当前包的体积。
建议规范使用 package.json 字段，dependencies 字段中添加生产环境必须的依赖包，这样安装当前包的时候也会将 dependencies 内需要的包添加到使用的项目内。此选项建议直接读取 package.json dependencies。
4. 为什么 rollup 对 tree-shaking 支持更好？
因为rollup基于显式的 import 和 export 语句的方式解析（不需要第三方 babel）。比在编译后的输出代码中，简单地运行自动 minifier 检测未使用的变量更有效（个人理解是webpack只会检测babel处理后的代码）。
5. rollup 不需要 babel 编译新特性代码，自身就可以处理。（官网：Rollup itself processes the config file, which is why we're able to use export default syntax – the code isn't being transpiled with Babel or anything similar, so you can only use ES2015 features that are supported in the version of Node.js that you're running）。有利于 tree-shaking

## package.json
* version 版本号发布时不能与已有的重复。
* dependencies 和 devDependencies 
一直说开发环境的包要放到 devDependencies 中、生产环境的把放到 dependencies。但即使混淆也并不影响项目打包，对于平时开发似乎没什么影响。但作为包发布却有很大的差别，**dependencies 里的依赖用 npm 安装的时候会自动添加到 node_modules 中，webpack externals 或 roolup external 中配置后可不打包在 bundle 中，减小包的体积。**
* peerDependencies
**字段指定的包在npm install 时不会在自动安装到当前宿主环境。**

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
  "typings": "dist/index.d.ts', // 指定 ts 项目的申明文件
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

* 根目录下执行 `npm publish` 发布

* 在其他项目中执行 `npm install -i fuzhongfeng-test-slide-sdk` 安装模块，并可通过 import、require 方式引入

本文参考：https://webpack.js.org/guides/author-libraries/