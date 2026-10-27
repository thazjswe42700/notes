---
title: "Nginx Proxy Manager：NAS Docker 统一入口 + 内网穿透"
date: 2026-10-19
description: "在 NAS 上用 Docker Compose 部署 Nginx Proxy Manager：一个网页管理所有服务的域名和入口，新增 Proxy Host 把域名转发到容器端口，支持 WebSocket 和常见攻击拦截；用帕斯内网穿透拿公网地址，手机在外面也能打开家里的服务。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/nginx-proxy-manager-nas-docker-unified-entry-intranet-penetration
---

## 项目介绍

Nginx Proxy Manager 是一个给 Nginx 套了网页界面的反向代理管理器。它自己不提供相册、笔记或者下载功能，它解决的是另一个问题：NAS 上的服务越装越多，每个都要记一个端口，家里人和朋友根本记不住。装上它之后，对外只留一个入口，按域名把请求分到后面不同的容器。

界面里有 Proxy Hosts、Redirection Hosts、Streams、404 Hosts 四类规则，最常用的是 Proxy Host：填一个域名，再填要转发到的容器名和端口，保存就让 Nginx 生成对应的配置。表单里能顺手打开 WebSocket 支持、拦掉常见的攻击请求，证书也可以在这里统一申请和续期。

它是单容器加 SQLite，数据就在 /data 一个目录里，备份和搬家都简单。项目地址：[点我跳转](https://github.com/NginxProxyManager/nginx-proxy-manager)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示。飞牛自带的网页服务和下载服务已经占了 80 和 443，所以编排里把 NPM 的三个端口挪到 8181、8081、8443，不跟系统服务打架。

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 npm，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  npm:
    image: jc21/nginx-proxy-manager:latest
    container_name: nginx-proxy-manager
    restart: unless-stopped
    ports:
      - "8181:81"    # 管理界面
      - "8081:80"    # 代理入口 HTTP
      - "8443:443"   # 代理入口 HTTPS
    volumes:
      - npm_data:/data
      - npm_letsencrypt:/etc/letsencrypt
  demo:
    image: nginx:alpine
    container_name: npm-demo
    restart: unless-stopped

volumes:
  npm_data:
  npm_letsencrypt:
```

编排里三个端口对应三件事：

**8181→81**：管理界面。装完先用 http://NAS地址:8181 打开它。

**8081→80**：代理入口的 HTTP 端口。以后所有走 NPM 的网站都从这个口进来。

**8443→443**：代理入口的 HTTPS 端口，配了证书之后走这个。

最后的 demo 服务只是给你做实验用的：它没有对外端口，只能被 NPM 转发，用来验证「一个域名指到一个容器」这件事。验证完可以在编排里删掉，也可以换成你自己的服务。

勾上「创建项目后立即启动」，等镜像拉完。NPM 起来之后会在 /data 里建库、生成默认配置，第一次打开需要几十秒。数据都在 npm\_data 和 npm\_letsencrypt 两个具名卷里，备份这两个卷就够了。

### 一、第一次打开：创建管理员

用 http://NAS地址:8181 打开管理界面。全新实例不是登录页，而是让你创建管理员：填名字、邮箱、密码，点 Save。这个邮箱是以后找回密码用的，密码自己记好。

创建完就进仪表盘，四张卡片分别数着 Proxy Hosts、重定向、TCP 流和 404 规则。以后所有配置都在左边这几个菜单里：Hosts 下面管转发，Certificates 管证书，Access Lists 管谁能访问。

### 二、加第一个 Proxy Host

点 Hosts → Proxy Hosts → Add Proxy Host，弹出表单，只填 Details 这一页：

**Domain Names**：填你准备给这个服务用的域名。局域网里可以先用 demo.lan 这种本地域名做实验；要在外面用，就填你自己的真实域名，并且让它解析到 NPM 的入口。

**Scheme**：后面那个服务是 http 就选 http，是 https 就选 https。

**Forward Hostname / IP**：填容器名或者容器所在机器的地址。跟 NPM 在同一个 Compose 项目里，直接填容器名就行，比如 demo；跨项目就填 NAS 的局域网地址。

**Forward Port**：后面那个服务容器内部的端口，比如 demo 的 80。注意不是宿主端口。

下面的 Options 建议把 Block Common Exploits 和 Websockets Support 都打开：前者挡掉常见的扫描和注入请求，后者让一些带实时刷新的应用能正常连。Cache Assets 按需开，静态站点开了会快一些，后台类应用别开。

点 Save，列表里出现一行 demo.lan → http://demo:80，状态是 Online。这时用浏览器打开 http://demo.lan:8081（端口换成你编排里的入口端口），看到的就是 demo 容器的页面。

**域名要先能解析。** NPM 是按请求里的 Host 分发的，域名解析不到这台 NAS，请求就到不了 NPM。局域网里最简单的办法是在路由器里加一条 DNS 记录，或者在各台设备的 hosts 文件里写一行；不想折腾 DNS 的话，也可以在 Proxy Host 的域名里直接填 NAS 的地址，但换机器就得改，不推荐。

### 三、多个服务怎么分

再点一次 Add Proxy Host，给第二个服务填另一个域名，转发到另一个容器名和端口。以后访问 photo.你的域名 进相册，访问 notes.你的域名 进笔记，外面看起来是几个独立网站，背后其实是同一台 NAS、同一个 8081 入口，NPM 按域名把请求分过去。

如果服务之间是路径区分（比如 /photo 和 /notes 在同一个域名下），可以在 Custom Locations 里加规则；但很多应用生成链接时不知道自己被挂在子路径下，容易白屏或者丢样式。能用子域名就用子域名，这是最省事的分法。

### 四、在外面用：给入口做内网穿透

NPM 的入口只在局域网里，出门就用不了。用 [帕斯内网穿透](https://passnat.com) 把 8081 这个入口映射出去：飞牛「应用中心」里搜 PassNAT，装上打开，点「创建隧道」。名字随便起，本地地址留默认的 127.0.0.1，本地端口填 8081，建好点开隧道按下电源，详情里就有公网地址。

如果 PassNAT 给的是带域名的 HTTP 隧道，把这个域名填进 Proxy Host 的 Domain Names，外面访问的域名和家里保持一致，NPM 的规则不用改。不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 五、关于 SSL 证书

SSL 标签页可以给域名申请 Let us Encrypt 证书，但它要求域名真的解析到这台机器、并且外面能访问到 80 或 443 端口。只在内网实验、没有真实域名的时候，让 SSL 保持 None，用 HTTP 访问就行；等域名和穿透都稳定了，再回来点 Request a new SSL Certificate，勾上强制 HTTPS 和同意条款。

证书申请失败最常见的原因是域名解析还没生效、或者 80 端口被别的服务占了。先把 http://你的域名 打开确认能到 NPM，再申请证书，顺序别反。

### 几点说明

**数据在哪。** 配置和 SQLite 库都在 /data，对应 npm\_data 卷；证书在 /etc/letsencrypt，对应 npm\_letsencrypt 卷。换机器时把这两个卷带走，域名和转发规则都还在。

**升级。** 镜像标签是 latest，升级前先备份两个卷，再拉新镜像、重启项目。大版本升级偶尔会改数据库结构，容器启动时会自己迁移，别在迁移中途断电。

**安全。** 管理界面 8181 不要直接暴露到公网，需要远程管理就用穿透临时开一下，用完关掉；Proxy Host 只开你真要给别人用的服务，数据库、Docker 管理这类后台不要往公网放。

**它不是万能的。** NPM 只管转发和证书，不负责应用本身的登录和权限。每个服务该设的密码、该关的注册入口，还是要进各自的后台设。

---

原文：<https://www.passnat.com/posts/nginx-proxy-manager-nas-docker-unified-entry-intranet-penetration>


## 相关文章

- [家庭反向代理网关解决方案 Nginx代理管理器Docker快速部署指南](home-reverse-proxy-gateway-nginx-proxy-manager-docker-deployment-guide.md)
- [Nginx Proxy Manager Docker部署指南，内网穿透反向代理网关搭建教程配置](nginx-proxy-manager-docker-deployment-guide-intranet-penetration-reverse-proxy-gateway-setup-tutorial-configuration.md)
- [PdfDing：NAS 上的 PDF 阅读库，Docker 部署 + 内网穿透](pdfding-nas-docker-pdf-reader-progress-highlights-intranet-penetration.md)
- [沉浸式虚拟办公平台 SkyOffice，NAS Docker 极简部署，远程团队协作新体验](skyoffice-nas-docker-remote-team-collaboration.md)
- [NAS部署Authelia身份认证服务，Docker快速部署SSO单点登录系统，内网穿透远程安全访问](nas-authelia-docker-sso-ddns.md)
