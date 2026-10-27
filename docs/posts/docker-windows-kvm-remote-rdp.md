---
title: "Docker容器运行Windows系统 KVM加速极速部署 支持远程RDP连接访问"
date: 2025-10-16
description: "在Docker容器中运行完整Windows系统。Dockur Windows支持Windows 11、10、Server等多个版本，具备KVM加速、自动安装、Web浏览器访问和RDP远程连接功能。通过简单Docker命令或Compose配置，即可在Linux服务器、NAS设备上快速部署隔离的Windows环境，适合开发、测试和生产环境使用。"
canonical: https://www.passnat.com/posts/docker-windows-kvm-remote-rdp
---

## Docker 容器中的 Windows 系统

### 项目介绍

Dockur Windows 是一个创新的虚拟化解决方案，它将完整的 Windows 操作系统直接运行在 Docker 容器中。无论你是需要在 Linux 服务器上运行 Windows 应用，还是想要快速部署一个隔离的 Windows 环境，这个项目都能完美满足你的需求。

项目支持多个 Windows 版本，包括 Windows 11、Windows 10、Windows Server 系列，甚至还支持老版本的 Windows XP 和 Windows 2000。它具备 KVM 加速、ISO 自动下载、Web 浏览器访问等强大功能，让你可以像使用普通虚拟机一样轻松使用 Windows，但部署速度快得多。

最棒的是，整个安装过程完全自动化，你只需启动容器就能坐享其成。无论是个人使用还是企业部署，都能轻松应对。

项目地址：[点我跳转](https://github.com/dockur/windows)

### 部署安装

如果你需要在远程访问这个 Windows 系统，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册好账号以备不时之需。详细的跨平台部署指南可以 [点我查看](https://www.passnat.com/posts/p)。

#### 最快速部署方案

打开你的 NAS 设备（飞牛 NAS、群晖、威联通等）或 Linux 服务器，使用以下 Docker 命令一键启动：

```bash
docker run -it --rm --name windows \
  -p 8006:8006 \
  -p 3389:3389/tcp \
  -p 3389:3389/udp \
  --device=/dev/kvm \
  --device=/dev/net/tun \
  --cap-add NET_ADMIN \
  -v "${PWD:-.}/windows:/storage" \
  --stop-timeout 120 \
  dockurr/windows
```

执行这条命令后，Docker 会自动下载 Windows 11 Pro 镜像（约 7.2GB），并在容器中完成全自动安装。

#### Docker Compose 方案

如果你更习惯使用 Docker Compose，可以复制下列配置文件：

```yaml
version: '3'
services:
  windows:
    image: dockurr/windows
    container_name: windows
    environment:
      VERSION: "11"
    devices:
      - /dev/kvm
      - /dev/net/tun
    cap_add:
      - NET_ADMIN
    ports:
      - 8006:8006
      - 3389:3389/tcp
      - 3389:3389/udp
    volumes:
      - ./windows:/storage
    restart: always
    stop_grace_period: 2m
```

将上述内容保存为 'docker-compose.yml' 文件，然后在文件所在目录执行：

```
docker-compose up -d
```

容器会在后台运行，并在系统重启时自动启动。

#### 配置说明

**端口映射设置：**

'8006' - Web 浏览器访问端口（用于安装过程和低质量远程查看）
'3389' - RDP 远程桌面协议端口（推荐使用此方式连接，画质更好）

如果这些端口在你的系统中已被占用，可以修改前面的数字。例如，将 '8006:8006' 改为 '8007:8006' 表示通过本机的 8007 端口访问容器内的 8006 端口。

**存储位置配置：**

容器会在 './windows' 目录下创建虚拟磁盘文件。如果你想更改存储位置，只需修改 volumes 中的路径即可。建议为其分配足够的磁盘空间，因为 Windows 系统盘默认为 64GB。

#### 环境变量配置

**必须设置：**

无（所有参数都有合理的默认值）

**推荐设置：**

变量名 | 说明 | 默认值 | 示例
VERSION | Windows 版本 | 11 | 可选：11、10、8e、2025、2022 等
DISK\_SIZE | 虚拟磁盘大小 | 64G | 256G、512G 等
RAM\_SIZE | 分配内存 | 4G | 8G、16G 等
CPU\_CORES | 分配 CPU 核心数 | 2 | 4、8 等
USERNAME | Windows 用户名 | Docker | 任意用户名
PASSWORD | Windows 密码 | admin | 任意密码
LANGUAGE | Windows 语言 | English | French、German、Chinese 等

**可选设置：**

'REGION' - 地区设置（如 en-US）
'KEYBOARD' - 键盘布局（如 en-US）
'MANUAL' - 手动安装模式（Y/N，不推荐）

其他变量保持默认即可，直接启动容器。

#### 开始使用

1. 启动容器后，打开浏览器访问 'http://你的服务器IP:8006'
2. 等待自动安装，整个过程大约需要 5-10 分钟，取决于网络速度和硬件配置
3. 看到 Windows 桌面，说明安装已完成，你可以开始使用 Windows 系统了
4. 后续连接，建议使用 RDP 客户端连接（Windows 用户可按 Win+R 输入 'mstsc'），这样画质更清晰、响应更快速

#### 高级功能

**共享文件夹：** 安装完成后，桌面上会出现 'Shared' 文件夹，可用于与宿主机交换文件。

**扩展磁盘：** 如需更大的存储空间，可在环境变量中设置 'DISK\_SIZE: "256G"' 来扩展磁盘容量。

**RDP 远程连接：** 默认用户名为 Docker，密码为 admin（可通过环境变量修改）。

### 总结

Dockur Windows 提供了一种全新的 Windows 部署方式，相比传统虚拟机更加轻量、快速、易于管理。无论你是开发者、系统管理员还是普通用户，都能通过简单的一条命令或几行配置快速拥有一个完整的 Windows 环境。赶快试试吧！

---

原文：<https://www.passnat.com/posts/docker-windows-kvm-remote-rdp>


## 相关文章

- [DweebUI容器管理面板轻松上手，Docker Podman全能卫士一键部署运维](dweebui-container-management-panel-docker-podman-deployment.md)
- [免费私有相册系统Lomorage，Docker快速部署NAS，内网穿透远程访问家庭影库](lomorage-free-private-photo-gallery-docker-nas-deployment-remote-access.md)
- [家庭私人照片管理系统PhotoStructure，NAS一键部署，内网穿透远程访问你的回忆](photostructure-nas-private-photo-management-remote-access.md)
- [阿里云盘CLI命令行工具NAS部署指南，本地文件云端备份同步方案详解](aliyun-pan-cli-nas-deployment-guide-local-cloud-backup-sync-solution.md)
- [无状态密码管理器Heimdallr，一密掌控所有账户，NAS本地部署零风险，内网穿透远程访问无忧](heimdallr-stateless-password-manager-nas-local-deployment.md)
