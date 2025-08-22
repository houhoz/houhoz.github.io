---
title: Linux 服务器初始化配置完整指南
author: Leohoo
pubDatetime: 2024-10-01 13:36:42
slug: linux-server-initialization-complete-guide
featured: false
draft: false
tags:
  - Linux
description: Linux服务器初始化配置指南，包含服务器测评脚本、系统重装和哪吒探针安装等实用工具分享。
---

## 1. 服务器性能测评

新服务器到手第一件事当然是跑个分！虽然有些参数看不太懂，但看数据总是让人安心的。

推荐使用融合怪测评脚本，功能全面且更新及时：

**脚本特点**：
- 综合测试CPU、内存、硬盘、网络等性能
- 支持多种测试模式
- 输出详细的性能报告

```bash
curl -L https://gitlab.com/spiritysdx/za/-/raw/main/ecs.sh -o ecs.sh && chmod +x ecs.sh && bash ecs.sh
```

> 📝 **脚本地址**：[spiritLHLS/ecs](https://github.com/spiritLHLS/ecs)

## 2. 系统重装与优化

跑分只是参考，重装系统主要是为了方便后续管理和确保系统的纯净性。

**DD脚本推荐**：
- 支持多种Linux发行版
- 自动化安装过程
- 可自定义系统配置

> 🔧 **DD工具**：[leitbogioro/Tools](https://github.com/leitbogioro/Tools)

## 3. 安装监控面板

服务器监控必不可少，哪吒探针是个不错的选择，界面简洁且功能实用。

**哪吒探针特色**：
- 实时监控服务器状态
- 支持多服务器管理
- 美观的Web界面
- 告警通知功能

**我的面板地址**：
- 🏠 [个人首页](https://home.leoho.dev)
- 📊 [哪吒面板](https://dash.leoho.dev)

## 小结

以上三步是我每次新开服务器的必做清单，虽然简单但很实用。后续还会根据具体用途安装Docker、配置防火墙、设置定时任务等。

有什么好用的服务器管理工具欢迎分享！
