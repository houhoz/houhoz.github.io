---
title: React Hooks 开发指南
author: Leohoo
pubDatetime: 2021-10-25T15:11:26+08:00
slug: react-hooks-development-guide
featured: false
draft: false
tags:
  - React
description: React Hooks实用指南，深入解析useMemo、useCallback、useRef性能优化技巧，理解记忆化机制提升组件渲染效率。
---

## Table of contents


## 参考
https://juejin.cn/post/7101486767336849421#heading-19

https://codesandbox.io/s/peaceful-tdd-pf3nzy?file=/src/App.tsx

> hooks：目的是增加代码的可复用性、逻辑性，最主要的是解决了函数式组件无状态的问题，这样既保留了函数式的简单，又解决了没有数据管理状态的缺陷
>

### useMemo

当一个父组件中调用了一个子组件的时候，父组件的 state 发生变化，会导致父组件更新，而子组件虽然没有发生改变，但也会进行更新。

简单的理解下，当一个页面内容非常复杂，模块非常多的时候，函数式组件会**从头更新到尾**，只要一处改变，所有的模块都会进行刷新，这种情况显然是没有必要的。

我们理想的状态是各个模块只进行自己的更新，不要相互去影响，那么此时用 `useMemo` 是最佳的解决方案。

**只要父组件的状态更新，无论有没有对自组件进行操作，子组件都会进行更新**， `useMemo` 就是为了防止这点而出现的

---

### memo

`memo` 的作用是**结合了pureComponent纯组件和 componentShouldUpdate功能**，会对传入的props进行一次对比，然后根据第二个函数返回值来进一步判断哪些props需要更新。

`useMemo` 与 `memo` 的理念上差不多，都是判断是否满足当前的限定条件来决定是否执行 `callback` 函数，而 `useMemo` 的第二个参数是一个数组，通过这个数组来判定是否更新回掉函数

### `useMemo` 的好处：

- 可以减少不必要的循环和不必要的渲染
- 可以减少子组件的渲染次数
- 通过特地的依赖进行更新，可以避免很多不必要的开销，但要注意，有时候在配合 `useState` 拿不到最新的值，这种情况可以考虑使用 `useRef` 解决

---

### useCallback

`useCallback` 与 `useMemo` 极其类似,可以说是一模一样，唯一不同的是 `useMemo` 返回的是 ，而 `useCallback` 返回的是**函数**函数运行的结果

<aside>
💡 **注意：这个函数是父组件传递子组件的一个函数，防止做无关的刷新，其次，这个组件必须配合 `memo` ,否则不但不会提升性能，还有可能降低性能**

</aside>

### useRef

**useRef**可以获取当前元素的所有属性，并且返回一个可变的ref对象，并且这个对象**只有current属性**，可设置initialValue

- 通过useRef获取对应的属性值
- 缓存数据
- useLatest
- 结合useMemo和useRef封装useCreation

### 在 React 中，下面三个概念容易搞混：

- React.memo
- React.useMemo
- React.useCallback

**记忆化（Memoization）：**

简单来讲，记忆化的意思就是缓存函数的计算结果，当下次使用相同的参数调用该函数时，直接返回缓存值而不需要执行这个函数了。或者缓存某个变量，当依赖项未发生变化时，直接返回原来的变量，否则将缓存设置为新变量。
