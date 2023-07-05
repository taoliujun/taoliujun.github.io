---
title: 聊一聊Symbol
url: JavaScript/Symbol
date: 2023-03-05
tags:
  - javascript
  - symbol
categories:
  - [JavaScript]
---

我在 5 年前看完`Symbol`，就将它抛之脑后，直到最近才重新研究，看看它是什么样的宝藏。

先快速看下它是什么。

## Symbol 基本用法

```typescript
const a1 = Symbol();
const a2 = Symbol("i am a");
const a3 = Symbol("i am a");

console.log("a1", a1);
console.log("a2", a2);
console.log("a2", a2.description);
console.log("a2 === a3", a2 === a3);
```

```shell
a1 Symbol()
a2 Symbol(i am a)
a2 i am a
a2 === a3 false
```

- 从上可看到，`Symbol`是一个函数，它的返回值是一个`Symbol`类型的值，这个值是**唯一**的，即使传入相同的参数，也不会相等。
- 入参的目的仅仅是为了描述它的来源。
- `description`返回`Symbol`实例的描述。

## 让我使用它

假设如下代码：

```typescript
const WOMAN = "woman";
const MAN = "man";

function getSex(input) {
  if (input === WOMAN) return 1;
  if (input === MAN) return 2;
}

console.log(getSex(MAN));
```

但代码足够复杂，或出于粗心，几个常量的值设置为一样的字符串，怕是难以发现。那么用 Symbol 可以方便的保证其唯一性。

```typescript
const WOMAN = Symbol();
const MAN = Symbol();
// ...more
```

## Symbol 的全局注册

直接看代码：

```typescript
function fn1() {
  return Symbol("a");
}

function fn2() {
  return Symbol.for("a");
}

console.log("fn1", fn1() === fn1());
console.log("fn2", fn2() === fn2());
```

```shell
fn1 false
fn2 true
```

正如前面说的，`Symbol`函数的返回值肯定是不一样的，所以多次`fn1`的执行结果是不相等的。但是`Symbol.for`的执行结果是相等的，因为它是全局注册的。
