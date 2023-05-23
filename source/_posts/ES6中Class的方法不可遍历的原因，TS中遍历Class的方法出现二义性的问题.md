---
title: ES6中Class的方法不可遍历的原因，TS中遍历Class的方法出现二义性的问题
date: 2023-05-23 21:45:29
tags:
  - JavaScript
categories:
  - Note
cover:
feature: false
---

# 前言

今天写了一段Ts代码，发现编译成ES5和ES6时的执行结果居然不同，花了点时间学习了一下，总结如下。

# 问题

在ts代码中，使用in操作符遍历对象成员，将代码编译成ES5和ES6时，结果是不同的。

# 原因

es6中class的所有非静态方法虽然是定义到原型对象上的，但是却是不可遍历的，源码如下


```js
// 为target对象添加属性
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

可以看到descriptor.enumerable = descriptor.enumerable || false这句代码设置了属性默认是不可遍历的，如果我们没有设置，属性默认不可遍历。

in操作符默认是可以遍历原型对象的成员的，但是下边代码不会有任何输出，因为原型对象的成员被设置了不可遍历。

```js
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

```js
function Person() {}
Person.prototype.say = function() {};
let p = new Person();
for (const key in p) {
    console.log(key); // say
}
```

这就导致了我遇到的问题，编译的ECMAScript版本不同，代码可能会有不同的运行结果。

```js
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

上边代码，如果使用默认设置编译为ES5时，会输出say，编译后的代码如下：
可以看到，其实是返回了一个构造函数，并设置了构造函数的原型。


```js
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

而如果设置编译为ES6，则不会有输出，编译后的代码如下（和编译前相同）：

```js
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