---
title: Reflect和Object的关系
url: JavaScript/Reflect_And_Object
date: 2023-03-09
tags:
  - javascript
  - reflect
categories:
  - [JavaScript]
---

## Reflect 是什么

在我若干年前阅读 Reflect 文档的那几天，我常闷被里自问，这是个什么玩意？随着时间的推移，我对它的理解越来越深刻，也越来越喜欢它。我想，这是一个值得深入研究的东西。(这句话是 Copilot 补充的，我觉得很有道理，就留下来了)

Reflect 是操作对象用的，我觉得 Reflect 最大的用处有两个。

一是将 Object 的命令式、函数式混用行为，统一成函数式行为。比如`delete obj.key`，`key in obj`对应为`Reflect.deleteProperty(obj, key)`和`Reflect.has(obj, key)`。

二是 Reflect 的方法和 Proxy 的拦截器一一对应，这样就可以用 Reflect 来实现 Proxy 的拦截器，而不用再写一遍拦截器的逻辑。比如`Reflect.get(obj, key)`对应为`get`拦截器，`Reflect.set(obj, key, value)`对应为`set`拦截器。

**它拥有的静态方法和 Proxy 一样多**

## get

```ts
const user = {
  name: "张三",
  age: 20,
};
// user.age
console.log(Reflect.get(user, "age"));
```

```shell
20
```

代码很好理解，不一一表述了。

## set

```ts
const user = {
  name: "张三",
  age: 20,
};
// user.name = '李四'
Reflect.set(user, "name", "李四");
console.log(user);
```

```shell
{ name: '李四', age: 20 }
```

## delete

```ts
const user = {
  name: "张三",
  age: 20,
};

Reflect.deleteProperty(user, "name");
console.log(user);
```

```shell
{ age: 20 }
```

更多的方法见 MDN 即可。
