---
title: "开源物联网平台ThingsBoard Docker部署指南，NAS极简搭建企业级IoT解决方案"
date: 2025-10-16
description: "ThingsBoard是开源物联网平台，支持MQTT、HTTP、CoAP等多协议接入。提供规则引擎、实时仪表板、SCADA监控等功能，适用于智能能源、工业控制、车队追踪等场景。本文详解Docker Compose快速部署方法、端口配置、数据持久化和环境变量设置，帮助你在NAS或服务器上轻松搭建企业级IoT解决方案。"
canonical: https://www.passnat.com/posts/thingsboard-docker-deployment-guide-nas-enterprise-iot
---

## 项目介绍

ThingsBoard 是一个开源的物联网平台，专为数据收集、处理、可视化和设备管理而设计。这是一个功能完整的企业级 IoT 解决方案，支持多种设备连接协议（MQTT、HTTP、CoAP、LwM2M、SNMP 等），让你能够轻松构建从设备接入到数据分析的完整物联网生态。

平台提供了强大的规则引擎、实时仪表板、SCADA 监控等功能，无论是智能能源、工业控制、车队追踪、智能农业还是智能计量，ThingsBoard 都能胜任。它具有高度的可扩展性和容错能力，支持微服务架构部署，是构建物联网应用的理想选择。

项目地址：[点我跳转](https://github.com/thingsboard/thingsboard)

## 部署安装

若要在外网访问你的 ThingsBoard 平台，建议配置内网穿透服务。可以使用 [帕斯内网穿透](https://passnat.com) 来实现，先注册账号备用。具体的操作流程可以参考 [这份详细教程](https://www.passnat.com/posts/p)，轻松掌握全平台内网穿透的使用方法。

ThingsBoard 提供了完整的 Docker Compose 配置，包含了多个微服务组件（核心服务、规则引擎、各种传输协议支持、Web UI 等）。以下是最快速的部署方式：

### 使用 Docker Compose 部署

复制下列的配置文件到你的 NAS 或服务器上，命名为 `docker-compose.yml`：

```yaml
services:
  zookeeper:
    restart: always
    image: "zookeeper:3.8.1"
    ports:
      - "2181"
    environment:
      ZOO_MY_ID: 1
      ZOO_SERVERS: server.1=zookeeper:2888:3888;zookeeper:2181
      ZOO_ADMINSERVER_ENABLED: "false"
      
  tb-core1:
    restart: always
    image: "thingsboard/tb-node:latest"
    ports:
      - "8080:8080"
      - "7070:7070"
    environment:
      TB_SERVICE_ID: tb-core1
      TB_SERVICE_TYPE: tb-core
      EDGES_ENABLED: "true"
    env_file:
      - tb-node.env
    volumes:
      - ./tb-node/conf:/config
      - ./tb-node/log:/var/log/thingsboard
    depends_on:
      - zookeeper
      
  tb-web-ui:
    restart: always
    image: "thingsboard/tb-web-ui:latest"
    ports:
      - "8080:8080"
    env_file:
      - tb-web-ui.env
      
  tb-mqtt-transport:
    restart: always
    image: "thingsboard/tb-mqtt-transport:latest"
    ports:
      - "1883:1883"
    environment:
      TB_SERVICE_ID: tb-mqtt-transport
    env_file:
      - tb-mqtt-transport.env
    depends_on:
      - zookeeper
      - tb-core1
      
  tb-http-transport:
    restart: always
    image: "thingsboard/tb-http-transport:latest"
    ports:
      - "8081:8081"
    environment:
      TB_SERVICE_ID: tb-http-transport
    env_file:
      - tb-http-transport.env
    depends_on:
      - zookeeper
      - tb-core1
```

打开你的飞牛 NAS 或其他 NAS 设备，进入 Docker 管理界面。

点击'本地镜像'菜单，选择'添加镜像'，然后选择'从 URL 添加'。这一步用来预先下载所需的 Docker 镜像。

在镜像地址栏中填入 `thingsboard/tb-node:latest`、`thingsboard/tb-web-ui:latest`、`thingsboard/tb-mqtt-transport:latest` 等镜像地址，耐心等待下载完成。

完成镜像下载后，返回 Docker 管理界面，点击'从模板创建'或'自定义'选项，将上面的 docker-compose 配置内容粘贴进去。

### 端口配置说明

ThingsBoard 需要映射以下主要端口：

8080：Web UI 和 HTTP API 接口（容器内端口 8080）

7070：设备边缘 RPC 通信端口（容器内端口 7070）

1883：MQTT 协议接入端口（容器内端口 1883）

8081：HTTP 传输协议端口（容器内端口 8081）

2181：Zookeeper 服务端口（容器内端口 2181）

如果你的 NAS 或服务器上这些端口已被占用，可以修改左侧的端口号（冒号前面的数字），但容器内的端口（冒号后面的数字）需要保持不变。例如，如果 8080 被占用，可以改为 `8888:8080`。

### 存储和数据持久化

ThingsBoard 需要持久化存储配置文件和日志。在创建容器时，你需要创建以下目录映射：

/config：存储 ThingsBoard 核心配置文件

/var/log/thingsboard：存储应用日志文件

在 NAS 上创建对应的文件夹（如 `/mnt/data/tb-node/conf` 和 `/mnt/data/tb-node/log`），然后在容器配置中将这些路径映射进去。这样即使容器重启，你的配置和数据也不会丢失。

### 环境变量配置

必须设置的环境变量：

TB\_SERVICE\_ID：服务实例的唯一标识符，用于集群部署时区分不同节点

TB\_SERVICE\_TYPE：服务类型，可选值为 `tb-core`（核心服务）、`tb-rule-engine`（规则引擎）等

EDGES\_ENABLED：是否启用 ThingsBoard Edge 功能，设置为 `true` 或 `false`

可选配置的环境变量：

JAVA\_OPTS：Java 虚拟机启动参数，用于调整内存和性能

ZOO\_MY\_ID：Zookeeper 集群中该节点的 ID

ZOO\_SERVERS：Zookeeper 集群配置字符串

其他环境变量通常在对应的 `.env` 文件中配置，包括数据库连接、缓存设置、消息队列配置等。如果你是第一次部署，可以使用官方的默认配置，无需手动修改这些高级参数。

### 启动和访问

完成上述配置后，点击'启动'或'部署'按钮启动容器组。等待所有服务完全启动（通常需要 30-60 秒），然后在浏览器中访问：

```
http://你的NAS_IP:8080
```

首次访问时，系统会引导你创建管理员账户。默认的登录凭证为：

用户名：`tenant@thingsboard.org`

密码：`tenant`

成功登录后，你就可以开始创建设备、配置规则引擎、构建实时仪表板了。

本篇教程到此结束。ThingsBoard 是一个功能强大的物联网平台，通过 Docker 部署可以快速搭建起企业级的 IoT 解决方案。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/thingsboard-docker-deployment-guide-nas-enterprise-iot>
