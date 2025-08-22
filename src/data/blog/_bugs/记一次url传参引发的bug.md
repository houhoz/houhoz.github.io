---
title: 记一次url传参引发的bug
author: Leohoo
pubDatetime: 2021-12-24 15:11:26
slug: url-parameter-bug-taro-webview
featured: false
draft: false
tags:
  - Taro
  - 前端踩坑
description: Taro小程序WebView URL传参踩坑记录，详解encodeURIComponent与encodeURI的区别及正确的URL编解码方式。
---

## Table of contents

## bug 复现

小程序开发的时候我们之前需要跳转第三方的 webview 页面，一般传参数的时候

```
Taro.navigateTo({
  url: `/pages/webview/index?url=url`,
})

```

这个时候我们在 webview 页面获取 url 的参数

```
const url = Taro.getCurrentInstance().router?.params?.url || ''

```

url 后面没有添加参数，获取的时候也是没什么问题，但是 url 后面有参数，
诸如https://bot.biyouxinli.com/lianxin-botserver/openApi/gotoPsyBot?channel=TXHBOT&chnlCode=8BFE42DF24C45563B5EA6E82C67AF8139EC3757CFF1FC3E322D5CE2F41F97C407B021F83A6EF1ED3DD65CEE6&unionId=
我们再像上面的方式获取 url，就会出现错误，拿不到原始完整的 url

## encodeURI,encodeURIComponent 有什么区别?

它们都是编码 URL，唯一区别就是编码的字符范围，其中

> encodeURI 方法不会对下列字符编码 ASCII 字母 数字 ~!@#$&()=:/,;?+'
encodeURIComponent 方法不会对下列字符编码 ASCII 字母 数字 ~!()'
>

所以 encodeURIComponent 比 encodeURI 编码的范围更大。

实际例子来说，encodeURIComponent 会把 http:// 编码成 http%3A%2F%2F 而 encodeURI 却不会。

## webview 传参 URL 正确的方式

```
// 编码
Taro.navigateTo({
  url: `/pages/webview/index?url=${encodeURIComponent(live)}`,
})
// 解码
const url = decodeURIComponent(
  Taro.getCurrentInstance().router?.params?.url || ''
)

```
