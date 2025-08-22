---
title: docker 打包优化
author: Leohoo
pubDatetime: 2024-08-12 13:40:13
slug: docker-build-optimization
featured: false
draft: false
tags:
  - Docker
description: Docker容器化前端应用的最佳实践，包含构建缓存优化、多阶段构建和镜像体积压缩等实用技巧。
---

### **单页应用的静态资源**

**所有的前端单页应用对于部署，最重要的就是两点:**

1. 静态资源如何构建: 大部分项目都是 `npm run build`。
2. 静态资源目录在哪: 有的项目是 `/dist`，有的项目是 `/build`。**CRA 是 `/build` 目录**。

### **Dockerfile**

1. 选择一个基础镜像。由于需要构建，需要 node 的运行环境，因此选择 node。
2. 将以上几个脚本命令放在 RUN 指令中。
3. 启动服务命令放在 CMD 指令中。

```docker
FROM node:14-alpine

WORKDIR /code

ADD . .
RUN yarn && npm run build

CMD npx serve -s build
EXPOSE 3000
```

构建完成。然而还可以针对以下两点进行优化。

1. 构建镜像时间过长，**优化构建时间**
2. 构建镜像大小过大，**优化镜像体积**

### **构建时间优化: 构建缓存**

在 Dockerfile 中，对于 `ADD` 指令来讲，如果**添加文件内容的 `checksum` 没有发生变化，则可以利用构建缓存**。

而对于前端项目而言，如果 `package.json/yarn.lock` 文件内容没有变更，则无需再次 `npm i`。

将 `package.json/yarn.lock`事先置于镜像中，安装依赖将可以获得缓存的优化，优化如下。

```docker
FROM node:14-alpine as builder

WORKDIR /code

# 单独分离 package.json，是为了安装依赖可最大限度利用缓存
COPY ["package.json", "yarn.lock", "./"]
# 此时，yarn 可以利用缓存，如果 yarn.lock 内容没有变化，则不会重新依赖安装
RUN yarn

ADD . .
RUN npm run build

CMD npx serve -s build
EXPOSE 3000
```

### **构建体积优化: 多阶段构建**

```docker
FROM node:14-alpine as builder

WORKDIR /code

# 单独分离 package.json，是为了安装依赖可最大限度利用缓存
COPY ["package.json", "yarn.lock", "./"]
RUN yarn

ADD . .
RUN npm run build

# 选择更小体积的基础镜像
FROM nginx:alpine
COPY --from=builder /code/build /usr/share/nginx/html

```
