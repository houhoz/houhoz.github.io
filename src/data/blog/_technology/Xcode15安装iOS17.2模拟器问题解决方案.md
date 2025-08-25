---
title: Xcode15 安装 iOS17.2 模拟器问题解决方案
author: Leohoo
pubDatetime: 2023-12-22T15:11:26+08:00
slug: xcode15-ios17-simulator-solution
featured: false
draft: false
tags:
  - React Native
description: 解决Xcode15中iOS 17.2模拟器下载失败问题的完整方案，包含手动下载和命令行安装步骤。
---

iOS 17.0 Simulator在下载过程中，频繁失败，也无法断点下载。这时可以手动下载，手动安装。

[登录 - Apple](https://developer.apple.com/download/all/)

点击[iOS 17 Simulator Runtime.dmg](https://download.developer.apple.com/Developer_Tools/iOS_17_Simulator_Runtime/iOS_17_Simulator_Runtime.dmg)下载。使用浏览器下载，支持断点续传。

下载成功后，打开终端，使用下面命令安装iOS 17.2 Simulator。

```bash
xcode-select -s /Applications/Xcode.app
xcodebuild -runFirstLaunch
xcrun simctl runtime add "~/Downloads/iOS_17.2_Simulator_Runtime.dmg"

```

安装成功后，去Xcode设置中就可以看到iOS 17.2 Simulator了。

这个命令比较费时，三五分钟，耐心等待。

安装成功后，去Xcode设置中就可以看到iOS 17.2 Simulator了。
