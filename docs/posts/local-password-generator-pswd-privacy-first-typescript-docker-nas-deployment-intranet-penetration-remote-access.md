---
title: "本地密码生成器PSWD，隐私优先TypeScript构建，支持Docker和NAS快速部署，内网穿透远程访问"
date: 2025-10-16
description: "PSWD是TypeScript构建的在线密码生成器，提供安全隐私优先的密码生成方案。支持自定义密码长度和字符类型，所有生成过程在本地完成。支持Docker快速部署，适配NAS设备，无需复杂配置即可快速上手。"
canonical: https://www.passnat.com/posts/local-password-generator-pswd-privacy-first-typescript-docker-nas-deployment-intranet-penetration-remote-access
---

## 项目介绍

PSWD 是一款简洁高效的在线密码生成器。它采用 TypeScript 构建，提供了一个安全、隐私优先的密码生成方案。无论你是需要为各个账户创建强密码，还是想要一个可靠的密码管理辅助工具，PSWD 都能满足你的需求。

项目界面简洁易用，无需复杂配置即可快速上手。所有密码生成过程都在本地完成，充分保护你的隐私安全。支持自定义密码长度、字符类型等参数，让你灵活生成符合各种要求的密码。

项目地址： [点我跳转](https://github.com/remvze/pswd)

## 部署安装

如需在外网也能访问项目，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号备用，这样即使在公司或出差时也能随时使用你的密码生成器。具体操作方法可以 [点我查看](https://www.passnat.com/posts/p) 内网穿透全平台快速上手指南。

### 最快速部署方式

PSWD 提供了开箱即用的 Docker 部署方案，只需一条命令即可快速启动：

```bash
docker run -d --name pswd -p 8080:8080 --restart always ghcr.io/remvze/pswd
```

**部署完成！** 打开浏览器访问 http://你的IP:8080 即可开始使用密码生成器。

### NAS 设备部署教程

如果你在使用飞牛 NAS 或其他 NAS 设备，可以按照以下步骤进行部署：

**第一步：添加 Docker 镜像**

打开 NAS 管理后台，进入 Docker 管理界面，点击 **本地镜像** - **添加镜像** - **从 URL 添加**

镜像地址填写：ghcr.io/remvze/pswd

等待镜像下载完成后，点击启动按钮启动容器。

**第二步：配置容器参数**

勾选 **开机自动启动** 后进入下一步。

**端口映射设置：**

容器内端口：8080

宿主机端口：8080（如果该端口已被占用，可以修改为其他端口，例如 8081、8082 等，但容器内端口需保持为 8080）

**存储位置：**

该应用为无状态应用，无需特殊的存储映射，使用默认配置即可。

**环境变量：**

无需配置任何环境变量，保持默认即可。

**第三步：启动完成**

点击启动按钮，容器启动成功后，即可在浏览器中访问 http://NAS的IP地址:8080 来使用 PSWD 密码生成器。

## 总结

通过本教程，你已经成功部署了 PSWD 密码生成器。这是一个轻量级、隐私友好的工具，特别适合在个人 NAS 或家庭网络中部署使用。如需外网访问，记得配置内网穿透哦！

祝你使用愉快！

---

原文：<https://www.passnat.com/posts/local-password-generator-pswd-privacy-first-typescript-docker-nas-deployment-intranet-penetration-remote-access>
