---
title: Proxy的应用场景
url: JavaScript/using_Proxy
date: 2023-03-08
tags:
  - javascript
  - proxy
categories:
  - [JavaScript]
---

Proxy 拦截对象的操作，这些操作有 13 种：get、set、apply 等。

## get、set

拦截对象的读取和赋值操作。如下场景，如果用户角色是管理员，那么期望在读取姓名的时候，补充上“管理员”角色名称，并且管理员的姓名不允许被修改。

```ts
const user = {
  name: "张三",
  sex: "男",
};

const admin = new Proxy(
  { ...user },
  {
    get: (target, key) => {
      if (key === "name") {
        return `管理员：${target[key]}`;
      }
      return target[key];
    },

    set: (target, key, value) => {
      if (key === "name") {
        throw new Error("不能修改管理员姓名");
      }
      target[key] = value;
      return true;
    },
  }
);

console.log("admin信息1：", admin.name, admin.sex);
admin.sex = "女";
try {
  admin.name = "李四";
} catch (e) {
  console.log(e);
}
console.log("admin信息2：", admin.name, admin.sex);
```

```shell
admin信息1： 管理员：张三 男
Error: 不能修改管理员姓名
    at Object.set (/Users/stao2/www/learn/something/1.ts:18:15)
    at Object.<anonymous> (/Users/stao2/www/learn/something/1.ts:29:14)
    at Module._compile (node:internal/modules/cjs/loader:1254:14)
    at Module._extensions..js (node:internal/modules/cjs/loader:1308:10)
    at Module.load (node:internal/modules/cjs/loader:1117:32)
    at Module._load (node:internal/modules/cjs/loader:958:12)
    at Function.executeUserEntryPoint [as runMain] (node:internal/modules/run_main:81:12)
    at node:internal/main/run_main_module:23:47
admin信息2： 管理员：张三 女
```

利用这个规则，可以方便的给对象做一层扩展，而不用修改原来的对象逻辑。

## apply

apply 可以拦截函数的调用，对用户的年龄，但是对不同的角色，校验规则不一样，而且校验代码挺多，函数里的校验就耦合了。例如：

```ts
function validate(role, age) {
  if (role === "student") {
    if (age < 7) {
      return "不满足学生年龄";
    }
  }
  if (role === "teacher") {
    if (age < 24 || age > 60) {
      return "不满足教室年龄";
    }
  }
  // ...其他公用的代码
  return "validate success";
}

console.log(validate("student", 4), validate("teacher", 123));
```

```shell
不满足学生年龄 不满足教室年龄
```

但是利用 Proxy，可以这样做：

```ts
function validate(user) {
  // ...其他公用的代码
  return "validate success";
}

const validateStudent = new Proxy(validate, {
  apply: (target, thisArg, args) => {
    const [user] = args;
    if (user.age < 7) {
      return "不满足学生年龄";
    }
    return target(args);
  },
});

console.log(validateStudent({ age: 7 }), validateStudent({ age: 8 }));
```

## 其他

Proxy 还有`has`，`construct`等拦截，先知道有这些东西，用的时候看文档用 Proxy 实现，而不是自己两眼一抹黑写了半天自己杂乱的代码。

值得一提的是，很多 MVVM 框架是用 Proxy 做数据劫持的，例如 Vue3，这样可以监听到对象的变化，从而实现响应式。
