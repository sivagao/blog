---
title: 【ionic】玩转 view cache
categories: 技术-前端开发
tags: [ionic, 混合应用, JavaScript]
date: 2015-10-30 07:56:29
keywords: Nodejs, 前端, ES6
---


# Ionic 系列 - 玩转 view cache

在默认情况下， views 是被缓存以便于提升性能。当view 切换时，该 view html element 依然是被保留在 DOM 树中的，而view 的 scope 被从 Angular $watch cycle 中抽离。所以当回到原先被cache的view中，scope 重新被加入到 $watch cycle 中 （因此之前的滚动位置都能保留，也不用等待页面元素重新被render）

## 基本用法

viewcache 默认是保留 10 views（太多会吃爆你的内存），当然可以设置更多或关掉。 同时你也可以单独设置 view 的cache 开启情况

- 通过 router 定义
- 通过 ionView directive 的 cache-view 属性

```js
$ionicConfigProvider.views.maxCache(0);

$stateProvider.state('notCacheState', {
    cache: false,
    url: '/not-cache-me',
    templateUrl: 'tpl.html'
});

<ion-view cache-view="false" view-title="My Title!">
```

## 注意点
被 cache 的 view， 在随后被回到的查看，不会在重新执行controller，如果你需要页面在进入和离开时做一些处理，可以使用 ionView 的相关事件 `$ionicView.enter` 和 `$ionicView.leave` (对应之前的 $scope.$on('$destroy'))


## 实际开发遇到的问题

### 展示页数据缓冲没更新

对于展示页，cached view 让你的浏览体验爽到爆，不用等之前的state 和 page 被重新数据拉取和页面渲染，但是你很容易遇到数据更新不及时的问题。 可以通过如下方案解决：

1 加入 pull-refresh 下拉刷新机制（注意在数据拉取和绑定到scope完毕后，调用 `$scope.$broadcast('scroll.refreshComplete')` 告诉 `ionRefresher` 可以收起下拉滚动
2 在页面再次被进入时（$ionView.enter && state.fromCache），静默去更新数据（通过是用 $api 和 $indicator 来设置 loading）


```html
<ion-refresher
  pulling-text="下拉更新..."
  on-refresh="pullFetch()">
</ion-refresher>
```

```js
class demoCtrl extend cacheView {
    constructor($scope, $api, $interval) {

      var fetch = (indicator)=>{
        $api('getPageHome', null, {
          indicator: indicator ? 'global' : null
        }).then((r)=>{
          $scope.$broadcast('scroll.refreshComplete');
          self.viewData = r.data;
        });
      }, homeApiTimer, self = this;
      $scope.pullFetch = fetch; // bind to pull-refresh
      $scope.$on('$ionicView.enter', (scope, states)=>{
        fetch(!states.fromCache); // 从 cache 进入静默更新
        homeApiTimer = $interval(fetch, 10000); // every 2 mins update
      });

      // 定时更新数据，页面离开移除 $interval
      $scope.$on('$ionicView.leave', (scope, states)=>{
        $interval.cancel(homeApiTimer);
      });
    }
}

```

### 过程页中表单数据问题

对于一个task来说（如购买流程，充值流程等），会存在多个过程页面，并且有需要可以回到上一步修改（如在确认页回到订单页去修改购买金额），所以页面缓存是需要的，但是在流程结束后，需要及时清理这个流程中的中间页，但在流程进行中要保存 cache 便于返回操作。

```js
rmCacheView('xxxx')
# 参考：$ionicNavViewDelegate 内部代码

scope.__rmViewCache = (type)=>{
  var map = {
    purchase: [ // 购买 task 的流程页
      'purchase-risk-match', 'purchase-order-verify',
      'purchase-order', 'purchase-otc-order'
    ],
    trans: [ // 转让 task 的中间页
      'trans-buyback', 'trans-other-offer', 'trans-transfer', 'trans-transform'
    ],
    withdraw: ['moneyio-withdraw'] // 取现 task 的中间页
  };
  return $timeout(function() {
    $ionicNavViewDelegate._instances.forEach(function(instance) {
      instance.clearCache(map[type]);
    });
  });
};

scope.__goTrans = (type, product)=>{
  scope.__rmViewCache('trans').then(()=>{
    $temp.set('trans-'+type, {
      product: product
    });
    $state.go('trans-'+type);
  });
};
```

### 切换/退出用户

还有常见，如用户登出或者切换用户，需要把之前用户相关的留有用户状态的页面缓存移除。可以使用如下方法 `$ionicHistory.clearCache()`。

```js
// input[type=submit ng-click=login($event)]
$scope.login = (e)=>{
  $api('login', {
    username: vm.username,
    password: md5(vm.password),
    captach: vm.captach
  }, {
    indicator: e.target // 使用 $indicator 给按钮disable和加状态
  }).then((r)=>{
    $scope.$emit('login:success');
    $ionicHistory.clearCache().then(()=>{
      if($state.hasBack()) {
        $state.backOrGo('recommend-index');
      } else {
        $state.go('recommend-index');
      }
    });
  });
};
```

