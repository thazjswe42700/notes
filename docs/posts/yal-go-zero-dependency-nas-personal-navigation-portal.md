---
title: "极简链接中心YAL，Go语言零依赖部署，NAS一键启动个人导航门户，内网穿透远程访问"
date: 2025-10-16
description: "YAL是一款轻量级Go语言开发的个人链接中心系统，单二进制文件运行，零依赖高效能。支持自定义品牌、搜索引擎集成、图标内联，完美适配家庭NAS、服务器和容器部署。本文详细介绍YAL的Docker快速部署方法、端口配置、存储卷挂载、环境变量设置及JSON配置文件编写等完整安装步骤，帮助用户快速搭建个人导航站和企业链接中心。"
canonical: https://www.passnat.com/posts/yal-go-zero-dependency-nas-personal-navigation-portal
---

## 项目介绍

YAL（Yet Another Landingpage）是一个轻量级、高效能的个人链接中心系统。它采用 Go 语言编写，以单个静态编译的二进制文件运行，提供了一个简洁美观的链接聚合展示平台。无论你是想要搭建个人导航站、企业内部链接中心，还是家庭 NAS 的快速访问门户，YAL 都能轻松胜任。

该项目具有极低的资源占用，支持自定义品牌、搜索引擎集成、图标内联等功能。最重要的是，它完全无依赖，运行安全可靠，非常适合在家庭 NAS、服务器或容器环境中部署。

项目地址：[点我跳转](https://github.com/timo-reymann/yal)

## 部署安装

如需在外网也能访问你的链接中心，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的操作步骤可以 [参考这篇教程](https://www.passnat.com/posts/p)，帮助你快速上手全平台内网穿透解决方案。

### 使用 Docker 快速部署

打开你的 NAS 设备或服务器的 Docker 管理界面，按照以下步骤操作：

**第一步：添加镜像**

点击'本地镜像'→'添加镜像'→'从 URL 添加'，在镜像地址栏填入：

```
timoreymann/yal:latest
```

等待镜像下载完成。

**第二步：启动容器**

下载完成后点击启动按钮，进入容器配置界面。

**第三步：配置基础设置**

勾选'开机自动启动'选项，这样 NAS 重启后容器会自动运行。

**第四步：端口映射设置**

在端口配置部分，设置以下映射关系：

容器内部端口：2024（这是 YAL 应用监听的端口）

宿主机端口：可设置为 2024 或其他未被占用的端口

如果提示端口 2024 已被占用，可以改为其他端口号（如 2025、3000 等），但容器内部端口保持 2024 不变。

**第五步：存储卷挂载**

YAL 需要挂载配置文件和资源文件。请在你的 NAS 中创建以下文件夹结构，然后进行挂载：

```
/data/yal/
├── config/          # 配置文件目录
├── images/          # 图片资源目录
└── icons/           # 图标文件目录（可选）
```

挂载映射关系：

宿主机 /data/yal/config → 容器 /app/config

宿主机 /data/yal/images → 容器 /app/images

宿主机 /data/yal/icons → 容器 /app/icons

**第六步：环境变量配置**

以下是可以设置的环境变量及其说明：

必须设置：

YAL\_PORT: 2024（应用监听端口）

YAL\_PAGE\_TITLE: 你的链接中心名称（如'我的导航中心'）

YAL\_CONFIG\_FOLDER: /app/config（配置文件路径）

YAL\_IMAGES\_FOLDER: /app/images（图片文件路径）

可选设置：

YAL\_MASCOT: mascot（左侧吉祥物图片名称，不需要扩展名）

YAL\_LOGO: logo（右侧 Logo 图片名称）

YAL\_BACKGROUND: background（背景图片名称）

YAL\_FAVICON: favicon（网站图标名称）

YAL\_BACKGROUND\_FILTER: blur(5px) brightness(0.9)（背景图片的 CSS 滤镜效果）

如果你只想快速体验，可以保留默认值，其他变量无需填写。

**第七步：启动应用**

完成以上配置后，点击'启动'或'确认'按钮，容器即可启动。

启动成功后，在浏览器中访问：

```
http://你的NAS_IP:2024
```

即可看到 YAL 链接中心的界面。

### 配置链接和搜索引擎

首次启动后，你需要配置链接内容。在挂载的 config 文件夹中创建两个 JSON 配置文件：

**items.json** - 配置链接内容：

```
[
  {
    'title': '常用工具',
    'entries': [
      {
        'text': 'GitHub',
        'link': 'https://github.com',
        'description': '全球最大代码托管平台',
        'icon': 'https://github.githubassets.com/favicons/favicon.svg'
      },
      {
        'text': 'Google',
        'link': 'https://google.com',
        'description': '搜索引擎',
        'icon': 'https://www.google.com/favicon.ico'
      }
    ]
  }
]
```

**searchEngines.json** - 配置搜索引擎：

```
[
  {
    'title': 'Google',
    'urlPrefix': 'https://www.google.com/search?q='
  },
  {
    'title': 'Bing',
    'urlPrefix': 'https://www.bing.com/search?q='
  }
]
```

修改配置文件后，重启容器即可生效。

## 总结

YAL 是一个开箱即用的链接中心解决方案，部署简单、功能完整、资源占用极低。通过 Docker 一键启动，配合简单的 JSON 配置文件，你就能拥有一个属于自己的个性化导航中心。无论是个人使用还是团队协作，YAL 都能为你提供高效的链接管理体验。

本篇教程结束

---

原文：<https://www.passnat.com/posts/yal-go-zero-dependency-nas-personal-navigation-portal>


## 相关文章

- [个人财务管理系统BudgetBee，Docker一键部署，NAS极简配置，多账户记账工具](budget-bee-docker-deployment-nas-multi-account-accounting.md)
- [开源时间追踪Kimai完整部署指南,NAS极简配置轻松打造专业计时系统](kimai-open-source-time-tracking-deployment-guide-nas-simple-configuration.md)
- [ZJSON在线JSON格式化编辑工具，支持转义压缩对比，NAS Docker一键部署，内网穿透远程访问使用](json-formatter-online-tool-escape-compress-compare-nas-docker-deployment-intranet-penetration.md)
- [Hoppscotch 接口测试台：NAS Docker 部署 + 内网穿透](hoppscotch-nas-docker-api-testing-workbench-intranet-penetration.md)
- [自建图片社区平台Szurubooru，Docker极简部署NAS，内网穿透远程访问图片库](szurubooru-docker-nas-deployment-intranet-penetration-remote-access-image-library.md)
