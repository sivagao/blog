---
title: 【Angular】给 $http 加上好用的操作提示indicator interceptor
categories: 技术-前端开发
tags: [Angular, JavaScript]
date: 2015-09-11 07:56:29
keywords: Nodejs, 前端, ES6
---

### 为什么
用户在使用你的产品需要及时的反馈，譬如在拉取后端数据时候，在做了某些涉及后端的操作时，在消费数据时遇到某些莫名错误时。
同时，我们在开发的时候，如果对于这些通用的操作提示能够有全局的一致的UI/UX的话，可以继承进入 Angular 的$http 模块中，这样不需要在每次业务编写时都手动改下。 

所以下面的 Http interceptor 主要解决如下三个问题：

- 数据拉取的模态 loading
- POST/PUT/DELETE 等 操作button的buzy 和点击屏蔽disabled button
- 通用的5xx的报错提示等

### 效果展示
| 提交indicator  | 错误indicator  | 拉取indicator |
| :------------ |:---------------:| -----:|
| ![button](https://cloud.githubusercontent.com/assets/697853/9808244/2f9a839c-5890-11e5-868d-4005c626bb45.png) | ![error-msg](https://cloud.githubusercontent.com/assets/697853/9808245/2fe2145a-5890-11e5-84bf-4bcfe82a66ec.png) |![global](https://cloud.githubusercontent.com/assets/697853/9808246/2fe5cabe-5890-11e5-8286-13b8eeae47a7.png) |


### 使用方式

```javascript
$api('getFundRatio', {
  pageSize: 60,
  pageNum: 1
}, {
  // global 代表全局loading indicator
  indicator: 'global'
});

ng-click="depositFund($event)"
$api('depositFund', {
  amount: vm.amount,
  bankcode: vm.currentCard.bankcode,
  bankno: vm.currentCard.typeno,
  mobilephone: vm.mobile
}, {
  // 忽略通用的错误踢死
  ignoreErr: true,
  // 传入 button 来做按钮提示和在ajax过程中的disabled
  indicator: e.target
}).then((r)=>{
  $temp.set('charge', {
    card: vm.currentCard,
    amount: vm.amount,
    dealDate: vm.dealDate
  });
  $state.go('moneyio-charge-succ');
}, (r)=>{
  // 自定义的错误处理
  $temp.set('charge', r.data);
  $state.go('moneyio-charge-error');
});
```

### 具体实现

```javascript
'use strict';

// ngInject
var indicator = ($injector, $timeout, $q, $rootScope) => {
  var $ionicLoading, $notice, $compile;

  var toggleGlobalIndicator = tplMethod(()=> {
    $ionicLoading.show({
      template: '<ion-spinner icon="ios"></ion-spinner>',
      animation: 'fade-in',
      showBackdrop: true // 通过黑透明遮罩，来模态
      // showDelay: 100
    });
  }, ()=>{
    $ionicLoading.hide()
  }, 800);

  var toggleBtnIndicator = tplMethod((target)=>{
    $compile = $injector.get('$compile');
    // avoid chain indicator
    if($(target).find('ion-spinner').length) return;
    $(target).prepend(
      $compile('<ion-spinner icon="ios-small"></ion-spinner>'
    )($rootScope));
    target.disabled = true;
  }, (target)=>{
    if(!target) return;
    target.disabled = false;
    $(target).find('ion-spinner').remove();
  }, 3000); // 至少3s的显示

  function handleRequest(conf) {
    if(!_.isUndefined(conf.indicator)) {
      conf.timeout = 10000; // default timeout
    }
    if(!conf.indicator) return;
    conf.indicator === 'global'
      ? toggleGlobalIndicator(true)
      : toggleBtnIndicator(true, conf.indicator);
    // whether post/delete auto set global indicator
    // check conf.method POST DELETE
  }

  function handleResponse(r) {
    if (!r.config) return;
    if(_.isUndefined(r.config.indicator)) return;
    r.config.indicator === 'global'
      ? toggleGlobalIndicator(false)
      : toggleBtnIndicator(false, r.config.indicator);
  }

  return {
    request: function(conf) {
      // $ionicLoading 依赖需要动态的插入，避免循环引用
      $ionicLoading = $injector.get('$ionicLoading');
      handleRequest(conf);
      return conf || $q.when(conf);
    },
    response: function(response) {
      $ionicLoading = $injector.get('$ionicLoading');
      handleResponse(response);
      return response;
    },
    responseError: function(rejection) {
      handleResponse(rejection);
      // check rejection.config.ignoreErr
      if(rejection.status === 403) {
        $rootScope.$storage.userData = null;
        // auto redirect to login?!
      }
      if(rejection.data && !_.isUndefined(rejection.data.error)) {
        // ignoreErr 来允许HTTP API 使用方直接定制错误处理
        if(!rejection.config.ignoreErr) {
          $notice = $injector.get('$notice');
          $timeout(()=>{
            $notice.error(rejection.data.error);
          }, 100);
        }
      }
      return $q.reject(rejection);
    }
  }

  function tplMethod(yes, no, span) {
    var time, delta
    return function(bool) {
      var args = _.toArray(arguments)
      args.shift();
      if(bool) {
        time = new Date().getTime();
        yes.apply(null, args);
      } else {
        delta = new Date().getTime() - time;
        $timeout(()=>{
          no.apply(null, args); // why not Fn.bind
        }, 0); // span - delta
      }
    }
  }
};
export default indicator;
```


