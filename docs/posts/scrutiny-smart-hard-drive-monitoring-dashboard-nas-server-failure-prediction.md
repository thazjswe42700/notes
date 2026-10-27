---
title: "硬盘智能监控仪表盘Scrutiny，S.M.A.R.T数据可视化预警，NAS服务器故障提前预测方案"
date: 2025-10-16
description: "Scrutiny是专业的硬盘健康监控仪表盘，结合S.M.A.R.T指标与真实故障率数据，为服务器提供智能预警。支持Web UI可视化界面、自动硬盘检测、温度监控、历史趋势追踪与Webhook告警通知。通过Docker快速部署，实时掌握所有硬盘关键指标，及时发现故障风险，确保数据安全。"
canonical: https://www.passnat.com/posts/scrutiny-smart-hard-drive-monitoring-dashboard-nas-server-failure-prediction
---

## 项目介绍

Scrutiny 是一个专业的硬盘健康监控仪表盘与管理方案。它将制造商提供的 S.M.A.R.T 指标与真实世界的硬盘故障率相结合，为你的服务器提供智能化的硬盘健康预警。

如果你运营一台拥有多块硬盘的服务器，Scrutiny 能够帮助你在硬盘故障前及时发现问题。通过 Web UI 仪表盘，你可以实时监控所有硬盘的关键指标，包括温度、S.M.A.R.T 属性历史趋势等信息。与传统的 smartd 命令行工具不同，Scrutiny 提供了友好的可视化界面，让复杂的硬盘数据一目了然。

核心特性包括：Web UI 仪表盘、smartd 集成、自动硬盘检测、S.M.A.R.T 指标历史追踪、基于真实故障率的自定义阈值、温度监控、Webhook 告警通知等功能。

项目地址：[点我跳转](https://github.com/AnalogJ/scrutiny)

## 部署安装

如果你需要在外网访问 Scrutiny 仪表盘，建议配置内网穿透工具。可以前往[帕斯内网穿透](https://passnat.com)注册账号，参考[全平台上手指南](https://www.passnat.com/posts/p)快速配置，这样就能在任何地方查看服务器硬盘状态了。

### 使用 Docker Compose 快速部署

复制下列的配置文件，新建一个 `docker-compose.yml` 文件：

```yaml
version: '3.5'

services:
  scrutiny:
    container_name: scrutiny
    image: ghcr.io/analogj/scrutiny:master-omnibus
    cap_add:
      - SYS_RAWIO
    ports:
      - "8080:8080" # webapp
      - "8086:8086" # influxDB admin
    volumes:
      - /run/udev:/run/udev:ro
      - ./config:/opt/scrutiny/config
      - ./influxdb:/opt/scrutiny/influxdb
    devices:
      - "/dev/sda"
      - "/dev/sdb"
```

打开你的 NAS 设备或服务器，进入 Docker 管理界面。

**关键配置说明：**

端口映射：`8080` 是 Scrutiny 的 Web 界面端口，`8086` 是 InfluxDB 数据库管理端口。如果这些端口在你的系统中已被占用，可以修改左侧的端口号（冒号前面的数字），右侧的容器内部端口保持不变。

设备挂载：`/dev/sda` 和 `/dev/sdb` 是你要监控的硬盘设备。请根据你的实际硬盘数量进行修改。如果你不确定设备名称，可以在主机上运行 `smartctl --scan` 命令查看。

存储卷：

./config - 存放 Scrutiny 的配置文件

./influxdb - 存放时间序列数据库的数据，用于记录硬盘 S.M.A.R.T 属性的历史趋势

请在运行前创建这两个文件夹，确保容器有权限进行读写操作。

**环境变量设置：**

此项目的 Docker 镜像已包含所有必要配置，通常无需额外设置环境变量。如有特殊需求可参考官方文档进行自定义。

**权限说明：**

SYS\_RAWIO - 允许容器访问硬盘的 S.M.A.R.T 数据

如果你使用 NVMe 硬盘，还需添加 `--cap-add SYS\_ADMIN` 权限

配置完成后启动容器。

### 部署完成

创建成功后，在浏览器中访问 `http://你的IP:8080` 即可打开 Scrutiny 仪表盘。

你将看到一个清晰的硬盘健康监控界面，所有连接的硬盘都会被自动检测并显示其 S.M.A.R.T 状态、温度、故障预警等关键信息。仪表盘会持续追踪硬盘属性的变化趋势，帮助你提前发现潜在的硬盘故障风险。

本篇教程结束

---

原文：<https://www.passnat.com/posts/scrutiny-smart-hard-drive-monitoring-dashboard-nas-server-failure-prediction>


## 相关文章

- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
- [DPanel Docker可视化管理面板一键部署，内网穿透远程访问容器，极简操作无需命令行](dpanel-docker-visual-management-panel-one-click-deployment-intranet-penetration-remote-access-containers-simple-operation-no-command-line.md)
- [免费在线虚拟钢琴演奏平台 AutoPiano NAS 极简部署 内网穿透远程弹奏](free-online-virtual-piano-autopiano-nas-simple-deployment-intranet-penetration-remote-playing.md)
- [自托管即时通讯平台Stoat完整部署指南，内网穿透远程访问无公网限制](self-hosted-stoat-instant-messaging-deployment-guide-intranet-penetration-remote-access-no-public-ip.md)
- [家庭私人照片管理系统PhotoStructure，NAS一键部署，内网穿透远程访问你的回忆](photostructure-nas-private-photo-management-remote-access.md)
