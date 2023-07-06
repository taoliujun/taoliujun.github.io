---
title: Map真的挺好的
url: JavaScript/Map
date: 2023-03-07
tags:
  - javascript
  - map
categories:
  - [JavaScript]
---

在以前，想要维护一个集合去处理 dom 和其附加信息的关系，使用 Array 方式如下：

```ts
const list = [
  {
    dom: dom1,
    info: { area: 123 },
  },
  {
    dom: dom2,
    info: { area: 123 },
  },
];
```

如上代码有个很难受的问题，就是读取某个 dom 的时候需要遍历，而且每次都需要遍历，如果数据量大的话，性能会很差。

那么改成 Object 方式：

```ts
const list = {
  key1: {
    dom: dom1,
    info: { area: 123 },
  },
  key2: {
    dom: dom1,
    info: { area: 123 },
  },
};
```

这种方式虽然可以快速读取，但是如何处理 key 和 dom 的关系又是一个麻烦；并且如果需要遍历的话，就需要先转成 Array，然后再遍历，这样也很麻烦。

## Map 的基础用法

但是使用 Map 可以方便的解决上面的问题：

```ts
const list = new Map([
  [dom1, { area: 123 }],
  [dom2, { area: 123 }],
]);
```

这样就可以快速的读取 dom 对应的信息，也可以快速的遍历。

## 注意点

- Map 的键是内存引用，所以两个相同值的对象实例，它们是两个键。
- Map 采取了和 Set 一样的比较算法，所以虽然 NaN 不严格相等于自身，但 Map 将其视为同一个键。
