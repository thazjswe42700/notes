---
title: "自托管服务门户Easy Gate，NAS一键部署，内网穿透远程访问自建应用聚合平台"
date: 2025-10-16
description: "Easy Gate 是轻量级自托管基础设施管理门户，为家庭实验室和企业服务提供统一访问入口。支持 JSON/YAML 配置文件动态解析，无需重启即可更新服务列表。通过 IP 地址灵活管理用户组，实现内网和 VPN 用户差异化访问控制。聚合 NAS、Docker、Git 等自建应用于一个美观仪表板中。"
canonical: https://www.passnat.com/posts/easy-gate-nas-deployment-intranet-penetration-remote-access
---

## 项目介绍

Easy Gate 是一个轻量级的自托管基础设施管理门户，为你的家庭实验室或企业服务提供统一的访问入口。它采用简洁高效的设计理念，通过实时解析配置文件（支持 JSON/YAML 格式），无需重启应用即可动态更新服务列表和公告信息。

Easy Gate 的核心优势在于其灵活的用户组管理功能——你可以根据用户的 IP 地址将服务分配给不同的用户组，实现内网用户和 VPN 用户的差异化访问控制。无论是个人的 NAS 服务、Docker 容器管理、Git 仓库还是各类自建应用，Easy Gate 都能将它们优雅地聚合在一个美观的仪表板中。

项目地址： [点我跳转](https://github.com/r7wx/easy-gate)

## 部署安装

如果你需要在外网也能访问家中的 Easy Gate 门户，推荐使用内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，具体的配置教程请 [点我查看](https://www.passnat.com/posts/p)，帮助你快速上手全平台内网穿透方案。

### 最快速上手方案 - Docker 单命令部署

打开你的 NAS 设备或服务器的 Docker 管理界面。

点击'本地镜像'→'添加镜像'→'从 URL 添加'，在镜像地址栏填入：

```
wiredlush/easy-gate:latest
```

等待镜像下载完成后，点击启动按钮。

### 容器配置步骤

**1. 基础设置**

勾选'开机自动启动'确保服务不中断。

**2. 端口映射设置**

将容器端口 8080 映射到主机端口。如果你的 8080 端口已被占用，可以修改为其他端口（如 8081、8082 等），映射关系为 主机端口:8080。记住你设置的主机端口，后续访问时需要用到。

**3. 存储位置配置**

创建一个文件夹用于存放 Easy Gate 的配置文件，例如在 NAS 上创建 /mnt/easy-gate 目录。

将此目录映射到容器内的 /etc/easy-gate 路径，这样配置文件会被持久化保存。

**4. 环境变量设置（可选）**

以下是 Easy Gate 支持的环境变量：

**必须设置：**

暂无必须设置的环境变量

**可选设置：**

EASY\_GATE\_CONFIG\_PATH：自定义配置文件路径（默认：/etc/easy-gate/easy-gate.json）

EASY\_GATE\_CONFIG：直接以环境变量形式提供配置内容（JSON 或 YAML 字符串）

EASY\_GATE\_ROOT\_PATH：自定义应用根目录路径

如无特殊需求，这些环境变量可以不填，系统会使用默认配置。

**5. 启动容器**

完成上述配置后，点击'下一步'并确认启动。

### 访问应用

容器启动成功后，在浏览器中访问：

```
http://你的NAS_IP:8080
```

（如果你修改了端口映射，请将 8080 替换为你设置的端口号）

此时你应该能看到 Easy Gate 的欢迎页面。

### 配置你的服务

在 NAS 上编辑 /mnt/easy-gate/easy-gate.json 配置文件，添加你的自建服务。配置示例：

```
{
  'title': '我的家庭服务',
  'addr': '0.0.0.0:8080',
  'theme': {
    'background': '#1d1d1d',
    'foreground': '#ffffff'
  },
  'services': [
    {
      'name': 'Portainer',
      'url': 'http://192.168.1.100:9000',
      'category': '容器管理'
    },
    {
      'name': 'Home Assistant',
      'url': 'http://192.168.1.100:8123',
      'category': '智能家居'
    }
  ],
  'notes': [
    {
      'name': '公告',
      'text': '欢迎使用 Easy Gate！'
    }
  ]
}
```

配置文件修改后会实时生效，无需重启应用。

本篇教程结束。通过 Easy Gate，你现在拥有了一个优雅的自建服务聚合平台，让你可以轻松管理和访问家中的所有服务。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/easy-gate-nas-deployment-intranet-penetration-remote-access>
