---
title: Promise的忽略点和几个方法的区别
url: JavaScript/Promise_ignore_points_and_methods
date: 2023-03-02
tags:
  - javascript
  - promise
categories:
  - [JavaScript]
---

**Promise**是业务开发中使用率最高的 ES6 方法了，但日常容易忽略几个点，以及记不得它几个方法的区别，现记录。

## 容易忽略的点

- `catch`是`then`的语法糖，但是`catch`可以捕获`then`中的异常，而`then`的第二个回调函数不行。
- 如果没有使用`catch`捕获错误，那么 Promise 的错误不会传递到外层，即使外层有`try...catch`也捕获不到。
- 因为`catch`捕获了`rejected`且返回新的实例，所以在`all`等方法中，p1 实现了`catch`方法，则 p1 的`rejected`不会触发 p 的`catch`。

## 几个方法的区别

| 方法       | resolved 的前提 | rejected 的前提 | 说明                                                                                                                                 |
| ---------- | --------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| all        | 全部 resolved   | 任一 rejected   | -                                                                                                                                    |
| race       | 任一 resolved   | 任一 rejected   | 任一率先改变的状态，传递给 p                                                                                                         |
| allSettled | 全部改变        | -               | 状态全部改变，传递给 p。格式为: <br /> `{status: 'fulfilled', value: value}` <br/> 或者 <br/> `{status: 'rejected', reason: reason}` |
| any        | 任一 resolved   | 全部 rejected   | 传递给`catch`的是一个`AggregateError`实例                                                                                            |
