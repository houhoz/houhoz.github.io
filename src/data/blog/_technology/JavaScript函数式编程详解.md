---
title: 函数式编程
author: Leohoo
pubDatetime: 2021-07-22T15:11:26+08:00
slug: functional-programming-javascript
featured: false
draft: false
tags:
  - JavaScript
description: 函数式编程入门指南，深入理解纯函数概念，对比Lodash与原生JavaScript实现，掌握函数式编程实用技巧。
---

## Table of contents

## 定义

函数式编程就是一种抽象程度很高的编程范式，纯粹的函数式编程语言编写的函数没有变量，因此，任意一个函数，只要输入是确定的，输出就是确定的，这种纯函数我们称之为没有副作用。

### 创造一个移除所有 false（隐式类型转换为 false）类型数据的数组

```
// Underscore/Loadsh
const arr = [0, 1, false, 2, '', 3]
_.compact(arr)

// Native
arr.filter(Boolean)

```

### 返回数组 a 中不包括数组 b 的部分

```
const arrays = [
  [1, 2, 3, 4, 5],
  [5, 2, 10],
]
// Underscore/Loadsh
console.log(_.difference(arrays))
// output: [1,3,4]
// Native

arrays.reduce(function (a, b) {
  return a.filter(function (value) {
    return !b.includes(value)
  })
})

```

### 创造一个数组，按指定长度将原数组拆分成多个小数组

```
// Underscore/Loadsh
_.chunk(['a', 'b', 'c', 'd'], 2)
// => [['a', 'b'], ['c', 'd']]

_.chunk(['a', 'b', 'c', 'd'], 3)
// => [['a', 'b', 'c'], ['d']]

// Native
const chunk = (input, size) => {
  return input.reduce((arr, item, idx) => {
    return idx % size === 0
      ? [...arr, [item]]
      : [...arr.slice(0, -1), [...arr.slice(-1)[0], item]]
  }, [])
}

```
