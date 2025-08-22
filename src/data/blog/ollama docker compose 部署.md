---
title: ollama docker compose 部署
author: Leohoo
pubDatetime: 2025-03-25 10:50:00
slug: ollama-docker-compose-deployment
featured: false
draft: false
tags:
  - Docker
  - Ollama
description: 使用Docker Compose部署Ollama大语言模型服务的完整指南，包含配置文件和使用示例。
---

## Table of contents

## 介绍

Ollama是一个强大的大语言模型运行和管理工具，通过Docker Compose可以快速部署Ollama服务，实现本地AI模型的运行和管理。

## Docker Compose 配置

创建 `docker-compose.yml` 文件：

```yaml
version: '3.8'

services:
  ollama:
    image: ollama/ollama:latest
    container_name: ollama
    restart: unless-stopped
    volumes:
      - ollama_data:/root/.ollama
    ports:
      - "11434:11434"
    # 如果需要 GPU 支持，取消注释以下行
    # deploy:
    #   resources:
    #     reservations:
    #       devices:
    #         - driver: nvidia
    #           count: all
    #           capabilities: [gpu]

volumes:
  ollama_data:
    driver: local
```

## 基本使用

### 启动服务

```bash
# 启动 Ollama 服务
docker-compose up -d

# 查看服务状态
docker-compose ps

# 查看日志
docker-compose logs -f ollama
```

### 运行模型

```bash
# 进入容器
docker exec -it ollama bash

# 运行模型（在容器内）
ollama run deepseek-r1:1.5b

# 或者直接在宿主机执行
docker exec -it ollama ollama run deepseek-r1:1.5b
```

### API 调用

```bash
# 生成文本
curl -X POST http://localhost:11434/api/generate \
  -H "Content-Type: application/json" \
  -d '{
    "model": "deepseek-r1:8b",
    "prompt": "你好，请介绍一下自己",
    "stream": false
  }'

# 聊天对话
curl -X POST http://localhost:11434/api/chat \
  -H "Content-Type: application/json" \
  -d '{
    "model": "deepseek-r1:8b",
    "messages": [
      {
        "role": "user",
        "content": "你好"
      }
    ],
    "stream": false
  }'
```

### 模型管理

```bash
# 拉取模型
docker exec -it ollama ollama pull deepseek-r1:8b

# 查看已安装模型
docker exec -it ollama ollama list

# 删除模型
docker exec -it ollama ollama rm deepseek-r1:8b
```

## 常用命令

```bash
# 停止服务
docker-compose down

# 重启服务
docker-compose restart

# 更新镜像
docker-compose pull
docker-compose up -d

# 完全清理（包括数据）
docker-compose down -v
```

## 总结

通过Docker Compose部署Ollama服务，可以快速搭建本地AI模型运行环境。这种方式便于管理、易于扩展，是实现本地AI应用开发的理想选择。
