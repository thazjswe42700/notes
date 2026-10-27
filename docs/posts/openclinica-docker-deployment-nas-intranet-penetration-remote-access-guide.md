---
title: "OpenClinica开源临床数据管理系统Docker部署指南，NAS内网穿透远程访问完整教程"
date: 2025-10-16
description: "OpenClinica是开源临床数据管理系统，提供电子数据采集、临床试验数据管理、eCRF表单设计、数据验证、患者访问安排、数据监测、审计追踪和电子签名等功能。支持角色访问控制、数据导入导出和分析，是医疗机构规范化临床试验的理想选择。"
canonical: https://www.passnat.com/posts/openclinica-docker-deployment-nas-intranet-penetration-remote-access-guide
---

## 项目介绍

OpenClinica 是一款开源的临床数据管理系统，专为电子数据采集（EDC）和临床试验数据管理（CDM）而设计。这是一个功能完整、安全可靠的企业级解决方案，能够帮助医疗研究机构高效地管理临床试验工作流程。

通过 OpenClinica，你可以轻松构建临床研究项目、创建电子病历表单（eCRF）、设计数据验证规则、安排患者访问计划、从各研究中心通过网络采集数据、进行数据监测与管理，同时具备完整的审计追踪和电子签名功能。系统提供了基于角色的访问控制、数据导入导出、以及数据提取分析等强大功能，是医疗机构进行规范化临床试验的理想选择。

项目地址：[点我跳转](https://github.com/OpenClinica/OpenClinica)

## 部署安装

如果你需要在外部网络也能访问 OpenClinica，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样就能随时随地访问你的系统。具体的操作步骤可以参考 [帕斯内网穿透全平台上手攻略](https://www.passnat.com/posts/p)，让你快速掌握内网穿透的使用方法。

### Docker 快速部署

打开你的 NAS 设备或服务器的管理界面（如飞牛 NAS 等）

点击本地镜像 - 添加镜像 - 从 URL 添加，在镜像地址栏填入：

```
openclinica/openclinica:latest
```

等待镜像下载完成后，点击启动按钮启动容器。

**容器配置步骤：**

1. 开机自启设置：勾选'开机自动启动'，确保系统重启后容器能自动运行

2. 端口映射：

容器内部端口：8080（OpenClinica 应用服务）

容器内部端口：5432（PostgreSQL 数据库）

你可以根据需要修改宿主机的映射端口。如果提示端口被占用，只需修改宿主机端口号即可，容器内的端口保持不变。例如，如果 8080 被占用，可改为 8081、8082 等。

3. 存储挂载（如需持久化数据）：

建议在 NAS 上创建一个文件夹用于存储 OpenClinica 的数据库文件和应用数据

将容器内的 /var/lib/postgresql 目录挂载到 NAS 的存储位置，确保数据不会因容器重启而丢失

4. 环境变量设置：

**必须设置：**

POSTGRES\_PASSWORD：PostgreSQL 数据库密码（请设置一个安全的密码）

POSTGRES\_USER：数据库用户名（默认值：postgres）

**可选设置：**

POSTGRES\_DB：数据库名称（默认值：openclinica）

OC\_INSTANCE\_NAME：系统实例名称

OC\_ADMIN\_PASSWORD：管理员初始密码

其他参数保持默认即可，无需额外配置。

5. 完成部署：点击下一步启动容器

部署完成后，在浏览器中访问 http://你的NAS地址:8080 即可进入 OpenClinica 系统。首次登录时使用默认的管理员账号，然后根据提示进行初始化配置。

## 总结

通过上述步骤，你已经成功部署了 OpenClinica 临床数据管理系统。这个系统为医疗研究机构提供了一个完整、安全、易于使用的临床试验数据管理平台。如有任何问题，可以参考官方文档或在社区论坛寻求帮助。

祝你使用愉快！

---

原文：<https://www.passnat.com/posts/openclinica-docker-deployment-nas-intranet-penetration-remote-access-guide>
