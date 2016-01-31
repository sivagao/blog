---
title: 【ionic】Navigation 和 ionHistory 详解
categories: 技术-前端开发
tags: [ionic, 混合应用, JavaScript]
date: 2015-10-30 18:56:29
keywords: Nodejs, 前端, ES6
---

# Ionic 系列 - Navigation 和 ionHistory 详解

navigation，本身是个简单的概念。用户使用不同功能，在不同页面跳转，同时提供回退，前进功能来方便用户。但是遇到如下场景和需求就会让问题变得开始棘手起来。

- 去做任务B，必须先完成A（甚至它还有前置任务），然后跳转回来。
- 在完成任务后，返回不能回到之前的步骤过程页，而是回到任务入口页。
- 一些关键步骤不能跳过
- Android系统有自己系统级的返回键需要特殊处理
- iOS 有自己的 swipe back 的使用习惯
- 页面的任务入口需要检查不同的权限（是否登录，用户是否实名验证，用户是否风险测评）
- 一些特定的 UI 影响 navigation（如 tabs, side-menus，引入 history stack 概念


下面我们依次来解决这些问题，理解 Ionic 中 navigation 和 $ionHistory 用法和hack

## 基本用法

navigation 主要涉及 ionNavView（替换掉原来 ui-router 的 ui-view），还有 ionNavBar, nav-transition, nav-buttons 等。
http://ionicframework.com/docs/api/directive/ionNavView/


## 实际开发遇到的问题

以下的问题单独解决起来都不能，但是如何在不侵入原来代码和流程的情况下，在外围（global ctrl && ionic 层）提供一些快捷方法来处理这些case，而不是在业务代码中处理。

### 前置任务完后跳回

在 `ui-router` 下，我们常常使用  $state.go 来跳转页面进入不同的状态。但是当在前置任务需要完成的情况下，我们通过增强 $state 来提供 goAndBack 和 backOrGo 的功能。
前者是说跳到前置任务页，并且标示自己需要返回。
后者在结果页中申明说如果这个task B是其他task A的前置任务那么需要回退到task A，否则就到自己的默认下一页

使用： 在需要授权操作的 `asset-index` 有 `auth-sref`（其中调用了
`goAndBack('login-index')`），在登录成功后会检查 `hasBack()`，调用 ·。这样登录成功自动进入 `assets-index` 而不是默认登录成功后的 `home-index` 页

```js
$state.backOrGo = function() {
  if($state.hasBack()) return $state.back();
  resetStateBack();
  $state.go.apply($state, arguments);
};
$state.goAndBack = function() {
  stateBack = $state.current.name;
  stateBackParam = $state.params;
  $state.go.apply(null, arguments);
};

var stateBack = null, stateBackParam;
$state.hasBack = ()=>{
  if(stateBack) return true;
  return false;
};
function resetStateBack() {
  stateBack = null
  stateBackParam = null;
}
$state.back = ()=>{
  var ionHist = $ionicHistory.viewHistory();
  var hId = ionHist.currentView.historyId;
  var stack = ionHist.histories[hId].stack, len = stack.length, count;
  var hasGone = _.any(stack, (i, idx)=>{
    i = stack[len-1-idx];
    if(i.stateName === stateBack) {
      count = -(idx);
      return true;
    }
  });
  resetStateBack();
  if(!hasGone) count = -1;
  $ionicHistory.goBack(count);
};
```

### 结果页回退到入口而不是上一步骤页

```js
var isInjectBackRoute = false, injectBackRoute;
scope.__injectBackRoute = (r)=>{
  isInjectBackRoute = true;
  injectBackRoute = r;
};

scope.$ionicGoBack = function(backCount) {
  if(isInjectBackRoute) {
    scope.__route(injectBackRoute);
  } else {
    $ionicHistory.goBack(backCount);
  }
  isInjectBackRoute = false;
};
```

### 关键步骤(页)不能返回跳过

```html
<ion-nav-bar>
  <ion-nav-back-button class="button-link"
    ng-hide="__isDisableBack">
    <i class="ion-ios-arrow-left"></i>
  </ion-nav-back-button>
</ion-nav-bar>
```

```js
scope.__isDisableBack = false;
scope.__disableBack = ()=>{
  scope.__isDisableBack = true;
};

scope.__restoreRoute = (r)=>{
  isInjectBackRoute = false;
  scope.__isDisableBack = false;
  /*$ionicPlatform.offHardwareBackButton(disBackSystemBtn); */
};

// 在 $stateChangeSuccess 等页面跳转中清除对router的修改，__restoreRoute
```


### Android 系统级的返回键处理

把 Android 的回退按钮的 fallback 到被injected 的$ionicGoBack 和 __isDisableBack. 前者来提供定制的回退（如结果页到入口页），后者来屏蔽返回（如在强制步骤页不许跳出回退）

ionic 在 platform service 中提供来hook来拦截 backbutton action。registerBackButtonAction(handler, proirty)

Ionic 自带的优先级如下：

- Return to previous view = 100
- Close side menu = 150
- Dismiss modal = 200
- Close action sheet = 300
- Dismiss popup = 400
- Dismiss loading overlay = 500

```js
$ionicPlatform.registerBackButtonAction((e)=>{
  if(!scope.__isDisableBack) {
    scope.$ionicGoBack(); // isInjectBackRoute dont care
  }
  e.preventDefault();
  e.stopPropagation();
}, 101);
```

### 任务入口需要检查不同的权限

```js
# 是否登录，用户是否实名验证，用户是否风险测评

/*
  auth-sref directive
  <a auth-sref="assets-hold">我的持仓</a>
 */

export default ($state) => {
  return function(scope, elem, attrs) {
    elem.on('click', (e)=>{
      if(scope.__needLogin()) return;
      $state.go(attrs.authSref);
    });
  }
}

// Global Methods:
scope.__needLogin = ()=>{
  var account = $storage.userData;
  if(!account) {
    // $notice.xinfo('请先登录');
    $state.goAndBack('login-index');
    return true;
  }
  return false;
};
```

