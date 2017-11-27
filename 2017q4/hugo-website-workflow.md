---
title: 使用 CircleCI/Slack 构建 Hugo 多 SubModule 项目
date: 2017-03-25
---

通过在各个 SubModule 的代码仓库，配置 webhook，当每次有提交通知 CircleCI 构建 gaohailang.github.io 项目，即可实现子项目更新同步更新父项目。

Slack 可以先建立自己的 workspace. 然后新建诸如 #website channel.
安装 Github 和 CircleCI 两个 app，设置 integration.


curl -X POST 'https://circleci.com/api/v1.1/project/github/gaohailang/gaohailang.github.io/tree/master?circle-token=e41f9e115c2430c7240e950210651c075218d95e'
