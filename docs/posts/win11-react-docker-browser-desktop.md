---
title: "Win11React网页版Windows11桌面一键Docker部署，浏览器在线体验完整系统界面"
date: 2025-10-16
description: "Win11React开源项目让你在浏览器中完整体验Windows11桌面。采用React和原生JavaScript打造，无需第三方库，包含开始菜单、文件浏览器、终端、计算器等丰富应用。支持Docker一键部署，适配NAS和跨平台使用，为前端开发者和系统爱好者提供轻量级解决方案。"
canonical: https://www.passnat.com/posts/win11-react-docker-browser-desktop
---

## 项目介绍

Win11React 是一个开源项目，致力于在网页上复现 Windows 11 桌面体验。该项目采用 React、CSS (SCSS) 和 JavaScript 等标准网页技术精心打造，无需任何第三方 UI 库，完全从零开始开发。项目不仅提供了美观逼真的 Windows 11 界面设计，还包含了开始菜单、搜索菜单、窗口管理、文件浏览器、终端、计算器、记事本等丰富的应用功能，让你可以在浏览器中体验完整的 Windows 11 操作系统界面。

无论是想要体验最新的 Windows 11 设计语言，还是对前端开发感兴趣的开发者，这个项目都能为你提供一个轻量级、跨平台的解决方案。

项目地址：[点我跳转](https://github.com/blueedgetechno/windows11)

## 部署安装

如需在外网也能访问项目，建议配置内网穿透服务。你可以选择 [帕斯内网穿透](https://passnat.com) 这样的专业工具，先注册账号备用。具体的操作步骤可以参考 [内网穿透全平台上手攻略](https://www.passnat.com/posts/p)，轻松实现远程访问。

### Docker 单命令快速启动

这是最简便的部署方式，适合所有用户。

打开你的 NAS 设备或任何支持 Docker 的系统，在终端中执行以下命令即可一键启动：

```bash
docker run -d --restart unless-stopped --name win11react -p 3000:3000 blueedge/win11react:latest
```

**命令说明：**

-d 表示后台运行

--restart unless-stopped 开机自动启动容器

--name win11react 为容器命名

-p 3000:3000 将容器内的 3000 端口映射到主机的 3000 端口

**端口配置：**

如果你的系统中 3000 端口已被占用，可以修改映射端口。例如，将其改为 8080：

```bash
docker run -d --restart unless-stopped --name win11react -p 8080:3000 blueedge/win11react:latest
```

这样容器内部仍然使用 3000 端口，但主机访问时使用 8080 端口。

**访问应用：**

等待容器启动完成后，在浏览器中访问：

```
http://你的IP地址:3000
```

或者如果修改了端口，访问：

```
http://你的IP地址:8080
```

此时你就能看到完整的 Windows 11 桌面体验了！

**部署完成！** 现在你已经成功部署了 Win11React，可以在浏览器中尽情体验这个精美的 Windows 11 模拟器。项目完全开源，如果你有任何建议或想要贡献代码，欢迎访问 GitHub 项目页面参与其中。

---

原文：<https://www.passnat.com/posts/win11-react-docker-browser-desktop>
