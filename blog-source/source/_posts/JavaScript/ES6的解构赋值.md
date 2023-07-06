---
title: ES6的解构赋值
url: JavaScript/es6_destructuring_assignment
date: 2023-03-03
tags:
  - javascript
  - 解构赋值
categories:
  - [JavaScript]
---

工作中经常会使用变量的解构赋值，随便举几个例子。

## 数组解构

```typescript
const fruits = ["apple", "pear", "orange", "banana", "peach"];
const [fruit1, , fruit3, ...otherFruits] = fruits;

console.log({
  fruit1,
  fruit3,
  otherFruits,
});
```

```shell
> node 1.ts
{
  fruit1: 'apple',
  fruit3: 'orange',
  otherFruits: [ 'banana', 'peach' ]
}
```

在执行结果中可以看到，`fruit1`是 apple，其后是一个空位，所以`fruit3`是 orange，`otherFruits`前面有扩展运算符，所以它读取了剩下的所有索引值。

## 对象解构

对象解构里记录一个概念，就是模式与变量，见下面一个嵌套结构的例子：

```typescript
const user = {
  base: {
    truename: "张三",
    sex: "女",
  },
  card: "320xxx",
};

const {
  base,
  base: { truename },
  card,
} = user;

console.log("base:", base);
console.log("truename:", truename);
console.log("card:", card);
```

```shell
base: { truename: '张三', sex: '女' }
truename: 张三
card: 320xxx
```

在如上的对象解构中，第一个`base`是变量，第二个`base`是模式，它的值是`user`对象中的`base`属性，`truename`是变量，`card`也是变量。

## 字符串、数组、布尔值解构

因为解构的规则是将变量转为对象（数组也是对象），字符串、数字、布尔都可以转成对象。

**所以字符串可以像数组**那样解构。

```typescript
const word = "abcdefg";

const [, x] = word;
const { 3: y } = word;

console.log({ x, y });
```

```shell
{ x: 'b', y: 'd' }
```

## 小技巧

**数组也是对象**，所以读取指定索引的值，可以这样写：

```typescript
const fruits = ["apple", "pear", "orange", "banana", "peach"];
const { 3: fruit1 } = fruits;

console.log(fruit1);
```

```shell
banana
```
