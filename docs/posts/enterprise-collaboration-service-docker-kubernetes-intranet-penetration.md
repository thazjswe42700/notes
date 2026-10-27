---
title: "企业级协作服务开箱即用，私有部署支持Docker与Kubernetes，内网穿透远程访问"
date: 2025-10-16
description: "Univer是开箱即用的企业级协作服务解决方案，支持Kubernetes和Docker Compose部署。基于云原生架构，提供协作编辑、实时同步、数据存储等功能。个人开发者和企业用户可快速部署私有服务器，实现团队高效协作。系统需求低至1核2GB内存，支持Linux和Mac操作系统。"
canonical: https://www.passnat.com/posts/enterprise-collaboration-service-docker-kubernetes-intranet-penetration
---

## 项目介绍

Univer 是一个开箱即用的企业级协作服务解决方案。它基于现代化的云原生架构设计，支持 Kubernetes 和 Docker Compose 两种部署方式，为团队提供强大的协作编辑、实时同步、数据存储等功能。无论你是个人开发者还是企业用户，都可以快速部署自己的私有协作服务器，实现团队内部的高效协作。

项目地址： [点我跳转](https://github.com/dream-num/helm-charts)

## 部署安装

如果需要在外网也能访问你的协作服务器，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册好账号。具体的操作指南可以 [点击这里查看](https://www.passnat.com/posts/p)内网穿透全平台使用手册。

### 系统要求

CPU: 1 核心及以上

内存: 2GB 及以上

硬盘: 10GB 及以上

操作系统: Linux 或 Mac

### Docker Compose 快速部署

**第一步：安装 Docker**

确保你的系统已安装 Docker 23.0 或更高版本。如未安装，请访问 [Docker 官方文档](https://docs.docker.com/engine/install/) 进行安装。

**第二步：克隆项目仓库**

打开终端或命令行工具，执行以下命令：

```bash
git clone https://github.com/dream-num/helm-charts.git
cd helm-charts/docker-compose
```

**第三步：启动服务**

运行启动脚本即可一键启动所有服务：

```
bash run.sh
```

**第四步：验证部署**

等待所有容器启动完成后，你可以通过以下方式访问：

主服务: http://localhost:8000

对象存储控制台: http://localhost:19000

监控仪表板: http://localhost:13000

### 端口映射说明

服务名称 | 端口 | 用途说明
Universer | 8000 | API 服务器及主应用入口
MinIO | 19000 | 对象存储服务
Grafana | 13000 | 可观测性监控面板

如果上述端口与你系统中的其他服务产生冲突，可以通过编辑 '.env.custom' 文件来修改宿主机端口映射。例如：

```
HOST_NGINX_PORT=8080
HOST_MINIO_PORT=19001
HOST_GRAFANA_PORT=13001
```

修改后重新运行启动脚本即可生效。

### 可选配置

启用可观测性组件

如需启用完整的监控和日志功能，编辑 '.env.custom' 文件并添加：

```
ENABLE_UNIVER_OBSERVABILITY=true
```

演示 UI 启动

如果你想快速体验项目功能，可以运行：

```
bash run.sh start-demo-ui
```

这会为你启动一个预配置的演示界面，方便快速了解功能特性。

本篇教程结束

---

原文：<https://www.passnat.com/posts/enterprise-collaboration-service-docker-kubernetes-intranet-penetration>
