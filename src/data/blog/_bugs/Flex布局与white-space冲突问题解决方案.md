---
title: Flex布局与white-space冲突问题解决方案
author: Leohoo
pubDatetime: 2022-02-22 15:11:26
slug: flex-layout-white-space-conflict-solution
featured: false
draft: false
tags:
  - CSS
  - 前端踩坑
description: 解决Flex布局与white-space:nowrap冲突问题的实战记录，详解文本省略号在弹性布局中的正确实现方式。
---

## Table of contents

## 工作中经常会用到css文本超出显示省略号

```css
overflow:hidden; //超出的文本隐藏
text-overflow:ellipsis; //溢出用省略号显示
white-space:nowrap; //溢出不换行
```

但是当display:flex;碰上white-space:nowrap;的时候，会打乱flex布局。

### 问题再现:

https://codepen.io/houhoz/pen/gOmWPNo

预期结果应该是左边header头像完整展示，右边的文字出现超出展示省略号。

仔细分析代码，理论上确实是可以实现。

带着疑惑再一次去MDN看flex的文档，然后发现

由于我们设置了 white-space:nowrap 导致content 的内容超出了flex项目的剩余宽度，我们期望content 元素的内容区域缩小，按照MDN的说法，需要给content元素设置min-width属性

### 问题解决:

- content 设置min-width: 0;
- content 设置overflow: hidden;

https://codepen.io/houhoz/pen/NWpjNdo

flex:1 到底什么含义

flex属性是flex-grow，flex-shrink和flex-basis的缩写。

- Flex-grow: 指定了flex容器中剩余空间的多少应该分配给该项目。省略时默认值为 1。 (初始值为 0)
- Flex-shrink: 属性指定了 flex 元素的收缩规则。flex 元素仅在默认宽度之和大于容器的时候才会发生收缩，其收缩的大小是依据 flex-shrink 的值。 省略时默认值为1。 (初始值为 1)
- Flex-basis: 指定了 flex 元素在主轴方向上的初始大小。若值为0，则必须加上单位，以免被视作伸缩性。省略时默认值为 0。(初始值为 auto)

```css
flex: 1;
flex: 1 1 0%;
/* flex: 1, 它表示flex项目扩展并填充可用空间。 */
```

另一种关于左边内容固定尺寸，右边超出省略号的布局

可以直接对左边内容设置

`flex: 0 0 40px;`

### 参考文章:

1. 关于display:flex碰上white-space nowrap的问题 - SegmentFault 思否
2.  https://codepen.io/aj-foster/pen/emBYPW
3.  CSS flex属性深入理解 « 张鑫旭-鑫空间-鑫生活
4. 深入了解 Flex 属性
