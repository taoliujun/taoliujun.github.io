---
title: 从Radix看如何优雅的写组件
url: 前端/write_component_with_radix
date: 2023-06-27
tags:
  - radix
  - 组件
categories:
  - [架构]
  - [React]
---

## 组件的困扰

在更早以前，网页应用很简单，开发者自己维护简单的交互组件，通过入参去控制组件有不一样的渲染、样式、交互。后来 React/Vue 等框架的到来，使网页应用变得愈发复杂，于是使用 Element、Ant、Mui 之类的 UI 组件来提高开发效率，但使用后发现一些问题，其中常见的是：

1. 难以改变样式。
1. 几乎不能改变 dom 结构。

虽然很多组件库提供了 css variables、less/sass 包等方式让人们来定义样式，但离精细定义差些距离。而且固化的组件 dom 结构让开发者难以完成产品、UI 设计师要求的定制化二次开发。

于是部分人开始从 0 或基于更基础的 UI 组件来开发自定义组件以满足自定义样式和 dom 结构的目的，但问题是维护这些基础组件又是一个麻烦，或者说这些组件放到另一个项目中，是否又需要是另一个 dom 结构？

于是，有开源项目在解决这样的事，比较出众的是 Mui-Base、Radix，它们都旨在提供一套 Uncontrolled、Unstyled、Opened 的基础组件以便大家二次封装。我在阅读实践了这两个方案后，选择了使用 Radix 继续深入下去，主要是因为 Radix 将组件拆分的更为精细。

## Radix 的理念

它提出了几个特性，一起看看吧。

### Accessible 可访问性

如果你的应用对可访问性有要求，那 Radix 会有很大帮助，它遵循了一个 `WAI-ARIA` 的设计规范。但我对可访问性的研究不多，在此不表了。

### Unstyled 无样式

顾名思义的是，它提供的组件都是不带任何样式的，你可以使用任意样式方案去开发。这也解决了上文提到的重要问题：自定义样式。

### Opened 开放

我认为 Radix 的开发性做到了优秀的程度，它的每个组件都太颗粒了，你可以精细的自由搭配，组装成想要的。

### Incremental 渐进的

可以只安装我们需要用到的组件库，比如`npm install @radix-ui/react-tooltip`。

## 一个小例子

我们要做一个 tooltip，如果自己写，需要考虑样式、状态控制、可访问性等。但是在 Radix 里，采用简单的封装和自定义样式即可：

```typescript
import * as Popover from "@radix-ui/react-popover";
import { FC } from "react";

export const Main: FC = () => {
  return (
    <Popover.Root>
      <Popover.Trigger>点击我</Popover.Trigger>
      <Popover.Portal>
        <Popover.Content sideOffset={5}>
          要显示的内容
          <Popover.Arrow />
        </Popover.Content>
      </Popover.Portal>
    </Popover.Root>
  );
};
```

在上面的代码中，组装 Radix 提供的 popover 组件的颗粒，不加任意 className 就得到一个简单的 tooltip，并且"点击我"可进行交互。如图：

![](https://cdn.jsdelivr.net/gh/taoliujun/taoliujun.github.io/assets/202306271427990.png)

接着，我随便加了点 tailwindcss。

```typescript
return (
  <Popover.Root>
    <Popover.Trigger className="text-red">点击我</Popover.Trigger>
    <Popover.Portal>
      <Popover.Content
        className="border border-solid border-black"
        sideOffset={5}
      >
        要显示的内容
        <Popover.Arrow />
      </Popover.Content>
    </Popover.Portal>
  </Popover.Root>
);
```

就得到如图的效果：

![](https://cdn.jsdelivr.net/gh/taoliujun/taoliujun.github.io/assets/202306271430207.png)

Radix 的组件将几乎所有的 state 变化，反馈到`data-state`上方便你精细的定制样式了。

## 其他

Radix 提供了很多小细节让你可以优雅的封装组件，不限于：

- 提供`forceMount`以方便你的组件能运行完整的动画。
- 提供`asChild`以方便你更精细的自定义 dom。
- 支持 SSR。
- 提供了许多现成组件，并且足够颗粒化，让你更快更优化的定制出自己的组件哟。

相比较使用它，理解它的理念才能消化为自己的知识，个人认为即便是自己开发的基础组件，以精细颗粒控制为目标，也不失为一个很棒的思路。

## 参考

- https://www.radix-ui.com/
