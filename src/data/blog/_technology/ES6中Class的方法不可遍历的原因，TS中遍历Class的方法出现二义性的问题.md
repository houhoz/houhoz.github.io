---
title: ES6中Class的方法不可遍历的原因，TS中遍历Class的方法出现二义性的问题
author: Leohoo
pubDatetime: 2023-06-22 15:11:26
slug: es6-class-enumeration-typescript-compilation-differences
featured: false
draft: false
tags:
  - JavaScript
description: 深入分析ES6 Class方法不可枚举的设计原理，以及TypeScript编译到ES5/ES6时产生的行为差异和解决方案。
---

## Table of contents

# 前言

今天写了一段 TS 代码，发现编译成 ES5 和 ES6 时的执行结果居然不同，花了点时间学习了一下，总结如下。

# 问题

在 TS 代码中，使用 in 操作符遍历对象成员，将代码编译成 ES5 和 ES6 时，结果是不同的。

# 原因

ES6中 Class 的所有非静态方法虽然是定义到原型对象上的，但是却是不可遍历的，源码如下

```jsx
// 为 target 对象添加属性
function _defineProperties(target, props) {
    for (var i = 0; i < props.length; i++) {
        var descriptor = props[i]
        // enumerable 默认为 false
        descriptor.enumerable = descriptor.enumerable || false
        descriptor.configurable = true
        if ('value' in descriptor) descriptor.writable = true
        Object.defineProperty(target, descriptor.key, descriptor)
    }
}

// 为 Constructor 添加原型属性或者静态属性
function _createClass(Constructor, protoProps, staticProps) {
    // 如果是原型属性，添加到原型对象上
    if (protoProps) _defineProperties(Constructor.prototype, protoProps)
    // 如果是静态属性，添加到构造函数上
    if (staticProps) _defineProperties(Constructor, staticProps)
    return Constructor
}

```

可以看到 descriptor.enumerable = descriptor.enumerable || false 这句代码设置了属性默认是不可遍历的，如果我们没有设置，属性默认不可遍历。

in 操作符默认是可以遍历原型对象的成员的，但是下边代码不会有任何输出，因为原型对象的成员被设置了不可遍历。

```jsx
class Person {
    say() {
        console.log("hello");
    }
}
let p = new Person();
for (const key in p) {
    console.log(key);
}

```

构造函数则没有这个问题，可以被遍历到。

```jsx
function Person() {}
Person.prototype.say = function() {};
let p = new Person();
for (const key in p) {
    console.log(key); // say
}

```

这就导致了我遇到的问题，编译的 ECMAScript 版本不同，代码可能会有不同的运行结果。

```jsx
class Person {
  say() {
    console.log("hello");
  }
}
let p = new Person();
for (const key in p) {
  console.log(key);
}

```

上边代码，如果使用默认设置编译为 ES5 时，会输出 say，编译后的代码如下：
可以看到，其实是返回了一个构造函数，并设置了构造函数的原型。

```
var Person = /** @class */ (function () {
    function Person() {
    }
    Person.prototype.say = function () {
        console.log("hello");
    };
    return Person;
}());
var p = new Person();
for (var key in p) {
    console.log(key);
}

```

而如果设置编译为 ES6，则不会有输出，编译后的代码如下（和编译前相同）：

```jsx
class Person {
  say() {
    console.log("hello");
  }
}
let p = new Person();
for (const key in p) {
  console.log(key);
}

```
