---
title: useTransition在性能优化中的使用
url: React/useTransition
date: 2022-09-05
tags:
  - react
  - useTransition
categories:
  - [React]
---

`useTransition` is a React Hook that lets you update the state without blocking the UI.

文档中简单一句话说明`useTransition`的用途：不阻塞 UI 的情况下更新**状态**。

## 解决什么问题？

正常代码下，JavaScript 是单线程的，所以执行一段耗时的代码，会阻塞 UI 的渲染，导致页面卡顿。React 提供了**时间切片**的功能来尽量确保一帧中有充足的时间来渲染 UI，而`useTransition`就是在这个基础上，可以在不阻塞 UI 的情况下使用时间分片特性**更新状态**。

## 一个例子

先看下卡顿是如何形成的，一个简单的代码，每 500ms，更新`name`状态。另外点击按钮的时候，更新一系列状态并渲染到 dom 中。

```tsx
const getDatas = () => {
  const datas = [];
  for (let i = 1; i <= 2000; i += 1) {
    const s = Math.random() * Math.random();
    datas.push(s);
  }
  return datas;
};

const Main: FC = () => {
  const [name, setName] = useState("world");

  const [datas1, setDatas1] = useState<number[]>([]);
  const [datas2, setDatas2] = useState<number[]>([]);
  const [datas3, setDatas3] = useState<number[]>([]);
  const [datas4, setDatas4] = useState<number[]>([]);
  const [datas5, setDatas5] = useState<number[]>([]);
  const [datas6, setDatas6] = useState<number[]>([]);
  const [datas7, setDatas7] = useState<number[]>([]);
  const [datas8, setDatas8] = useState<number[]>([]);

  const onClick1 = useCallback(() => {
    setDatas1(getDatas());
    setDatas2(getDatas());
    setDatas3(getDatas());
    setDatas4(getDatas());
    setDatas5(getDatas());
    setDatas6(getDatas());
    setDatas7(getDatas());
    setDatas8(getDatas());
  }, []);

  useEffect(() => {
    window.setInterval(() => {
      setName(`world ${Math.random()}`);
    }, 500);
  }, []);

  return (
    <div>
      hello {name}
      <br />
      <button onClick={onClick1}>click me</button>
      <br />
      <div>
        {datas1.map((v) => {
          return <div key={v}>{v}</div>;
        })}
      </div>
      <div>
        {datas2.map((v) => {
          return <div key={v}>{v}</div>;
        })}
      </div>
      <div>
        {datas3.map((v) => {
          return <div key={v}>{v}</div>;
        })}
      </div>
      <div>
        {datas4.map((v) => {
          return <div key={v}>{v}</div>;
        })}
      </div>
      <div>
        {datas5.map((v) => {
          return <div key={v}>{v}</div>;
        })}
      </div>
      <div>
        {datas6.map((v) => {
          return <div key={v}>{v}</div>;
        })}
      </div>
      <div>
        {datas7.map((v) => {
          return <div key={v}>{v}</div>;
        })}
      </div>
      <div>
        {datas8.map((v) => {
          return <div key={v}>{v}</div>;
        })}
      </div>
    </div>
  );
};
```

**首先不点击按钮，观察 5 秒，没有卡顿现象，性能表现如图：**

![](https://cdn.jsdelivr.net/gh/taoliujun/taoliujun.github.io/assets/202307041609217.png)

可以看到有几个微微凸起的黄色点，对应着每次的`hello`状态更新和渲染，它们的执行时间都在 1ms，没有超过一帧的时间。

![](https://cdn.jsdelivr.net/gh/taoliujun/taoliujun.github.io/assets/202307041610767.png)

选取其中一个黄色的点，查看它的详情。React 的调度器、协调器、渲染器创建了对应的任务，分步执行了任务，具体可阅读 React 架构的相关文章。

**然后连续点几次按钮，`hello`的渲染出现明显的卡顿，性能表现如图：**

![](https://cdn.jsdelivr.net/gh/taoliujun/taoliujun.github.io/assets/202307041608992.png)

在性能图中截取的一段时间中，黄色是脚本执行时间，灰色是 UI 渲染时间，白色是空闲时间（我停止点击了一会儿），在每帧里，要跑完所有的状态变更和 UI 渲染，`datas`系列的状态变更和渲染占据了大量的时间，基本是阻塞了`hello`的状态变更和渲染。

**只点击一次，性能表现如图：**

![](https://cdn.jsdelivr.net/gh/taoliujun/taoliujun.github.io/assets/202307041625903.png)

可以看到几个 Task，第一个 Task 就是在更新`datas`系列状态和渲染，它占据了太多帧的时间，导致`hello`的状态变更和渲染被推迟到后面的帧。

## 优化它

前面说到，React 的架构中实现了**时间切片**，它允许开发者将*不重要*的变更推迟到后面的帧，这样就可以尽量保证优先执行默认任务。使用`useTransition`改下代码：

```tsx
const [pending, startTransition] = useTransition();

const onClick1 = useCallback(() => {
  startTransition(() => {
    setDatas1(getDatas());
    setDatas2(getDatas());
    setDatas3(getDatas());
    setDatas4(getDatas());
    setDatas5(getDatas());
    setDatas6(getDatas());
    setDatas7(getDatas());
    setDatas8(getDatas());
  });
}, []);
```

**再次连续点击按钮，卡顿现象明显减轻很多，性能表现如下：**

![](https://cdn.jsdelivr.net/gh/taoliujun/taoliujun.github.io/assets/202307041629743.png)

一看起来，执行时间还是很长，那么为什么`hello`渲染看起来不卡顿呢？

**只点一次，看看性能表现：**

![](https://cdn.jsdelivr.net/gh/taoliujun/taoliujun.github.io/assets/202307041631650.png)

查看几个 Task 的详情，发现`datas`系列状态的更新，被分配在了多个 Task 中，中间还穿插了`hello`的状态更新的任务。这也印证了`useTransition`的实现背景：将不重要的任务通过时间切片架构，分配到多帧中，优先执行其他任务，从而实现不卡顿的目的。

## 注意事项

- `useTransition`是个 hook，它的返回值还包括了一个 pending 状态，用来表示是否处于时间切片的过程中，可以用来优化 UI，比如显示一个 loading。

- 你也可以使用`startTransition`这个 util 函数代替 hook 的使用。

- 时间切片架构是调度状态变化的，所以`startTransition`的入参函数里，将状态更新标记为可切片，普通的代码段不会被标记。所以简单的说，你还是得将一个状态变更的执行时间控制在 5ms 内。
