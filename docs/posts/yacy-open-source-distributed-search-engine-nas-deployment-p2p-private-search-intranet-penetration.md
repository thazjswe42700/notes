---
title: "开源分布式搜索引擎YaCy自建方案,NAS极简部署P2P私密搜索,内网穿透远程访问"
date: 2025-10-16
description: "YaCy是开源分布式搜索引擎软件，支持P2P网络架构。提供完整搜索解决方案，包括索引服务器、Web前端和网络爬虫。可独立运行或协作搜索，尊重用户隐私。内置高效爬虫保持索引新鲜，支持企业内网部署和局域网服务发现。"
canonical: https://www.passnat.com/posts/yacy-open-source-distributed-search-engine-nas-deployment-p2p-private-search-intranet-penetration
---

## 项目介绍

YaCy 是一款功能强大的开源搜索引擎软件，它将搜索引擎的核心功能完全开放给用户。这是一个包含搜索索引服务器、Web 应用前端和生产级网络爬虫的完整解决方案。无论是个人用户还是企业用户，都可以通过 YaCy 搭建属于自己的搜索引擎。

YaCy 最大的特色是支持点对点（P2P）网络架构。每个 YaCy 实例都可以与其他 YaCy 节点协作，形成一个分布式搜索网络，让你能够瞬间访问海量搜索索引。同时，你也可以选择完全独立运行，仅使用本地索引进行搜索，这使得 YaCy 成为了真正尊重用户隐私的搜索解决方案。

YaCy 还内置了高效的网络爬虫和调度器，可以自动保持搜索索引的新鲜度。它甚至可以部署在企业内网环境中，作为商业企业搜索解决方案的替代品。此外，还有内置的网络扫描工具，可以轻松发现局域网内的 HTTP、FTP 和 SMB 服务器。

项目地址：[点我跳转](https://github.com/yacy/yacy_search_server)

## 部署安装

如果你想在公网上访问 YaCy，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体操作步骤可以参考 [这份全平台上手指南](https://www.passnat.com/posts/p)，让你轻松实现远程访问。

### 使用 Docker 快速部署

打开你的 NAS 设备或本地 Docker 环境，执行以下命令一键启动 YaCy：

```bash
docker run -d --name yacy_search_server \
  -p 8090:8090 \
  -p 8443:8443 \
  -v yacy_search_server_data:/opt/yacy_search_server/DATA \
  --restart unless-stopped \
  --log-opt max-size=200m \
  --log-opt max-file=2 \
  yacy/yacy_search_server:latest
```

**参数说明：**

-p 8090:8090 - 映射 Web 界面端口（HTTP）

-p 8443:8443 - 映射加密连接端口（HTTPS）

-v yacy\_search\_server\_data:/opt/yacy\_search\_server/DATA - 数据持久化存储，保存搜索索引和配置信息

如果 8090 或 8443 端口已被占用，你可以修改前面的端口号。例如想使用 8888 端口，改为 `-p 8888:8090` 即可，但容器内部端口保持不变。

### 访问 YaCy

部署完成后，在浏览器中访问：

```
http://localhost:8090
```

或者如果你在 NAS 上部署，使用 NAS 的 IP 地址：

```
http://你的NAS_IP:8090
```

页面加载后，你就可以看到 YaCy 的搜索界面了。

**默认账户信息：**

用户名：admin

密码：yacy

首次登录后，强烈建议立即修改默认密码。访问 `http://localhost:8090/ConfigAccounts\_p.html` 页面可以修改账户信息。

### 功能介绍

部署成功后，你可以使用以下功能：

Web 搜索 - 在本地或 P2P 网络中进行搜索

网络爬虫 - 自定义爬取网站内容并建立索引

索引管理 - 浏览和管理本地搜索索引

P2P 网络 - 可选择加入全球 YaCy 网络共享索引

隐私模式 - 完全本地搜索，不连接任何外部网络

本篇教程结束。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/yacy-open-source-distributed-search-engine-nas-deployment-p2p-private-search-intranet-penetration>


## 相关文章

- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
- [自托管即时通讯平台Stoat完整部署指南，内网穿透远程访问无公网限制](self-hosted-stoat-instant-messaging-deployment-guide-intranet-penetration-remote-access-no-public-ip.md)
- [DPanel Docker可视化管理面板一键部署，内网穿透远程访问容器，极简操作无需命令行](dpanel-docker-visual-management-panel-one-click-deployment-intranet-penetration-remote-access-containers-simple-operation-no-command-line.md)
- [免费在线虚拟钢琴演奏平台 AutoPiano NAS 极简部署 内网穿透远程弹奏](free-online-virtual-piano-autopiano-nas-simple-deployment-intranet-penetration-remote-playing.md)
- [Docker容器运行微信应用，Web远程访问与VNC连接，NAS一键部署搭建家庭微信服务器](docker-weixin-nas-vnc-remote-access.md)
