---
title: "Chibisafe 文件上传站：NAS Docker 部署 + 内网穿透"
date: 2026-10-20
description: "在 NAS 上用 Docker Compose 部署 Chibisafe 文件上传站：前端 + 后端 + Caddy 三个容器，文件存本地目录。装完先改默认管理员密码，再用帕斯内网穿透拿到公网地址，填进 Serve Uploads From，关掉公开注册，拖文件上传、建相册、复制直链发给朋友。含 compose.yml 与 Caddyfile 全文。"
canonical: https://www.passnat.com/posts/chibisafe-nas-docker-file-upload-share-link-intranet-penetration
---

## 项目介绍

Chibisafe 是一个自托管的文件上传站。把图片、文档、压缩包拖进网页，它给每个文件一个直链，复制出去对方点开就能看、能下载；文件按瀑布流排开，可以建相册，也能直接建文本片段分享。后台可以管用户、配额、单文件大小上限、允许和禁止的扩展名，大文件会自动分块上传，网络断一下也能续上。

它适合「经常要把文件发给别人、又不想每次都找地方传」的场景：自己 NAS 上开一个站，文件留在自己硬盘里，发出去的只是一个链接。项目地址：[点我跳转](https://github.com/chibisafe/chibisafe)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示。Chibisafe v6 拆成三个容器：前端、后端和一个 Caddy 反向代理，文件默认存在本地目录，不用接对象存储。装好之后先用默认管理员登录、把密码改掉，再用 [帕斯内网穿透](https://passnat.com) 拿到公网地址、关掉注册，最后才把地址发出去。

### 一、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 chibisafe，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  chibisafe:
    image: chibisafe/chibisafe:latest
    environment:
      - BASE_API_URL=http://chibisafe_server:8000
    expose:
      - 8001
    restart: unless-stopped

  chibisafe_server:
    image: chibisafe/chibisafe-server:latest
    environment:
      - ADMIN_PASSWORD=change-me-admin
    volumes:
      - ./database:/app/database:rw
      - ./uploads:/app/uploads:rw
      - ./logs:/app/logs:rw
    expose:
      - 8000
    restart: unless-stopped

  caddy:
    image: caddy:2-alpine
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile:ro
      - ./uploads:/app/uploads:ro
    ports:
      - "24424:80"
    environment:
      - BASE_URL=:80
    restart: unless-stopped
```

同一个目录里再建一个没有扩展名的文件，名字就叫 **Caddyfile**，内容如下（它负责把 /api 转给后端、把上传的文件按静态文件安全地发出去）：

```
{
	servers {
		trusted_proxies static private_ranges
		client_ip_headers X-Forwarded-For X-Real-IP
	}
}

{$BASE_URL} {
	route {
		@uploadedFile file {
			root /app/uploads
			try_files {path} {path}/index.html {path}/index.txt
		}
		handle @uploadedFile {
			header {
				Content-Security-Policy "sandbox; script-src 'none'; object-src 'none'; base-uri 'none'; form-action 'none'"
				X-Content-Type-Options nosniff
				Access-Control-Allow-Origin *
			}
			file_server {
				root /app/uploads
				pass_thru
			}
		}

		@api path /api/*
		reverse_proxy @api http://chibisafe_server:8000 {
			header_up Host {http.reverse_proxy.upstream.hostport}
			header_up X-Real-IP {http.request.header.X-Real-IP}
		}

		@docs path /docs*
		reverse_proxy @docs http://chibisafe_server:8000 {
			header_up Host {http.reverse_proxy.upstream.hostport}
			header_up X-Real-IP {http.request.header.X-Real-IP}
		}

		reverse_proxy http://chibisafe:8001 {
			header_up Host {http.reverse_proxy.upstream.hostport}
			header_up X-Real-IP {http.request.header.X-Real-IP}
		}
	}
}
```

编排里要改一处口令：

**ADMIN\_PASSWORD**：首次启动时创建的管理员口令，用户名固定是 admin。改成你自己的，登录后还可以再改。

端口 24424 被占用的话，把冒号左边的数字换一个，建隧道时的本地端口也跟着改。勾上「创建项目后立即启动」，第一次会拉三个镜像，起来之后稍等十几秒。

### 二、第一次登录，先改密码

浏览器打开 http://NAS的局域网地址:24424，用 admin 和你刚才设的口令登录。第一次进去先点左侧「Credentials」，在 Current password / New password / Repeat password 里把默认口令换掉，点 Change password。这一步要在建隧道之前做——公网地址一旦通电，admin 就是全网可见的登录名。

### 三、用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 24424（就是编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 四、把公网地址填进 Chibisafe

用公网地址打开站点，登录后进左侧「Admin → Settings」，在 Service 页签最上面的 **Serve Uploads From** 里，填你刚拿到的公网地址，要带 http:// 或 https://，保存。

这一项决定分享出去的链接长什么样：不填时文件链接指向容器内部的主机名，别人打不开，页头还会提示缩略图不可用；填好之后，缩略图、直链、相册链接都会用这个地址拼，复制出去就能直接用。

### 五、关掉公开注册

还是在 Settings，切到 Users 页签：

**Public Mode** 保持关闭——关着的时候，没登录的人不能往站里传文件。

**User Accounts** 也关掉——关着的时候，注册页只显示「注册已关闭」，新账号只能由管理员发邀请。

两个都关，保存。你自己的实例就变成了「只有你知道入口、只有你能上传」的文件站。

### 六、上传和分享

回到「Uploads」，点右上角「Upload file」，一次选多个文件（也可以直接拖进去）。传完卡片会出现在瀑布流里，点开任意一张，地址栏里就是它的直链，复制发给对方即可。

想按主题整理，就去「Albums」点「New album」建相册，把照片传进去，把相册链接发给朋友，对方打开就是一面图墙。

手机上同样能用：手机浏览器打开公网地址，登录后点 Upload file 选相册里的照片，传完复制链接就能发出去——文件一直存在你的 NAS 上，不经过第三方网盘。

### 几点说明

**数据在哪。** 元数据库在 ./database，文件在 ./uploads，日志在 ./logs，三个目录都在项目文件夹里；备份整个项目文件夹就够了，迁移时一起拷走。

**对象存储是可选的。** Settings → Uploads 里有 S3 一栏（Region / Bucket / Key / Endpoint），本教程不接，文件就存在 NAS 本地；以后要接 Backblaze、Wasabi 这类服务再填。

**明文 HTTP 访问。** 内网穿透给的是 http 地址，Chibisafe 本身不需要 HTTPS 才能用；但上传和登录都在明文里走，公网地址别到处发，用完可以在插件里把隧道关掉。

**界面是英文的。** 后台和前台都没有中文语言包，功能项不多，照着本文的页签名点即可。

**升级。** 镜像标签用的是 latest，升级前先备份项目文件夹，再到 Docker 里重新拉取镜像、重启项目；数据库迁移会在启动时自动执行。

---

原文：<https://www.passnat.com/posts/chibisafe-nas-docker-file-upload-share-link-intranet-penetration>
