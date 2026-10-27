---
title: "开源密码管理器Passbolt部署指南，NAS极简安装内网穿透远程访问"
date: 2025-10-16
description: "Passbolt是开源团队密码管理器，采用端到端加密和用户自持密钥，为组织提供安全的凭证共享与管理。支持Docker快速部署，提供权限管理、审计日志和隐私保护，适合各规模企业使用。"
canonical: https://www.passnat.com/posts/passbolt-nas-deployment-guide-intranet-penetration-remote-access
---

## 项目介绍

Passbolt 是一款安全至上的开源团队密码管理器，专为组织设计。它帮助企业和团队集中管理、组织和安全地共享密码及机密信息。

**Passbolt 的核心优势：**

安全第一：采用用户自持密钥和端到端加密的安全模型，每年接受多次安全审计，所有审计报告公开透明

团队协作：支持安全地共享和审计凭证，为高级用户提供强大且可靠的权限管理策略

隐私保护：总部位于欧盟卢森堡，不收集个人数据或遥测信息，支持在隔离网络环境中部署

无论是小型团队还是大型企业，Passbolt 都能为你提供一个可靠、透明且易于审计的密码管理解决方案。

项目地址：[点我跳转](https://github.com/passbolt/passbolt_api)

## 部署安装

如果需要在外网访问你的 Passbolt 实例，建议配置内网穿透服务。推荐使用[帕斯内网穿透](https://passnat.com)，先注册账号备用。详细的操作步骤可以参考[这份完整上手指南](https://www.passnat.com/posts/p)。

### 最快速部署方案（Docker Compose）

Passbolt 官方提供了开箱即用的 Docker Compose 配置，只需三条命令即可快速启动：

```bash
curl -LO "https://download.passbolt.com/ce/docker/docker-compose-ce.yaml"
curl -LO "https://github.com/passbolt/passbolt_docker/releases/latest/download/docker-compose-ce-SHA512SUM.txt"
sha512sum -c docker-compose-ce-SHA512SUM.txt && echo "Checksum OK" || (echo "Bad checksum. Aborting" && rm -f docker-compose-ce.yaml)
docker-compose -f docker-compose-ce.yaml up -d
```

这个方案会自动下载官方验证的配置文件，并验证文件完整性后启动 Passbolt 服务。

### 通过 NAS 设备部署

打开你的飞牛 NAS 或其他 NAS 设备的管理界面。

进入容器管理模块，点击「本地镜像」-「添加镜像」-「从 URL 添加」。

在镜像地址栏填入：passbolt/passbolt:latest

等待镜像下载完成后，点击启动按钮启动容器。

**容器启动配置：**

勾选「开机自动启动」确保服务随系统启动。

**端口映射设置：**

Passbolt 默认使用**80 端口**（HTTP）和**443 端口**（HTTPS）进行通信。如果你的 NAS 上这些端口已被占用，可以修改主机端口映射。例如可以将容器的 80 端口映射到主机的 8080 端口，将 443 端口映射到 8443 端口。记住：容器内部端口保持不变，只需修改前面的主机端口号即可。

**存储卷配置：**

Passbolt 需要持久化存储数据库和密钥信息。建议在 NAS 上创建以下目录用于数据持久化：

/var/lib/passbolt/ - 存储应用数据和密钥

将这些目录挂载到容器中，确保数据不会因容器重启而丢失。

**环境变量设置：**

Passbolt 支持多项环境变量配置，以下为常用选项：

**必须设置：**

PASSBOLT\_SALT - 加密盐值（建议使用强随机字符串）

PASSBOLT\_SECURITY\_SMTP\_HOST - SMTP 邮件服务器地址（用于发送邮件通知）

PASSBOLT\_SECURITY\_SMTP\_PORT - SMTP 端口（通常为 587 或 25）

**可选设置：**

PASSBOLT\_SECURITY\_SMTP\_USERNAME - SMTP 用户名

PASSBOLT\_SECURITY\_SMTP\_PASSWORD - SMTP 密码

PASSBOLT\_SECURITY\_SMTP\_TLS - 是否启用 TLS（true/false）

其他参数保持默认即可，点击下一步启动容器。

**访问应用：**

容器启动成功后，在浏览器中访问http://你的NAS地址:80（如果修改了端口则使用对应端口）即可进入 Passbolt 登录界面。

首次访问时会进行初始化设置，按照向导完成管理员账户创建即可开始使用。

## 总结

通过上述步骤，你已经成功部署了 Passbolt 密码管理器。这个开源解决方案为你的团队提供了企业级的密码管理能力，同时保持了完全的隐私控制和数据所有权。现在你可以安全地与团队成员共享凭证，并通过完整的审计日志追踪所有操作。

本篇教程结束。

---

原文：<https://www.passnat.com/posts/passbolt-nas-deployment-guide-intranet-penetration-remote-access>
