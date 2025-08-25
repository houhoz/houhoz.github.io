---
title: React useEffect 完整使用指南
author: Leohoo
pubDatetime: 2022-09-29T15:11:26+08:00
slug: react-useeffect-usage-guide
featured: false
draft: false
tags:
  - React
description: React useEffect 完整使用指南，深入理解每次渲染的独立性、Props/State闭包特性和Effects生命周期管理机制。
---

## Table of contents

## **useEffect 完整指南**

[useEffect 完整指南](https://overreacted.io/zh-hans/a-complete-guide-to-useeffect/)

## **每一次渲染都有它自己的 Props and State**

> 当我们更新状态的时候，React 会重新渲染组件。每一次渲染都能拿到独立的count  状态，这个状态值是函数中的一个常量。
>

## **每一次渲染都有它自己的事件处理函数**

> 我们发现count在每一次函数调用中都是一个常量值。值得强调的是 — 我们的组件函数每次渲染都会被调用，但是每一次调用中count值都是常量，并且它被赋予了当前渲染中的状态值。
>

所以实际上，每一次渲染都有一个“新版本”的`handleAlertClick`。每一个版本的`handleAlertClick`“记住” 了它自己的  `count`：

## **每次渲染都有它自己的 Effects**

我们已经知道`count`是某个特定渲染中的常量。事件处理函数“看到”的是属于它那次特定渲染中的`count`状态值。对于 effects 也同样如此：

**并不是`count`的值在“不变”的 effect 中发生了改变，而是*effect 函数本身*在每一次渲染中都不相同。**

每一个 effect 版本“看到”的`count`值都来自于它属于的那次渲染：

## **每一次渲染都有它自己的…所有**

React 只会在[浏览器绘制](https://medium.com/@dan_abramov/this-benchmark-is-indeed-flawed-c3d6b5b6f97f)后运行 effects。这使得你的应用更流畅因为大多数 effects 并不会阻塞屏幕的更新。Effect 的清除同样被延迟了。**上一次的 effect 会在重新渲染后被清除：**

effect 的清除并不会读取“最新”的 props。它只能读取到定义它的那次渲染中的 props 值：

## **同步， 而非生命周期**
