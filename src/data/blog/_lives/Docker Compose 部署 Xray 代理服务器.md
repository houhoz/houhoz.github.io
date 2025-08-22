---
title: Docker Compose 部署 Xray 代理服务器
author: Leohoo
pubDatetime: 2025-08-23T00:24:48+08:00
slug: docker-compose-deploy-xray
featured: true
draft: false
tags:
  - Docker
  - Xray
description: 使用 Docker Compose 部署 Xray 代理服务器的完整指南，包含 VLESS+Reality 和 WebSocket 配置示例，支持多种协议和加密方式
---

## 前置准备

### 生成必要的密钥和 ID

**生成 UUID（客户端 ID）：**
```bash
docker run --rm ghcr.io/xtls/xray-core:latest uuid
```

**生成 Reality 密钥对：**
```bash
docker run --rm ghcr.io/xtls/xray-core:latest x25519
```

**生成 shortId（客户端标识，可选）：**
```bash
openssl rand -hex 8
```

## 部署配置

### Docker Compose 文件

创建 `compose.yml` 文件：

```yaml
version: '3.8'
services:
  xray:
    image: ghcr.io/xtls/xray-core:latest
    container_name: xray
    restart: unless-stopped
    command: ["run", "-c", "/usr/local/etc/xray/config.json"]
    volumes:
      - ./config.json:/usr/local/etc/xray/config.json:ro
    ports:
      - "443:443/tcp"
      - "6080:6080/tcp"
```

### Xray 配置文件

创建 `config.json` 文件，包含两个入站连接：

```json
{
  "log": {
    "loglevel": "warning"
  },
  "inbounds": [
    {
      "listen": "0.0.0.0",
      "port": 443,
      "protocol": "vless",
      "settings": {
        "clients": [
          {
            "id": "你的UUID",
            "flow": "xtls-rprx-vision"
          }
        ],
        "decryption": "none"
      },
      "streamSettings": {
        "network": "tcp",
        "security": "reality",
        "realitySettings": {
          "show": false,
          "dest": "m.media-amazon.com:443",
          "xver": 0,
          "serverNames": [
            "m.media-amazon.com",
            "media-amazon.com"
          ],
          "privateKey": "你的私钥",
          "shortIds": [
            "",            // 空字符串表示客户端可不配置 shortId
            "你的shortId"   // 用于标识不同客户端，长度为 2-16 位偶数
          ]
        }
      }
    },
    {
      "listen": "0.0.0.0",
      "port": 6080,
      "protocol": "vless",
      "settings": {
        "clients": [
          {
            "id": "你的UUID"
          }
        ],
        "decryption": "none"
      },
      "streamSettings": {
        "network": "ws",
        "security": "none",
        "wsSettings": {
          "path": "/lovelive"
        }
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    }
  ],
  "dns": {
    "servers": [
      "1.1.1.1",
      "8.8.8.8",
      "223.5.5.5"
    ]
  }
}
```

## 配置说明

### 协议配置

1. **VLESS + Reality（端口 443）**：使用 Reality 协议伪装成正常 HTTPS 流量
2. **VLESS + WebSocket（端口 6080）**：基于 WebSocket 的连接方式

### Reality 推荐目标站点

选择稳定的 HTTPS 网站作为伪装目标：
- `www.amazon.com`
- `addons.mozilla.org`
- `www.un.org`
- `www.tesla.com`
- `github.com`
- `www.cloudflare.com`

## 部署步骤

1. 生成必要的密钥和 ID
2. 创建配置文件并替换相应的值
3. 启动服务：
```bash
docker-compose up -d
```

4. 查看日志：
```bash
docker-compose logs -f xray
```

**参考资料：**

- [Xray-core issue 4797](https://github.com/XTLS/Xray-core/issues/4797)
- [官方示例配置](https://github.com/XTLS/Xray-examples/blob/main/VLESS-TCP-XTLS-Vision-REALITY/config_server.jsonc)
- [xray-core 镜像](https://github.com/xtls/Xray-core/pkgs/container/xray-core)
