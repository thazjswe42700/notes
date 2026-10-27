---
title: "我理解你的需求。根据VanBlog博客系统的内容，生成符合Google SEO最佳实践的标题：\n\nVanBlog个人博客系统Docker部署教程，NAS一键安装轻松搭建私有博客平台"
date: 2025-10-16
description: "VanBlog是一款简洁优雅的个人博客系统，采用现代技术栈构建。内置图床、流量统计、评论等功能，支持一键部署、自动HTTPS证书申请、黑暗模式和响应式设计。完美适配各种设备，Lighthouse评分接近满分，是搭建个人博客的理想选择。"
canonical: https://www.passnat.com/posts/vanblog-docker-deployment-tutorial-nas-one-click-install-private-blog
---

## 项目介绍

VanBlog 是一款简洁、实用、优雅的个人博客系统，采用现代化技术栈构建。它不仅包含完整的前后台管理系统，还内置了强大的图床、流量统计和评论功能。无论你是想要搭建个人博客、分享知识，还是记录生活，VanBlog 都能完美满足你的需求。

项目具有极致的响应速度，Lighthouse 评分接近满分。独特的按需全自动 HTTPS 证书申请功能让你无需手动配置域名就能获得安全连接。前后台都采用响应式设计，完美适配各种设备尺寸，支持黑暗模式自动切换。前台采用静态网页生成（SSG），支持秒级增量渲染，每次改动无需重新构建全部页面，大幅提升构建效率。

项目地址： [点我跳转](https://github.com/Mereithhh/vanblog)

## 部署安装

如果你需要在外网也能访问自己的博客，建议配置内网穿透。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样就能随时随地管理和访问你的博客。具体操作步骤可以 [点我查看](https://www.passnat.com/posts/p) 内网穿透全平台上手攻略。

VanBlog 提供了一键脚本部署的方式，非常适合技术小白快速上手：

```bash
curl -L https://vanblog.mereith.com/vanblog.sh -o vanblog.sh && chmod +x vanblog.sh && ./vanblog.sh
```

如果你更习惯使用 Docker Compose 的方式部署，下面为你介绍详细步骤。

## Docker Compose 部署教程

复制下列的配置文件到你的服务器，创建一个 `docker-compose.yml` 文件：

```yaml
version: '3'

services:
  vanblog:
    image: mereith/van-blog:latest
    restart: always
    environment:
      TZ: 'Asia/Shanghai'
      EMAIL: 'your-email@example.com'
    volumes:
      - ${PWD}/data/static:/app/static
      - ${PWD}/log:/var/log
      - ${PWD}/caddy/config:/root/.config/caddy
      - ${PWD}/caddy/data:/root/.local/share/caddy
    ports:
      - 80:80
      - 443:443
  mongo:
    image: mongo:4.4.16
    restart: always
    environment:
      TZ: 'Asia/Shanghai'
    volumes:
      - ${PWD}/data/mongo:/data/db
```

打开你的飞牛 NAS 或其他 NAS 设备，进入容器管理界面。

将上面的配置文件内容复制到 NAS 中，或者直接在 NAS 的 Docker 管理界面中创建这个配置文件。

**重要提示**：请修改配置文件中的 `EMAIL` 字段，填入你自己的邮箱地址。这个邮箱用于自动申请 HTTPS 证书，VanBlog 会自动为你的域名生成并续期证书，完全无需手动干预。

**端口配置说明**：配置文件中的 `80:80` 和 `443:443` 分别对应 HTTP 和 HTTPS 的端口映射。如果你的 NAS 上这些端口已被占用，可以修改前面的数字（宿主机端口），但容器内的端口（冒号后面的数字）需要保持不变。例如改为 `8080:80` 和 `8443:443`。

**存储位置说明**：

`/app/static` - 用于存储图床中的图片文件

`/var/log` - 存储系统日志

`/root/.config/caddy` - Caddy 反向代理配置存储

`/root/.local/share/caddy` - HTTPS 证书存储位置

`/data/db` - MongoDB 数据库存储位置

建议在 NAS 上提前创建这些文件夹，以便 Docker 容器能够正确挂载。

**环境变量设置**：

必须设置：

`EMAIL` - 你的邮箱地址，用于 HTTPS 证书申请和续期

`TZ` - 时区设置，默认为 `Asia/Shanghai`

其他配置均已使用默认值，无需修改。

在 NAS 的 Docker 管理界面中启动这个 Compose 配置后，系统会自动拉取 `mereith/van-blog` 和 `mongo:4.4.16` 两个镜像，并启动对应的容器。

部署完成后，打开浏览器访问 `http://你的NAS地址` 或 `https://你的NAS地址`（如果已配置域名），就能看到 VanBlog 的首页了。

进入后台管理界面，使用默认账号密码登录（首次部署时会提示设置），就可以开始创建文章、上传图片、管理博客了。

## 总结

VanBlog 凭借其简洁优雅的设计、完善的功能和快速的部署方式，成为了许多博主的首选。无论你是技术爱好者还是内容创作者，VanBlog 都能为你提供一个专业、稳定、易用的个人博客平台。现在就开始部署，享受属于自己的博客吧！

本篇教程结束。

---

原文：<https://www.passnat.com/posts/vanblog-docker-deployment-tutorial-nas-one-click-install-private-blog>


## 相关文章

- [自建私有云存储FileRun完整部署指南，Docker快速安装配置教程](filerun-private-cloud-storage-docker-deployment-guide.md)
- [开源自主翻译API LibreTranslate Docker部署指南 NAS一键搭建私人翻译服务](libre-translate-docker-nas-deployment-guide.md)
- [Webtop：NAS 上的网页桌面，Docker 部署 + 内网穿透](webtop-nas-browser-linux-desktop-docker.md)
- [极简网页仪表盘SimpleDash，本地快速部署，内网穿透远程访问，YAML配置自定义链接管理](simple-dash-local-dashboard-intranet-penetration-remote-access-yaml-config.md)
- [轻量级文件服务器 DUFS 快速部署指南 NAS Docker 一键启用文件共享搜索上传](lightweight-file-server-dufs-quick-deployment-guide-nas-docker-one-click-enable-file-sharing-search-upload.md)
