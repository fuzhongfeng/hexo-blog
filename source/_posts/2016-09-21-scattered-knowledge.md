---
title: 零散知识
layout: post
date: 2016-09-21 17:53:42
categories: Other
tags: Other
---

## window.parent和window.opener区别

- window.parent能获取一个框架的父窗口或父框架。顶层窗口的parent引用的是它本身。
- window.opener引用的是window.open打开的页面的父页面。

## 解决不支持html5标签的办法

方式一：自己创建标签节点
```
<script>
    document.createElement(e[i]);
</script>
```

方式二：使用Google的html5shiv包（推荐）  
`<script src="http://html5shiv.googlecode.com/svn/trunk/html5.js"></script>`

**不管使用以上哪种方法,都要初始化新标签的CSS**
```
article,aside,dialog,footer,header,section,footer,nav,figure,menu{
    display: block;
}
```

方式三：防弹衣技术
Bulletproof技术，防弹衣技术，建议在所有新的HTML5块级元素中增加一个内部的div元素，然后包含一个CSS class，用这个元素来替代HTML元素。
```
<section>
　　<div class="section">
　　<!-- content -->
　　</div>
</section>

.section {
   　　color: blue;
 }
```

## outerHTML、innerText、innerHTML

{% img https://fuzhongfeng.github.io/2016/09/21/scattered-knowledge/innerHTML.gif 一张图了解outerHTML和innerText、innerHTML %}

## echo

cmd中输入`echo %PATH%` 可查看path环境变量


## 端口占用

netstat -aon | findstr 8088 # 查看指定端口被哪个进程占用，会返回一个进程号
tasklist | findstr 6808 # 查看指定进程号是哪个程序的
taskkill -F -IM javaw.exe # 杀死应用程序

## 响应式编程

响应式编程是一种面向数据流和变化传播的编程范式，这意味着可以在编程语言中很方便地表达静态或动态的数据流，而相关的计算模型会自动将变化的值通过数据流进行传播。响应式编程最初是为了简化交互式用户界面的创建和实时系统动画的绘制而提出来的一种方法，但它本质上是一种通用的编程范式。在MVC软件架构中，响应式编程允许将相关模型的变化自动反映到视图上，反之亦然。

## 检测浏览器版本

最近做的项目对浏览器的类型及版本有要求，同事写了检测浏览器版本的代码如下，我在此处进行记录，供以后参考。

```html
<!--在工程入口html文件的body标签中添加如下script内容-->
<body id="body">
<div id="react-content">
    <script>
      function cheackBowser() {
        var userAgent = navigator.userAgent; //取得浏览器的userAgent字符串
        var version = navigator.appVersion; // 浏览器版本信息
        var isOpera = userAgent.indexOf("Opera") > -1; //判断是否Opera浏览器
        var isChrome = userAgent.indexOf("Chrome") > -1; //判断是否Chrome浏览器
        var isIE = userAgent.indexOf("compatible") > -1 && userAgent.indexOf("MSIE") > -1 && !isOpera; //判断是否IE浏览器
        var isFF = userAgent.indexOf("Firefox") > -1; //判断是否Firefox浏览器
        var isSafari = userAgent.indexOf("Safari") > -1; //判断是否Safari浏览器

        //如果是 IE 火狐 opera，提示下载qq浏览器
        if(isIE || isFF || isOpera) {
          //主页面不显示只显示弹出框，弹出弹框提示下载qq浏览器
          if(confirm("您使用的浏览器存在兼容问题，为了您更好的用户体验，建议您点击确定下载QQ浏览器")) {
              document.getElementById("react-content").style.display = "none";
              window.open("http://browser.qq.com/?adtag=SEM1", "_self");
          } else {
            //点击取消关闭此页面
            window.close();
            //如果关闭此页面被阻止则把页面清空
            if(window){
               window.location.href="about:blank";
            }
          }
        } else if(isChrome){
            var detailVersion = Number(VersionFilter(version)[0].substring(7,9));
            if(detailVersion < 45) {
              document.getElementById("react-content").style.display = "none";
              if(confirm("您使用的浏览器版本太低，为了您更好的用户体验，建议您点击确定下载QQ浏览器")) {
                window.open("https://dldir1.qq.com/invc/tt/QQBrowser_Setup_SEM1.exe","_self");
              }
            } else {
              //谷歌浏览器,版本符合要求
            }
        }
      }

      // 获取详细的chrome版本
      function VersionFilter (value) {
        var reg = /Chrome\/\d{2}\.\d+\.\d+/
         return reg.exec(value)
      }
      cheackBowser()
    </script>
</div>
</body>
```