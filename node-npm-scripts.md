---
title: 【Node】16年，在看Node项目构建和npm scripts
categories: 技术
tags: [Node.js, JavaScript]
date: 2015-12-30 07:56:29
keywords: Nodejs, 前端, ES6
---


## 简介
前端写node项目起来是很容易的，但是一些好的实践，一些提高开发效率，提高工程质量的方案如何快速无侵入的集成到 node development workflow 中起来，可能对刚上手的新人，或者是习惯了自己现有开发环境蜜罐的老人们是不那么容易的，所以该指南对一些常见问题和技巧进行收集和总结，为你的第一个 node 项目保驾护航！

## Todo

- [ ] 补完其余内容
- [ ] 优化下目录结构
- [ ] 优化下现有的例子
- [ ] 导出脚手架，下次方便使用

## Usage 使用
<!-- MarkdownTOC -->

- 在node项目中，如何使用 es6 来写
- 如何在项目中写 API文档（用 swagger api sepc）
- 如何在开发时，文件修改后重启项目
- 如何做环境aware的配置
- 如何通过环境变量配置接口等 - microserver 要求
- 如何部署es6代码到测试环境下（ pm2 babel 结合）
- 如何部署es6代码用于线上生产（先构建好 es5-compatible ）
- 如何更高级的使用的配置文件
- 如何用 supertest 做 api 测试
- 如何用 debug 模块来打测试log和诊断问题
- 如何通过各种hook来解耦合
- 如何做 请求的 validate 和 sanitize
- es6 lint 代码检查和git hooks，precommit
- 代码漏洞检查和扫描和依赖及时更新和锁定
- 代码格式化 beautify 和格式化 用 xo
- 代码覆盖率 用 lab 伊斯坦布尔工具
- babel-node-debug 集成 inspector 用上 devtools
- https://github.com/es-analysis/plato 代码质量
- https://github.com/danielstjules/jsinspect 重复代码检查

<!-- /MarkdownTOC -->

### 在node项目中，如何使用 es6 来写

npm install -g babel-cli

.babelrc
```js
{
  "presets": ["es2015"]
}
```

babel-node entry.js

当然也可以在local中安装 babel-register，用register的方式来运行，而不是解释器


### 如何在项目中写 API文档（用 swagger api sepc）

在loopback中，会对model自动生成CRUD的restful 接口。对于自定义的remote method，通过api 配置说明：

```js
ResPkgUpdate.viewDetail = function(id, cb) {
  ResPkg = loopback.findModel('ResPkg');

  ResPkgUpdate.findById(id, function(err, resPkgUpdate) {
    if(err || !resPkgUpdate) {
      debug('err: cannot find resPkgUpdate by id:', id);
      return cb(err || {msg: 'error'});
    }
    cb(null, resPkgUpdate.resPkg.diffDetail);
  });
};

ResPkgUpdate.remoteMethod('viewDetail', {
  accepts: [{
    arg: 'id', type: 'number', required: true
  }],
  http: {path: '/:id/viewDetail', verb: 'get'},
  returns: {arg: 'result', type: 'object'}
});
```

PS: 其他类型的项目，也可以通过工具从文件中抽取（如方法的注释）从而生成swagger spec 规范的api doc json，实在不纪，可以通过GUI工具来创作 api doc，同时借助 swagger-ui 来让前端使用和可视化调试现有的接口（提供接口参数约束，提供数据schema 查看等）


### 如何在开发时，文件修改后重启项目

package.json
```js
{
  "main": "index.js",
  "scripts": {
    "dev": "nodemon --exec babel-node -- $npm_package_main"
  }
}
```

npm run dev


### 如何做环境aware的配置
配置信息在conf/config.json 中。但如果conf目录下存在以环境变量 NODE_ENV 的值为名称的 json 配置文件，就会自动加载到覆盖替换原来基础配置信息。
这一块是透明无侵入的，只需提供不同环境下的配置信息就好，不需要在代码上区别对待。使用时候如下：

app.kraken.get('<key>');

### 如何通过环境变量配置接口等 - microserver 要求

env: 这个标识说明从环境变量中加载配置
同样还有： import: 从json文件加载到当前的key下为value object

```js
  "api": {
    "articleSearch": "env:GFWC-articleSearch",
    "shopSearch": "env:GFWC-shopSearch",
    "stockSearch": "env:GFWC-stockSearch",
    "portfolioSearch": "env:GFWC-portfolioSearch",
    "portfolioSelfSearch": "env:GFWC-portfolioSelfSearch"
  }
  
_.extend(process.env, {
  "GFWC-articleSearch": "http://<host>:6602/v1/social/article/findByTitle",
  "GFWC-shopSearch": "http://<host>:7700/v1/store/search/shop",
  "GFWC-stockSearch": "http://<host>:3500/portfolio/search/codechain",
  "GFWC-portfolioSearch": "http://<host>:3500/portfolio/search/portfolio/name",
  "GFWC-portfolioSelfSearch": "http://<host>:8090/portfolio/self/search"
});
```

### 如何部署es6代码到测试环境下（ pm2 babel 结合）

.pm2_config.json
```js
{
  "apps" : [{
    "name"        : "gfwealth-composite",
    "script"      : "./entry.js",
    "watch"       : true,
    "exec_interpreter" : "babel-node",
    "exec_mode"        : "fork",
  }]
}
```
pm2 start server/server.js --name "gfwealth-composite"


### 如何部署es6代码用于线上生产（先构建好 es5-compatible ）


### 如何更高级的使用的配置文件

参考使用 shortstop-handlers 来给 config 配置项，如 file:, env:, import: 等handler


### 如何用 supertest 做 api 测试

### 如何用 debug 模块来打测试log和诊断问题

```js
debug = require('debug')('ctrl:search')

debug(`ok:http:${type}:${resp.req.path}`);

```
NODE="ctrl:*,model:*" node .

### 如何通过各种hook来解耦合

一般框架会支持资源 CRUD 等操作的operational hook，
同时也有 remoteMethod hook和application lifecycle hook。

### 如何做 请求的 validate 和 sanitize

```js
req.assert('query', 'Invalid query').notEmpty();
req.checkQuery('page', 'Invalid page').notEmpty().isInt();
req.checkParam('type', 'Invalid type')
  .isIn(['aritlce', 'stock', 'shop']);
  
req.sanitizeParams('urlparam').toBoolean();

var errors = req.validationErrors();
if(errors) return res.send(errors, 400);
```

### es6 lint 代码检查和git hooks，precommit

### 代码漏洞检查和扫描和依赖及时更新和锁定

### 代码格式化 beautify 和格式化 用 xo

### 代码覆盖率 用 lab 伊斯坦布尔工具

### babel-node-debug 集成 inspector 用上 devtools


### https://github.com/es-analysis/plato 代码质量
https://segmentfault.com/a/1190000002434755

### https://github.com/danielstjules/jsinspect 重复代码检查

