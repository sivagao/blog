---
title: 【ionic】Ionic/cordova App 更新机制总结
categories: 技术-前端开发
tags: [ionic, 混合应用, JavaScript]
date: 2015-11-19 07:56:29
keywords: Nodejs, 前端, ES6
---

## Overview

ionic deploy 和 Microsoft 的 code push，都是不支持增量更新的前端资源包下载更新。支持配置是否强制更新和推荐更新，支持changelog配置，支持不同channel推送更新。

cordova-app-loader 和 gf'arms（设计状态）是支持前端资源包的增量更新。前者灵活精细稳健的接口和实现，为后续的扩展（如changelog 提示，更新的UI交互）都提供了强有力的支持。

广发现有的做法，是通过容器内容后台（cordova's android, ios 和 atom-shell）来推送打包好的容器包（apk, ipa with plist) 可以实现native代码的更新（如cordova 插件更新）这些是二进制包的更新，当然也可以推送内容包更新（如ionic deploy 和 M$的code push的做法）

## 方案一览

### Ionic deploy
http://docs.ionic.io/docs/deploy-from-scratch
默认的实现是用ionic.io自己的服务器，当然也可以换成自己的服务器来拉取新资源包

### 微软的code push
http://microsoft.github.io/code-push/index.html#getting_started
比起ionic 提供的deploy服务好不少。最重要是资源包是放在azure国内无压力（可更换，其次支持强制和推荐更新（可配给用户的提示更新文案changelog，支持不同的channel（dev,staging,production等。

### cordova-app-loader
https://github.com/markmarijnissen/cordova-app-loader

### 广发 arms 增量更新
杰哥的链接

### 广发现在做法
container容器更新和内容更新
http://10.1.126.53:8815/update/

## cordova-app-loader 详解

远程更新你的 Cordova/Ionic App

### 使用流程
- 使用 manifest.json 来 启动 App
- 构建和部署 App

#### 代码更改后：

1 提交更新到服务器（manifest.json + 修改的文件）
2 使用 CordovaAppLoader 去完成：
- check() 查询是否有新的manifest
- 下载文件
- 更新App

### Cordova App Loader 的一些优点：

- 通过 bootstrap.js 动态加载 js/css 文件
- 快速，可依赖，高性能的下载（并发控制，失败重试，精细的onprogress等）
- 未改变的文件仅仅copy不下载
- responsive App：所有的check, download 的接口都是resolve promise （不会因为异常而进入死循环和等待）
- 断线处理和rollback机制

