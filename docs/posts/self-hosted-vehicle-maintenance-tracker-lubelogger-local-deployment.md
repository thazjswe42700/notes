---
title: "自托管车辆保养追踪系统，本地部署LubeLogger，维护记录油耗管理随时查看"
date: 2025-10-16
description: "LubeLogger是一款自托管开源车辆维护管理系统，提供详细的保养记录、油耗追踪、费用统计等功能。界面简洁易用，所有数据存储在自己的服务器上，隐私安全有保障。支持Docker快速部署，适合车主和车队管理者系统地管理车辆生命周期。"
canonical: https://www.passnat.com/posts/self-hosted-vehicle-maintenance-tracker-lubelogger-local-deployment
---

## 项目介绍

LubeLogger 是一个自托管的、开源的、基于网页的车辆维护和油耗里程追踪系统。它彻底改变了你管理车辆维护记录的方式——再也不用为杂乱的电子表格或满地的收据而烦恼。

这是一款功能完整的车辆保养管理工具，支持详细的维护记录、油耗追踪、费用统计、数据导出等功能。界面简洁直观，即使是技术小白也能轻松上手。最重要的是，所有数据都存储在你自己的服务器上，完全掌握在自己手中，隐私和安全有保障。

无论你是车主还是车队管理者，LubeLogger 都能帮助你系统地管理车辆的整个生命周期，让每一次保养都有迹可循。

项目地址：[点我跳转](https://github.com/hargata/lubelog)

## unknown node

unknown node[unknown node](https://passnat.com)unknown node

### unknown node

unknown nodeunknown nodeunknown node

unknown node

```yaml
services:
  lubelogger:
    image: ghcr.io/hargata/lubelogger:latest
    container_name: lubelogger
    restart: unless-stopped
    network_mode: bridge
    ports:
      - "3492:8080"
    volumes:
      - ./data:/App/data
      - ./keys:/root/.aspnet/DataProtection-Keys
```

unknown nodeunknown nodeunknown nodeunknown nodeunknown nodeunknown nodeunknown nodeunknown nodeunknown node

unknown node

## unknown node

unknown nodeunknown nodeunknown nodeunknown nodeunknown node

unknown nodeunknown nodeunknown node

unknown node

## unknown node

unknown nodeunknown nodeunknown node

unknown nodeunknown nodeunknown nodeunknown nodeunknown node

unknown node

unknown node

unknown node

---

原文：<https://www.passnat.com/posts/self-hosted-vehicle-maintenance-tracker-lubelogger-local-deployment>


## 相关文章

- [LubeLogger 保养记录：NAS Docker 部署 + 内网穿透](lubelogger-nas-docker-car-maintenance-fuel-records-intranet-penetration.md)
- [自建车辆管理系统Hammond NAS极简部署，多用户多车追踪燃油费用记录平台](vehicle-management-hammond-nas-deployment-multi-user-fuel-tracking.md)
- [自建健身管理系统wger完整部署指南，Docker Compose一键启动训练营养追踪平台](fitness-management-system-wger-complete-deployment-guide-docker-compose-one-click-launch-training-nutrition-tracking-platform.md)
- [自托管私人图书馆管理系统BookLogr，NAS极简部署，内网穿透随处访问阅读数据](self-hosted-private-library-management-booklogr-nas-deployment-intranet-access.md)
- [个人财务管理系统BudgetBee，Docker一键部署，NAS极简配置，多账户记账工具](budget-bee-docker-deployment-nas-multi-account-accounting.md)
