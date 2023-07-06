---
title: useDeferredValue在性能优化中的使用
url: React/useDeferredValue
date: 2022-09-01
tags:
  - react
  - useDeferredValue
categories:
  - [React]
---

## 一个卡顿场景

已知浏览器在一帧时间里（默认 16.6 毫秒）要完成好多工作，其中最耗时的是 js 脚本执行和页面渲染。如果 js 脚本耗时太长，那要引起页面渲染掉帧，在用户的体验上就是卡顿。

这里有一个处理用户输入的搜索词语，将结果渲染到一个 dom 列表上的场景：

```tsx
import { FC, useMemo, useState } from "react";

const SearchResults: FC<{ query: string }> = ({ query }) => {
  const datas = useMemo(() => {
    return new Array(10000).fill(null).map(() => {
      return `${query} ${Math.random()}`;
    });
  }, [query]);

  if (!query) {
    return null;
  }

  return (
    <div>
      <h2>search "{query}" list:</h2>
      {datas.map((v, k) => {
        return <p key={k}>{v}</p>;
      })}
    </div>
  );
};

export const Main: FC = () => {
  const [query, setQuery] = useState("");

  return (
    <>
      Search:
      <input
        value={query}
        onChange={(e) => {
          setQuery(e.target.value);
        }}
      />
      <SearchResults query={query} />
    </>
  );
};
```

当用户每次输入一个字符，就会触发`SearchResults`组件的重新渲染，这个渲染包括`datas`的重新计算，和 dom 结构的重新渲染，这个时间远远超过 16 毫秒，会导致下一个输入值的处理任务一直在等待中，造成卡顿。

## useDeferredValue

React 提供了`时间切片`的模式，这里不详细展开了，允许你在调度任务的过程中安排高优先级的任务，而`useDeferredValue`就是这个模式的一个 hook，它可以**延迟更新部分 UI**

在之前的代码中，我们稍作修改：

```tsx
//...

const [query, setQuery] = useState("");
const defreredQuery = useDeferredValue(query);

//...
<SearchResults query={defreredQuery} />;
```

当用户**快速**输入一个字符时，`SearchResults`组件的渲染就会被延迟，这样就尽量减少卡顿了。

`useDeferredValue`通过延迟状态的更新来实现这个目的，它不同于节流或防抖的固定时间控制，而是根据一系列复杂调度算法来决定延迟的时间，这样可以尽量减少卡顿的发生。
