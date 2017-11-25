---
title: 【Tech-Guide】Python 简要面试问题
categories:  Tech-Guide
keywords: Python, fullstack, Node.js
date: 2016-02-29 07:56:29
---

从去年开始，团队就开始要从前端要向一个全职能的部门去转变，所以要陆陆续续总结一些后端技术的面试指南，用于团队面试。

## 脚本开发
- 如何在Python处理一个8G大小的文本（迭代器，with等）
- 你用Python做过哪些好用有趣的脚本
- 你最大的Python项目是怎么维护的，__init__.py 是干什么的，为什么global import 不好

## Web开发
- 喜欢/用过哪些Web框架（flask, django, tornade等）对比不同和优缺点
- 为什么Python会有这么多的Web Framework（WSGI，Jinja2，SQLAlchemy等
- 关于服务的部署（对于实体机，对于虚拟主机下，对于iaas，对于paas. supervisord，WSGI nginx等）
- 你是如何搭建开发环境的（关于环境隔离：virtualenv，requirements.txt等）

## 关于语法
- 你喜欢的Python语言特性
- with的用法和实现(__enter__ 和 __exit__)
- generator的好处和实现（__iter__ 和__next__）
- 装饰器Decorator的用法和实现（@<bar>, func_wrapper. flask中用于注解view function定义url path 和 method，或权限管理等）
- 有哪些special方法如何定义和用途（__<foo>__）
- list, array, tuple 的区别
- range和xrange的区别
- list compression列表推导式的用法
- PEP8有哪些guide，你还知道哪些PEP(255-simple generators, 318-decorators, 342-coroutines via enhanced generators, 333-WSGI等)
- Python 2.x 和 Python3k的区别，为什么这么大的升级和不兼容

## 编程特性
- 聊聊GIL。说说它是怎么影响Python的并发的，哪些情况下可以关闭掉
- 关于pickling和marshling的注意点
- Python的垃圾回收是怎么做的
- Python哪些语言特性来实现元编程，你用元编程的应用场景
- 函数式编程和Python中的辅助（高阶函数，functools, operator, 推导式，迭代器，itertools等）
- Python中的monkey patching有哪些技巧和运用

## 关于工具
- 你是怎么做debugging的，开发时和线上（remote debug?
- 你是怎么linting和profiling的
- 你用什么IDE做开发，哪些功能用的多（如refactor，docstrings，REPL ipython等）


