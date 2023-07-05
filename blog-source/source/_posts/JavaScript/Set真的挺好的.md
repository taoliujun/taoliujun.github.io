---
title: Set真的挺好的
url: JavaScript/Set
date: 2023-03-06
tags:
  - javascript
  - set
categories:
  - [JavaScript]
---

## 为什么用 Set

```ts
// Array
let arr1 = ["a", "b", "c", "d"];

arr1 = arr1.filter((element) => element !== "d");
arr1.filter((element) => element !== "e").push("e");
console.log(arr1.includes("b"));

// Object
let obj1 = { a: true, b: true, c: true, d: true };

delete obj1.d;
obj1.e = true;
console.log("b" in obj1);

// Set
let set1 = new Set(["a", "b", "c"]);

set1.delete("d");
set1.add("e");
console.log(set1.has("b"));
```

如上演示了 Array、Object、Set 三种数据结构的基本操作，Set 提供了更为便捷的方式：

1. 相比 Array 有更简单的语法，在运算复杂度上有很大提升。
1. 相比 Object 的命令式操作，Set 提供了方法式的操作。

## 唯一特性

在工作中，要用到集合唯一特性的场景有很多，用 Array 实现的时候，需要自己写很多代码来保证唯一性，而用 Object 实现，又不能保证元素的访问顺序。

举几个例子。

```ts
// 历史搜索记录唯一
const s1 = new Set();

s1.add("长江");
s1.add("黄河");
s1.add("故宫");
s1.add("长江");
s1.add("圆明园");

console.log(s1);
```

```ts
// 移除输入的重复数据ID
const a = [23, 58, 23, 19, 96];
const s1 = new Set(a);

console.log(Array.from(s1));
```

## 注意点

- Set 为了保持元素的唯一，内部使用了`SameValueZero`算法，这意味着`NaN`和`NaN`是相等的，`+0`和`-0`是相等的。
