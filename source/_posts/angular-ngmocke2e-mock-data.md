---
title: 【Angular】使用 ngMockE2E 模拟 api 数据
categories: 技术-前端开发
tags: [Angular, JavaScript]
date: 2015-08-07 07:56:29
keywords: ngMock, 前端, ES6
---


对于那些使用了 $http service 来网络请求的项目中，假冒(fake) HTTP 后端可以用来辅助实现 E2E 测试 或者是 无后端或不依赖后端的开发方式。

注意：对于 假冒(fake) HTTP 后端来实现适合于单元测试的需求，请参阅单元测试 ` $httpBackend mock`。

该模块即包括了，用于构建静态或动态响应数据的拦截式  `when` api（推荐使用 whenGET, whenPOST 等快捷方法），也包括了如 `passthrough` 把请求放出的 api，用于一些特定的请求（例如与某些后端接口或从开发服务器gulp connect 中获取模板文件等）

## 关键代码：

- 1 通过全局的 DEBUG 变量来看是否要注入 ngMockE2E 模块
- 2 通过 whenGET, whenPOST, passThrough 配置要拦截和放行的URL 和 预期的后端响应数据
- 3 加入一个 http interceptor 来 加入延迟，便于注入 webpack 的 sourcemap 代码注入到 devtools 这样断点才能打到 uncompiled raw es6 代码上

```js
var App = angular.module('gfInvest', [
     // your ng module dependencies defined here
]);
if(DEBUG) {
  App.requires.push('ngMockE2E');
}
```

```js
if(DEBUG) {
  // http-status, response body, response headers 
  $httpBackend.whenPOST(/send_sms_key/).respond([200, {}, {}]);

   // just response body
  $httpBackend.whenGET(/flash_sale/).respond({
    products: [{
      title: '定增宝3号进取',
      extra2: {
        qgjs: 0, // 非0表示抢购结束
        flashsale_start: '20150731',
        flashsale_end: '20151203',
        yqsyl: 6.22
      }
    }]
  });

  // some dynamic data (recommend using faker.js)
  $httpBackend.whenGET(/.*query_balance/).respond({
    sharelist: _.times(3, _.partial(_.random, 1, 6, false));
  });

  $httpBackend.whenGET(/.*/).passThrough();
  $httpBackend.whenPOST(/.*/).passThrough();
}
```


```js
// add delay for $httpBackend when mocking
// for webpack sourcemap to load devtools to debug...
App.config(function($provide) {
  $provide.decorator('$httpBackend', ($delegate)=>{
    var proxy = (method, url, data, callback, headers)=>{
      var interceptor = ()=>{
        var _this = this,
            _arguments = arguments;
        setTimeout(()=> {
            callback.apply(_this, _arguments);
        }, 700);
      };
      return $delegate.call(this, method, url, data, interceptor, headers);
    };
    for(var key in $delegate) {
      proxy[key] = $delegate[key];
    }
    return proxy;
  });
});
```



