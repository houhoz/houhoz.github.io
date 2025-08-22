---
title: 使用cloudflare规则 重写端口
author: Leohoo
pubDatetime: 2024-04-25 15:11:26
slug: cloudflare-port-rewrite-rules
featured: false
draft: false
tags:
  - Cloudflare
description: 使用Cloudflare Origin Rules实现端口重写的完整指南，替代传统Nginx转发方案，包含SSL证书配置。
---

## Table of contents

## 使用[cloudflare](https://so.csdn.net/so/search?q=cloudflare&spm=1001.2101.3001.7020)规则 重写端口

如果想实现访问使用 [test.example.com](http://test.example.com/) 访问 www.example.com:4567

之前的都是使用Nginx 转发

现在可以使用cloudFlare Origin Rules 实现代理

## 添加一个CNAME记录

[test.example.com](http://test.example.com/)`to`[www.example.com](https://www.notion.so/leohoo/www.example.com)

## 添加一个Origin Rules

在`Cloudflare`的`域名`的控制面板里找到`规则`→`Origin Rules`

添加一个规则 `主机名`等于`test.example.com` `端口`重写到`4567`

### SSL

如果提示SSL错误的话需要要生成一个 `Cloudflare` 签名的 `TLS 证书`

在`Cloudflare`的`域名`的控制面板里找到`SSL/TSL`→`源服务器` 创建一个证书部署到服务器就可以了
