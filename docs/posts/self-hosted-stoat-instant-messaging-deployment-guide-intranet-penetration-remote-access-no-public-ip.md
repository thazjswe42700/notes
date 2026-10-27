---
title: "自托管即时通讯平台Stoat完整部署指南，内网穿透远程访问无公网限制"
date: 2025-10-16
description: "Stoat 是功能完整的自托管即时通讯平台，采用现代技术栈构建，包含后端服务、Web前端、文件服务器等核心组件。所有数据掌握在自己手中，适用于企业内部沟通、朋友圈子交流和兴趣社群创建。本文详解部署安装步骤，包括环境准备、快速部署、配置说明和首次访问指南。"
canonical: https://www.passnat.com/posts/self-hosted-stoat-instant-messaging-deployment-guide-intranet-penetration-remote-access-no-public-ip
---

## 项目介绍

Stoat 是一个功能完整的自托管即时通讯平台，专为追求隐私和数据掌控的用户而设计。它采用现代化的技术栈构建，包含完整的后端服务、Web 前端、文件服务器、元数据代理等核心组件，让你可以搭建属于自己的专属通讯社区。

无论是用于企业内部沟通、朋友圈子交流，还是创建兴趣社群，Stoat 都能提供媲美主流通讯应用的体验。最重要的是，所有数据都掌握在你自己手中，完全不用担心隐私泄露问题。

项目地址：[点我跳转](https://github.com/revoltchat/self-hosted)

## 部署安装

若要在外网也能访问你的 Stoat 实例，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com) 来实现远程访问，先在官网注册账号备用。具体的使用步骤可以参考 [这份详细教程](https://www.passnat.com/posts/p)，涵盖了全平台的快速上手指南。

### 环境准备

首先，你需要一台服务器来部署 Stoat。建议配置至少 2 个 vCPU 和 2 GB 内存。推荐使用 Ubuntu Server 作为操作系统。

登录到你的服务器后，执行以下命令进行基础设置和依赖安装：

```bash
# 更新系统
apt-get update && apt-get upgrade -y

# 配置防火墙
ufw allow ssh
ufw allow http
ufw allow https
ufw default deny
ufw enable

# 安装必要工具
apt-get install ca-certificates curl git micro

# 安装 Docker
install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
chmod a+r /etc/apt/keyrings/docker.asc

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

apt-get update
apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### 快速部署

完成环境准备后，只需几个简单的命令就能启动 Stoat：

```bash
# 克隆配置仓库
git clone https://github.com/revoltchat/self-hosted stoat
cd stoat

# 生成配置文件（将 your.domain 替换为你的实际域名）
chmod +x ./generate_config.sh
./generate_config.sh your.domain
```

如果你想手动编辑配置文件，可以运行：

```
micro Revolt.toml
```

最后，启动 Stoat 服务：

```bash
# 前台运行（用于测试）
docker compose up

# 后台运行（生产环境推荐）
docker compose up -d
```

### 配置说明

启动后，Stoat 会自动创建以下核心服务：

MongoDB：数据库服务，存储所有用户和消息数据

Redis：缓存和事件消息代理

RabbitMQ：内部消息队列系统

MinIO：S3 兼容存储服务，用于文件上传

Caddy：Web 服务器和反向代理

API 服务：Stoat 后端 API

Web 前端：用户界面

文件服务：处理文件上传和下载

推送服务：消息推送通知

**端口映射：**

HTTP: 80

HTTPS: 443

如果这些端口已被占用，可以编辑 'compose.yml' 文件中的 Caddy 服务配置来修改端口。

**数据持久化：**

所有重要数据都会自动保存到以下目录：

'./data/db/' - 数据库数据

'./data/minio/' - 文件存储

'./data/rabbit/' - 消息队列数据

'./data/caddy-data/' 和 './data/caddy-config/' - Web 服务器配置

### 首次访问

部署完成后，打开浏览器访问你配置的域名即可开始使用 Stoat。系统会引导你创建账户并设置基本信息。

### 更新维护

当有新版本发布时，更新非常简单：

```bash
# 拉取最新代码
git pull

# 获取最新镜像
docker compose pull

# 重启服务
docker compose up -d
```

更新前建议查看项目的 README 文件，确认是否有重要的配置变更需要手动处理。

本篇教程结束

---

原文：<https://www.passnat.com/posts/self-hosted-stoat-instant-messaging-deployment-guide-intranet-penetration-remote-access-no-public-ip>
