---
title: npm 私服搭建
layout: post
date: 2019-10-24 16:40:20
categories: npm
tags: npm
---

# Nexus npm 私服搭建
> 利用 Nexus3 进行 npm 的私服搭建

## 安装及启动服务
1.  Nexus3 需依赖 java8 环境。
* 检测java版本：
```
$java -version
```
* 如已安装 java8 则显示信息如下：`java version "1.8.0_231"`

2. 点击 [此处](https://www.sonatype.com/download-oss-sonatype) 下载 Nexus

3. 在任意目录下解压，进入解压后的文件夹为`nexus-3.19.1-01-mac`

4. 启动  Nexus Repository Manager 服务
* 输入命令:
```
cd nexus-3.19.1-01/bin

sudo ./nexus run
```

5. 访问 http://127.0.0.1:8081/

6. 点击右上角的登录。
* 用户名：admin
* 密码：根据提示框找到对应的文件即可复制，也可按照提示修改密码

## 添加npm仓库
> 点击`Create repository`按钮创建如下三个仓库。
1. 选择 npm(proxy) 进入并填写字段：
```
Name: npm-proxy
remote storage : https://registry.npmjs.org  // 或设置为https://registry.npm.taobao.org，此字段为包请求的代理地址
```
* 点击保存

2. 选择 npm(hosted) 填写字段并保存： 
```
Name: npm-hosted // 用于存放私有包
```
* 点击保存

3. 选择 npm(group) 进入并填写字段: 
```
Name: npm-group // 用于存放私有包
```
* 在 Member repositories 选项中添加 npm-proxy 和 npm-hosted
* 点击保存

## 配置并验证 npm 仓库

1. 设置 npm 仓库地址：
```
// ip 自选
npm config set registry http://localhost:8081/repository/npm-group/
```

2. 查看 npm 仓库地址是否设置成功：
```
npm config get registry
// http://127.0.0.1:8081/repository/npm-group/
```

3. 在任意项目路径下初始化：
```
npm init -y
```

执行命令：
```
npm --loglevel info install jquery
```
* 安装信息中可查看请求地址是否私服，如：`fetch 200 http://localhost:8081/repository/npm-group/jquery/-/jquery-3.4.1.tgz`
* 查看 http://127.0.0.1:8081/#browse/search/npm 路由。最初搭建私服里的包内容为空，然后通过私服安装依赖包后就会被缓存下来，下次请求时就不会请求外网地址了。

## 发布包到私服

1. 设置发布权限：
* 在 Security-Realms 界面中 `npm Bearer Token Realm` 添加到右侧 Active 框。
* 点击保存

2. 在 Security-Roles 界面中创建角色：
```
Role ID: nx-deploy
Role name: nx-deploy
```
* 在 Given 框下给角色赋添加 `nx-repository-view-*-*-*` 权限
* 点击保存

3. Security-Users 界面中创建用户：
```
ID: fuzhongfeng // ID 为发布客户端 npm 中的 username 字段
Password: fuzhongfeng
Email: 337925234@qq.com
```

* 在 granted 选项中将用户设置为上一步骤中添加的 nx-deploy 角色
* 注意这里的用户名和密码和邮箱在发布包到私服时会用到

4. 配置包资源中的 package.json
```
"publishConfig" : {
    "registry" : "http://localhost:8081/repository/npm-hosted/"
}
```
* 注意这里的库为 npm-hosted

5. 生成 .npmrc 文件中的 _auth 字段:（用于配置发布权限）
```
echo -n 'username:password' | openssl base64
// username 和 password 分别为第三步新建user时的用户名和密码，这里为`fuzhongfeng`
```

6. 客户端配置 npm 发布权限：
* 查看 npm 的配置：
```
npm config ls -l
```
* 找到 userconfig = "/Users/fuzhongfeng/.npmrc” 字段。点击打开 .npmrc 文件添加如下配置并保存：
```
// 这里的 email 为 创建 user 中的字段，_auth 为第五步中生成的base64字符串
registry=http://127.0.0.1:8081/repository/npm-group/
email=337925234@qq.com
always-auth=true
_auth="ZGVwbG95ZXI6ZGVwbG95ZXI="
```

7. 项目根目录下执行：
```
npm publish
```