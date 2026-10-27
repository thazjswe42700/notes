---
title: "Standard Notes 私密笔记自建 - 端到端加密开源方案，NAS 极简部署指南"
date: 2025-10-16
description: "发现一款真正属于你的端到端加密笔记应用。Standard Notes 采用开源设计，支持自托管部署，所有笔记内容完全加密，仅你可读。跨Mac、Windows、Linux、iOS、Android平台同步，提供完整源代码和Docker一键部署方案，让你完全掌控个人数据。"
canonical: https://www.passnat.com/posts/standard-notes-private-notes-self-hosted-end-to-end-encryption-open-source-nas-simple-deployment-guide
---

## 📝 Standard Notes - 端到端加密的私密笔记本

### 项目介绍

Standard Notes 是一款功能强大的端到端加密笔记应用，为数字工作者和专业人士设计。它采用 TypeScript 构建，提供完全开源的解决方案，支持跨平台部署和自托管。

这是一个真正属于你的笔记应用——所有内容都经过端到端加密，只有你能读取你的笔记。无论是在 Mac、Windows、Linux、iOS 还是 Android 上，你都能获得快速、免费且安全的跨设备同步体验。最重要的是，Standard Notes 提供了完整的源代码，你可以在自己的服务器上部署，完全掌控你的数据。

项目地址：[点我跳转](https://github.com/standardnotes)

### 部署安装

如果你想在外网也能访问自建的笔记服务，建议配置内网穿透工具。可以考虑使用 [帕斯内网穿透](https://passnat.com)，先注册账号以备不时之需。具体的配置步骤可以参考 [这篇完整教程](https://www.passnat.com/posts/p)，内容涵盖了全平台的上手方法。

#### 快速部署指南

Standard Notes 提供了开箱即用的 Docker 部署方案，只需几个简单步骤即可启动你的私密笔记服务器。

**第一步：准备工作目录**

打开你的 NAS 或其他支持 Docker 的设备，创建一个工作文件夹用于存放配置文件。

**第二步：下载配置文件**

执行以下命令获取必要的配置文件：

```bash
mkdir standardnotes && cd standardnotes
curl https://raw.githubusercontent.com/standardnotes/server/main/.env.sample > .env
curl https://raw.githubusercontent.com/standardnotes/server/main/docker/localstack_bootstrap.sh > localstack_bootstrap.sh
chmod +x localstack_bootstrap.sh
curl https://raw.githubusercontent.com/standardnotes/server/main/docker-compose.example.yml > docker-compose.yml
```

**第三步：生成安全密钥**

编辑 `.env` 文件，使用以下命令生成安全的随机密钥值：

```
openssl rand -hex 32
```

将生成的值填入 `.env` 文件中 KEYS 部分的相应环境变量。

同时生成数据库密码：

```
openssl rand -hex 12
```

将这个密码值更新到 `.env` 文件中的 `DB\_PASSWORD`，以及 `docker-compose.yml` 中的 `MYSQL\_ROOT\_PASSWORD` 和 `MYSQL\_PASSWORD`。

**第四步：启动服务**

完成配置后，执行以下命令启动 Standard Notes 服务：

```bash
docker compose pull && docker compose up -d
```

等待容器启动完成（通常需要几分钟）。你可以通过以下命令查看日志：

```
tail -f logs/*.log
```

如果遇到问题，可以检查错误日志：

```
tail -f logs/*.err
```

#### 环境变量说明

**必须设置的环境变量：**

DB\_PASSWORD - 数据库密码（使用 `openssl rand -hex 12` 生成）
JWT\_SECRET - JWT 认证密钥（使用 `openssl rand -hex 32` 生成）
ENCRYPTION\_SERVER\_KEY - 加密服务器密钥（使用 `openssl rand -hex 32` 生成）

**可选设置：**

PUBLIC\_FILES\_SERVER\_URL - 文件服务器的公网 URL（例如：`http://10.0.1.2:3125`）
其他高级配置项可在 `.env.sample` 中查看

#### 端口配置

Standard Notes 默认使用以下端口：

3000 - 主服务端口（同步服务）
3125 - 文件服务端口

如果这些端口已被占用，你可以在 `docker-compose.yml` 文件中修改 `ports` 字段来自定义端口映射。只需修改冒号前的端口号即可，容器内的端口保持不变。

#### 存储配置

Standard Notes 使用 MySQL 数据库存储数据。建议在 `docker-compose.yml` 中配置数据卷映射，确保数据持久化。默认配置已包含数据库卷的设置。

#### 访问你的笔记服务

启动完成后，在浏览器中访问 `http://localhost:3000`（或你配置的对应 IP 和端口），即可看到 Standard Notes 的登录界面。

**首次使用：**

1. 点击注册创建新账户
2. 设置安全的密码
3. 开始记录你的笔记

**连接客户端应用：**

在 Standard Notes 的桌面或移动应用中，进入高级选项，选择'自定义'同步服务器，输入你的服务器地址即可同步所有笔记。

#### 更新维护

在更新服务器前，请务必备份数据。更新步骤如下：

```bash
docker compose down
# 更新配置文件
curl https://raw.githubusercontent.com/standardnotes/server/main/.env.sample > .env
curl https://raw.githubusercontent.com/standardnotes/server/main/docker-compose.example.yml > docker-compose.yml
# 重新启动
docker compose pull && docker compose up -d
```

### 总结

Standard Notes 为你提供了一个完全私密、安全可靠的笔记解决方案。通过 Docker 部署，即使是技术小白也能轻松搭建属于自己的笔记服务。所有数据都加密存储在你的服务器上，真正实现数据所有权。无论是个人使用还是团队协作，Standard Notes 都是理想的选择。

现在就开始部署，享受真正的隐私笔记体验吧！

---

原文：<https://www.passnat.com/posts/standard-notes-private-notes-self-hosted-end-to-end-encryption-open-source-nas-simple-deployment-guide>
