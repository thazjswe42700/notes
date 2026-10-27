---
title: "Hexon Hexo博客在线管理系统，图形化界面轻松管理文章，Docker极简部署内网穿透远程访问"
date: 2025-10-16
description: "Hexon是Hexo博客在线管理系统，提供直观图形化界面。支持文章编辑、页面管理、元数据模板和图片管理，无需命令行操作。采用TypeScript开发，性能稳定高效。Docker一键部署，适合个人博主和内容创作者快速管理博客，提高工作效率。"
canonical: https://www.passnat.com/posts/hexon-hexo-blog-management-system-graphic-interface-docker-deployment
---

## 项目介绍

Hexon 是一个功能强大的 Hexo 博客在线管理系统，它为 Hexo 用户提供了一个直观的图形化界面。通过 Hexon，你可以轻松管理博客文章、页面内容、前置元数据模板，并支持在线图片管理功能。无需繁琐的命令行操作，所有的 Git 操作、命令执行和内容管理都可以通过友好的网页界面完成。

Hexon 采用 TypeScript 开发，具有高效稳定的性能表现，是 Hexo 博客爱好者的必备工具。无论你是个人博主还是内容创作者，Hexon 都能让你的博客管理工作事半功倍。

项目地址：[点我跳转](https://github.com/gethexon/hexon)

## 部署安装

如需在外网也能访问你的博客管理后台，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，详细的部署指南请 [点我查看](https://www.passnat.com/posts/p) 内网穿透全平台快速上手教程。

**使用 Docker 快速部署**

打开你的 NAS 设备或本地 Docker 环境，执行以下命令一键启动 Hexon：

```bash
docker run -d \
  --name hexon \
  -p 5777:5777 \
  -v /path/to/your/hexo/blog:/app/hexo \
  -v hexon-data:/app/data \
  gethexon/hexon:latest
```

**参数说明：**

-p 5777:5777：将容器内的 5777 端口映射到主机。如果端口被占用，可以修改前面的端口号（如 -p 8080:5777），但容器内部端口必须保持为 5777
-v /path/to/your/hexo/blog:/app/hexo：将你本地的 Hexo 博客目录挂载到容器内。请将 /path/to/your/hexo/blog 替换为你实际的 Hexo 项目路径
-v hexon-data:/app/data：用于持久化存储 Hexon 的配置数据和用户信息

**首次使用步骤：**

1. 容器启动后，打开浏览器访问 http://你的IP:5777
2. 按照初始化向导完成设置，配置你的 Hexo 博客路径和 Git 信息
3. 设置管理员账号密码
4. 完成后即可开始使用 Hexon 管理你的博客

**环境变量设置（可选）：**

如需自定义配置，可添加以下环境变量：

```
-e NODE_ENV=production
-e PORT=5777
```

其他配置项均可在 Web 界面中直接设置，无需额外环境变量配置。

**创建成功后**

部署完成后，访问 http://你的服务器IP:5777 即可进入 Hexon 的管理后台。你现在可以：

📝 在线创建和编辑博客文章
🎨 管理前置元数据（Front-matter）
🖼️ 上传和管理博客图片
🔄 一键 Git 同步和发布
⚙️ 自定义脚本和命令

本篇教程结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/hexon-hexo-blog-management-system-graphic-interface-docker-deployment>
