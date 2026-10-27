---
title: "开源无代码数据库Baserow自托管部署，Docker一键安装NAS内网穿透远程访问完全指南"
date: 2025-10-16
description: "Baserow是开源无代码数据库工具，Airtable完美替代品。无需编程经验即可创建在线数据库，采用电子表格与数据库混合设计。基于Django、Vue.js和PostgreSQL构建，支持完全自托管部署，无存储限制。MIT许可证开源，适合个人和商业应用。Docker单命令部署，支持NAS设备安装。"
canonical: https://www.passnat.com/posts/open-source-no-code-database-baserow-self-hosted-deployment-docker-one-click-installation-nas-intranet-penetration-remote-access-complete-guide
---

## 项目介绍

Baserow 是一个开源的无代码数据库工具，也是 Airtable 的绝佳替代品。它让你无需任何技术经验就能创建自己的在线数据库。这款用户友好的无代码工具赋予你开发者级别的能力，而你只需要在浏览器中操作。

Baserow 采用电子表格与数据库的混合设计，既保留了电子表格的易用性，又具备强大的数据组织能力。它基于 Django、Vue.js 和 PostgreSQL 等流行框架构建，支持完全自托管部署，没有存储限制。无论是个人项目还是商业应用，Baserow 都能满足你的需求。最重要的是，所有非高级功能都采用 MIT 许可证开源，允许商业和私人使用。

项目地址：[点我跳转](https://gitlab.com/bramw/baserow)

## 部署安装

如需在外也打开项目需要配置内网穿透使用，点击前往 [帕斯内网穿透](https://passnat.com)，先注册好账号备用。
具体的操作方法可以 [点我查看](https://www.passnat.com/posts/p)内网穿透全平台上手攻略

### 最简单的方式 - Docker 单命令部署

Baserow 提供了最简洁的单命令部署方式，只需一行代码即可启动完整的 Baserow 实例：

```bash
docker run -v baserow_data:/baserow/data -p 80:80 -p 443:443 baserow/baserow:1.35.3
```

这个命令会：

创建一个名为 'baserow\_data' 的数据卷用于持久化存储所有数据
将容器的 80 端口（HTTP）映射到主机的 80 端口
将容器的 443 端口（HTTPS）映射到主机的 443 端口

**端口说明**：

80 端口：HTTP 访问端口，如果你的主机上已有其他服务占用，可以修改为其他端口，例如 '-p 8080:80' 表示通过主机的 8080 端口访问
443 端口：HTTPS 安全访问端口，同样可以根据需要修改

**存储说明**：

'baserow\_data' 是一个 Docker 数据卷，用于存储 Baserow 的所有数据，包括数据库、用户文件等。这个卷会自动创建，无需手动操作。

### 在 NAS 设备上部署

如果你使用飞牛 NAS 或其他 NAS 设备，可以按照以下步骤部署：

1. 打开 NAS 设备的管理界面，进入 Docker 应用管理模块
2. 点击 本地镜像 - 添加镜像 - 从 URL 添加
3. 在镜像地址栏填入：'baserow/baserow:1.35.3'
4. 等待镜像下载完成后，点击 启动 按钮启动容器

**容器配置步骤**：

勾选 开机自动启动 后进入下一步。

**端口映射设置**：

容器内部端口 80 映射到主机端口（建议 80 或 8080）
容器内部端口 443 映射到主机端口（建议 443 或 8443）

如果提示端口被占用，只需修改主机端口号即可，容器内的端口保持不变。

**存储位置设置**：

创建一个文件夹用于存储 Baserow 的数据，例如 '/mnt/nas/baserow\_data'
将其映射到容器内的 '/baserow/data' 目录
这样可以确保即使容器重启，所有数据也不会丢失

**环境变量设置**：

Baserow 的基础部署不需要设置任何环境变量，所有配置都已预设好。如果你需要进行高级配置，可以参考以下可选变量：

**可选设置的环境变量**：

'SECRET\_KEY'：Django 密钥，用于加密会话数据（如果不设置会自动生成）
'BASEROW\_PUBLIC\_URL'：Baserow 的公网访问地址，默认为 'http://localhost'
'DATABASE\_PASSWORD'：数据库密码（高级用户使用外部数据库时需要）
'REDIS\_PASSWORD'：Redis 缓存密码（高级用户使用外部 Redis 时需要）

对于大多数用户，直接点击 下一步 启动即可，无需修改这些变量。

### 访问 Baserow

部署完成后，你可以通过以下地址访问 Baserow：

本地访问：'http://localhost' 或 'http://你的NAS\_IP'
如果修改了端口：'http://你的NAS\_IP:8080'（根据你设置的端口号修改）

首次访问时，你需要创建一个账户。注册完成后就可以开始创建你的第一个数据库了。

### 使用建议

Baserow 会自动处理所有的数据库迁移和初始化，无需手动干预
建议定期备份 'baserow\_data' 数据卷中的数据
如果需要在外网访问，请配合内网穿透工具使用
Baserow 支持 API 接口，可以通过 API 进行二次开发

本篇教程结束。现在你已经拥有一个功能完整的无代码数据库系统，可以开始构建你的数据应用了！

---

原文：<https://www.passnat.com/posts/open-source-no-code-database-baserow-self-hosted-deployment-docker-one-click-installation-nas-intranet-penetration-remote-access-complete-guide>
