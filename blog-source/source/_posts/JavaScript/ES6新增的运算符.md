---
title: ES6新增的运算符
url: JavaScript/es6_operator
date: 2023-03-04
tags:
  - javascript
  - 运算符
categories:
  - [JavaScript]
---

## 链判断运算符`?.`

非常方便的代替了传统的属性是否存在的判断，它有两个注意点：

- 左侧的对象是否为`null`或`undefined`，如果不是，则继续运算。
- 如果是的，就不再往下运算，而是返回`undefined`。

常见用法如下：

- obj?.prop // 对象属性是否存在
- obj?.[expr] // 同上
- func?.(...args) // 函数或对象方法是否存在

## Null 判断运算符`??`

如果运算结果为`null`或`undefined`，则返回右侧的值，否则返回左侧的值。

```typescript
// 老写法，如果左侧的boolean值为falsy，都会返回右侧的值
const headerText = response.settings.headerText || "Hello, world!";
// ??写法，只有左侧的值为null或undefined时，才会返回右侧的值
const headerText = response.settings.headerText ?? "Hello, world!";
```
