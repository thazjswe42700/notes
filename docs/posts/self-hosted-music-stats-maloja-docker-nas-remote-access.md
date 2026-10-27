---
title: "自建私人音乐统计库 Maloja Docker 一键部署 NAS 内网穿透远程访问"
date: 2025-10-16
description: "Maloja自托管音乐统计数据库系统，完全私密掌控你的听歌数据。记录收听历史、生成个性化统计，支持Spotify、Last.fm导入。Docker快速部署，数据存储在自己服务器，相比Last.fm更安全可靠。为音乐爱好者打造的私人音乐追踪平台。"
canonical: https://www.passnat.com/posts/self-hosted-music-stats-maloja-docker-nas-remote-access
---

## 项目介绍

Maloja 是一个简洁而强大的自托管音乐统计数据库系统，专为音乐爱好者打造。它能够记录你的每一次音乐收听历史，生成个性化的音乐统计数据，让你深入了解自己的音乐品味。

相比于 Last.fm 等在线服务，Maloja 的核心优势在于完全自主可控——你的所有数据都存储在自己的服务器上，不会被任何第三方同步或篡改。项目基于 Python 构建，提供了简洁易用的网页界面，支持多种音乐平台的数据导入，包括 Spotify、Last.fm 和 ListenBrainz。

无论你是音乐发烧友想要追踪听歌习惯，还是想要自建私人音乐统计系统，Maloja 都能满足你的需求。

项目地址：[点我跳转](https://github.com/krateng/maloja)

## 部署安装

若要在外网也能访问你的 Maloja 服务，建议配置内网穿透工具。推荐使用[帕斯内网穿透](https://passnat.com)，先注册账号以备使用。具体的部署方案可以[点我查看](https://www.passnat.com/posts/p)内网穿透完整上手指南。

### Docker 快速部署

打开你的 NAS 设备或任何支持 Docker 的系统，使用以下单条命令即可快速启动 Maloja：

```bash
docker run -p 42010:42010 -v $PWD/malojadata:/mljdata -e MALOJA_DATA_DIRECTORY=/mljdata -e MALOJA_SKIP_SETUP=true krateng/maloja
```

如果你在 Linux 系统上使用 Docker，为了避免文件权限问题，建议加上用户 ID 和组 ID。先执行以下命令获取你的 UID 和 GID：

```
id -u    # 获取 UID
id -g    # 获取 GID
```

然后使用完整的命令：

```bash
docker run -e PUID=1000 -e PGID=1001 -p 42010:42010 -v $PWD/malojadata:/mljdata -e MALOJA_DATA_DIRECTORY=/mljdata -e MALOJA_SKIP_SETUP=true krateng/maloja
```

注意：请将 '1000' 和 '1001' 替换为你实际的 UID 和 GID

### 通过 NAS 图形界面部署

如果你更习惯使用 NAS 的图形界面，可以按以下步骤操作：

打开你的 NAS 设备（飞牛、群晖、威联通等）

进入容器管理界面，选择 本地镜像 - 添加镜像 - 从 URL 添加

镜像地址填写：'krateng/maloja'，等待镜像下载完成

下载完成后点击启动按钮

### 容器配置说明

#### 端口映射

容器默认使用 **42010** 端口。如果你的系统中该端口已被占用，可以修改映射规则。例如改为 '8080:42010' 表示通过主机的 8080 端口访问容器内的 42010 端口。

#### 存储卷挂载

Maloja 需要持久化存储来保存你的音乐统计数据和配置文件。建议在 NAS 上创建一个专用文件夹（如 '/volume1/malojadata'），然后将其映射到容器内的 '/mljdata' 目录。这样你的数据即使容器重启也不会丢失。

#### 环境变量设置

以下是 Maloja 的关键环境变量：

变量名: MALOJA\_SKIP\_SETUP | 说明: 跳过交互式设置流程（容器必需） | 默认值: true | 必需: ✓ 必须

变量名: MALOJA\_FORCE\_PASSWORD | 说明: 设置管理员密码（首次运行时使用） | 默认值: 无 | 必需: 建议

变量名: MALOJA\_DATA\_DIRECTORY | 说明: 数据存储目录 | 默认值: /mljdata | 必需: ✓ 必须

变量名: PUID | 说明: 用户 ID（Linux 系统） | 默认值: 1000 | 必需: 可选

变量名: PGID | 说明: 组 ID（Linux 系统） | 默认值: 1001 | 必需: 可选

其他高级配置选项可在启动后通过 web 界面的管理面板进行调整。

### 访问应用

容器启动成功后，在浏览器中访问：

```
http://你的设备IP:42010
```

即可看到 Maloja 的主界面。如果你设置了管理员密码，使用该密码登录即可进入管理面板。

### 功能初始化

首次访问时，你可以：

配置 Last.fm 和 Spotify API 密钥（可选，用于显示专辑封面）

导入之前的听歌数据（支持 Spotify、Last.fm、ListenBrainz 等格式）

设置自己喜欢的统计规则和艺术家关联

本篇教程到此结束。现在你已经拥有了一个完全属于自己的音乐统计系统，可以随时查看你的听歌习惯和音乐品味演变过程了！

---

原文：<https://www.passnat.com/posts/self-hosted-music-stats-maloja-docker-nas-remote-access>
