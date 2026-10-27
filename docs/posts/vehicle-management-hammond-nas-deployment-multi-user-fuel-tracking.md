---
title: "自建车辆管理系统Hammond NAS极简部署，多用户多车追踪燃油费用记录平台"
date: 2025-10-16
description: "Hammond 是自托管车辆管理系统，采用 Go 和 Vue.js 开发，支持多用户多车辆管理。功能包括燃油追踪、智能报表、数据导入和权限管理。支持 Docker 快速部署，提供数据隐私保护。是 Clarkson 项目的完美继任者，适合个人和车队使用。"
canonical: https://www.passnat.com/posts/vehicle-management-hammond-nas-deployment-multi-user-fuel-tracking
---

## 项目介绍

Hammond 是一款功能强大的自托管车辆管理系统，专为追踪车辆燃油消耗和其他相关费用而设计。这是已停止维护的 Clarkson 项目的完美继任者。

该系统采用 Go 语言编写后端和 Vue.js 前端，支持多用户共享多辆车的完整管理流程。无论你是车队管理员还是家庭用户，Hammond 都能帮助你详细记录每一笔车辆开支，生成专业的费用报表，让你对车辆成本了如指掌。

项目地址：[点我跳转](https://github.com/akhilrex/hammond)

## 主要功能

🚗 多车辆管理 - 轻松添加和管理多辆车

👥 多用户支持 - 支持多个用户共享同一辆车

⛽ 燃油追踪 - 详细记录每次加油和消费

📊 智能报表 - 生成车辆级别和整体费用报告

📸 快速记录 - 拍照保存收据或加油单据，稍后补充详情

📁 附件管理 - 为车辆保存相关文档和照片

📥 数据导入 - 支持从 Clarkson 和 Fuelly 导入历史数据

🔐 权限管理 - 完善的用户权限和数据隐私保护

## 部署安装

如果你需要在外网也能访问这个系统，建议配置内网穿透。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，详细的配置步骤可以参考 [这份全平台上手指南](https://www.passnat.com/posts/p)，让你轻松实现远程访问。

### 最快速部署方案（Docker 单命令）

打开你的 NAS 设备或 Linux 服务器，执行以下命令即可快速启动 Hammond：

```bash
docker run -d -p 3000:3000 --name=hammond -v '/host/path/to/config:/config' -v '/host/path/to/data:/assets' akhilrex/hammond
```

**参数说明：**

-p 3000:3000 - 端口映射，如果 3000 端口被占用，可改为其他端口（如 -p 8080:3000）

-v '/host/path/to/config:/config' - 配置文件存储路径，请将 /host/path/to/config 替换为你本地的实际路径

-v '/host/path/to/data:/assets' - 数据和附件存储路径，请将 /host/path/to/data 替换为你本地的实际路径

### 使用 Docker-Compose 部署

如果你更习惯使用 Docker-Compose，可以复制以下配置文件：

```yaml
version: '2.1'
services:
  hammond:
    image: akhilrex/hammond
    container_name: hammond
    volumes:
      - /path/to/config:/config
      - /path/to/data:/assets
    ports:
      - 3000:3000
    restart: unless-stopped
```

将上述内容保存为 docker-compose.yml 文件，然后在该文件所在目录执行：

```
docker-compose up -d
```

**配置说明：**

请根据你的实际情况修改 /path/to/config 和 /path/to/data 为本地真实路径

如果 3000 端口冲突，修改 ports 中的第一个数字即可（容器内部端口保持 3000 不变）

restart: unless-stopped 表示容器异常退出时会自动重启

### 环境变量配置

Hammond 支持以下环境变量配置：

**必须设置：**

JWT\_SECRET - JWT 令牌签名密钥（强烈建议修改默认值，以保证安全性）

**可选设置：**

PORT - 修改应用内部运行端口（默认为 3000，通常无需修改）

如无特殊需求，保持默认配置即可。

## 初次使用

部署完成后，在浏览器中访问 http://你的IP:3000（如果修改过端口，请相应调整），首次打开会看到设置向导。

你可以选择：

从 Clarkson 迁移 - 如果你之前使用过 Clarkson，可以导入历史数据

全新安装 - 创建管理员账户，开始使用

建议先进入设置页面调整相关参数，然后开始添加车辆和记录费用。

## 总结

Hammond 是一个开箱即用、功能完整的车辆管理解决方案。通过简单的 Docker 部署，你就能拥有一个属于自己的私密车辆管理系统，再也不用担心数据隐私问题。无论是个人用户还是车队管理者，都能从中受益。

本篇教程到此结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/vehicle-management-hammond-nas-deployment-multi-user-fuel-tracking>
