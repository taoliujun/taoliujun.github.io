---
title: React的性能优化项，持续更新
url: React/optimize_performance
date: 2023-04-02
tags:
  - react
categories:
  - [React]
---

**React 没有最佳性能优化项，性能优化和项目的复杂度相关，所以以下记录的优化项，酌情使用。**

## 组件需要更细粒度

**结论：如果组件中的状态更新频率很快，那么尽量将组件拆分到更细粒度。**

先看一个例子：

```tsx
const Test1: FC = () => {
  console.log("==", Math.random());
  return <div>hello {Math.random()}</div>;
};

const Main: FC = () => {
  const [value, setValue] = useState("");

  return (
    <>
      <input value={value} onChange={(e) => setValue(e.target.value)} />
      <Test1 />
    </>
  );
};
```

![](https://cdn.jsdelivr.net/gh/taoliujun/taoliujun.github.io/assets/202307061103775.png)

如上，虽然`Test1`组件和`value`状态并无关系，但每一次输入都会导致`Test1`组件的重新渲染。如果`Test1`组件的逻辑和渲染够复杂，那么很容易影响性能。

究其原理，Fiber 在节点这一层保存了组件函数中状态和虚拟 Dom 的关系，它的最小渲染单元就是本组件函数。

根据这个原理，试试将输入框拆分成另一个组件：

```tsx
const Input1: FC = () => {
  const [value, setValue] = useState("");

  return <input value={value} onChange={(e) => setValue(e.target.value)} />;
};

const Test1: FC = () => {
  console.log("==", Math.random());
  return <div>hello {Math.random()}</div>;
};

const Main: FC = () => {
  return (
    <>
      <Input1 />
      <Test1 />
    </>
  );
};
```

![](https://cdn.jsdelivr.net/gh/taoliujun/taoliujun.github.io/assets/202307061111086.png)

如上，连续输入 abc，并不触发`Test1`的多次执行，`Input1`组件在一个独立的 Fiber 结构中，它的状态更新便不会影响到`Main`函数。
