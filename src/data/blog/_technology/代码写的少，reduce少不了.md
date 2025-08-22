---
title: 代码写的少，reduce少不了
author: Leohoo
pubDatetime: 2021-04-22 15:11:26
slug: javascript-reduce-practical-guide
featured: false
draft: false
tags:
  - JavaScript
description: JavaScript reduce方法实战宝典，从API解析到8种实用场景，掌握数组求和、转换、分组等高效操作，让你的代码更简洁优雅。
---

## Table of contents

## reduce api

`array.reduce(function(accumulator, currentValue, index, array), initialValue);`

reduce 函数接收4个参数:

- Accumulator (acc) (累计器)
- Current Value (cur) (当前值)
- Current Index (idx) (当前索引)
- Source Array (src) (源数组)

reducer 函数的返回值分配给累计器，该返回值在数组的每个迭代中被记住，并最后成为最终的单个结果值。

## 应用

### 数组求和

```jsx
// const arr = [12, 34, 23];
// const sum = arr.reduce((acc, cur) => acc + cur);
// const sum = arr.reduce((acc, cur) => acc + cur, 10);
var result = [
  { subject: "math", score: 88 },
  { subject: "chinese", score: 95 },
  { subject: "english", score: 80 },
];
// const sum = result.reduce((accumulator, cur) => accumulator + cur.score, 0);
const sum = result.reduce(
  (accumulator, cur) => accumulator + cur.score,
  -10
);
console.log(sum);
```

### 数组最大值

```jsx
// 数组最大值
const a = [12, 34, 23, 6];
const max = a.reduce((acc, cur) => (acc > cur ? acc : cur));
console.log(max);
```

### 数组转字符串

```jsx
const objArr = [{ name: "老大" }, { name: "老二" }, { name: "老三" }];
const res = objArr.reduce((acc, cur, index, arr) => {
  if (index === 0) {
    return cur.name;
  } else if (index === arr.length - 1) {
    return acc + "和" + cur.name;
  } else {
    return acc + "、" + cur.name;
  }
}, "");
console.log(res);
```

### 求字符串中字母出现的次数

```jsx
const str = "sfhjasfjgfasjuwqrqadqeiqsajsdaiwqdaklldflas-cmxzmnha";
const res1 = str.split("").reduce((acc, cur) => {
  acc[cur] ? acc[cur]++ : (acc[cur] = 1);
  return acc;
}, {});
console.log(res1);
```

### 数组转数组

```jsx
var arr1 = [2, 3, 4, 5, 6]; // 每个值的平方
const res2 = arr1.reduce((acc, cur) => {
  acc.push(cur * cur);
  return acc;
}, []);
console.log(res2);
```

### 数组转对象

```jsx
var streams = [
  { name: "技术", id: 1 },
  { name: "设计", id: 2 },
];

var obj = streams.reduce((acc, cur) => {
  acc[cur.id] = cur;
  return acc;
}, {});
console.log(obj);
```

### 多维的叠加执行操作

```jsx
var result1 = [
  { subject: "math", score: 88 },
  { subject: "chinese", score: 95 },
  { subject: "english", score: 80 },
];
var dis = {
  math: 0.5,
  chinese: 0.3,
  english: 0.2,
};

const res3 = result1.reduce((acc, cur) => dis[cur.subject] * cur.score + acc, 0)
console.log(res3);
```

### Promise for循环

```jsx
const ajax = () => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      const randomNum = Math.random();
      console.log(randomNum);
      if (randomNum > 0.5) {
        resolve(true);
      } else {
        resolve(false);
      }
    }, 1000);
  });
};
const list = [1, 2]

list.reduce(async(previousValue, currentValue) => {
  await previousValue
  return ajax(currentValue)
}, Promise.resolve())

//不用await
list.reduce((previousValue, currentValue) => {
   return previousValue.then(() => ajax(currentValue));
}, Promise.resolve());

async function runPromiseByQueue() {
  //使用await 同步执行promise
  for (let value of list) {
    await ajax();
  }
}
runPromiseByQueue()

for (let value of list) {
  // 不使用await，异步执行promise
  ajax();
}
```

### 按属性分组

```jsx
let obj = [
  {name: 'Alice', job: 'Data Analyst', country: 'AU'},
  {name: 'Bob', job: 'Pilot', country: 'US'},
  {name: 'Lewis', job: 'Pilot', country: 'US'},
  {name: 'Karen', job: 'Software Eng', country: 'CA'},
  {name: 'Jona', job: 'Painter', country: 'CA'},
  {name: 'Jeremy', job: 'Artist', country: 'SP'},
]
let ppl = obj.reduce((group, curP) => {
  let newkey = curP['country']
  if(!group[newkey]){
    group[newkey]=[]
  }
  group[newkey].push(curP)
  return group
}, [])
```
