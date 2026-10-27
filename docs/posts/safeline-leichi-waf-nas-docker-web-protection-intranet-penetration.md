---
title: "SafeLine 雷池 WAF：NAS Docker 部署 + 内网穿透"
date: 2026-10-19
description: "在 NAS 上用 Docker Compose 部署开源 WAF 雷池：添加要保护的站点，把访问入口换成它，正常的请求放行、可疑的按规则拦截，攻击日志里记下来源和类型。配 PassNAT 内网穿透，人在外面也能打开管理页。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/safeline-leichi-waf-nas-docker-web-protection-intranet-penetration
---

## 项目介绍

SafeLine（中文名雷池）是长亭科技开源的 Web 应用防火墙。它站在你的网站或 NAS 服务前面，把进来的 HTTP 请求先过一遍：正常的放行，命中注入、XSS、命令注入、路径穿越等规则的直接拦下，并在控制台留下攻击日志。

它不是单个容器，而是一组服务一起跑：管理端负责控制台和配置，检测引擎做规则匹配，反向代理把请求转发给被保护的服务，另外还有数据库和几个辅助服务。下面这份编排把这些都写好了。

项目地址：[点我跳转](https://github.com/chaitin/SafeLine)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先部署 SafeLine，再用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，人在外面也能打开管理页。

### 一、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 safeline，路径在「我的文件」下面挑一个空文件夹，来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
networks:
  safeline-ce:
    name: safeline-ce
    driver: bridge

services:
  postgres:
    image: chaitin/safeline-postgres:15.2
    restart: always
    environment:
      POSTGRES_USER: safeline-ce
      POSTGRES_PASSWORD: change-me-2026
    volumes:
      - ./data/postgres:/var/lib/postgresql/data
    healthcheck:
      test: pg_isready -U safeline-ce -d safeline-ce
    networks:
      safeline-ce:
        aliases: [safeline-pg]

  mgt:
    image: chaitin/safeline-mgt:9.2.7
    restart: always
    ports:
      - "9443:1443"
    environment:
      MGT_PG: postgres://safeline-ce:change-me-2026@safeline-pg/safeline-ce?sslmode=disable
    volumes:
      - ./data/mgt:/app/data
      - ./logs/nginx:/app/log/nginx
      - ./data/sock:/app/sock
      - /var/run:/app/run
    depends_on:
      - postgres
      - fvm
    healthcheck:
      test: curl -k -f https://localhost:1443/api/open/health
    networks:
      safeline-ce:
        aliases: [safeline-mgt]

  detector:
    image: chaitin/safeline-detector:9.2.7
    restart: always
    environment:
      LOG_DIR: /logs/detector
    volumes:
      - ./data/detector:/resources/detector
      - ./logs/detector:/logs/detector
    networks:
      safeline-ce:
        aliases: [safeline-detector]

  tengine:
    image: chaitin/safeline-tengine:9.2.7
    restart: always
    ports:
      - "8080:80"
      - "8443:443"
    environment:
      TCD_MGT_API: https://safeline-mgt:1443/api/open/publish/server
      TCD_SNSERVER: safeline-detector:8000
      SNSERVER_ADDR: safeline-detector:8000
      CHAOS_ADDR: safeline-chaos
    volumes:
      - ./data/nginx:/etc/nginx
      - ./data/detector:/resources/detector
      - ./data/chaos:/resources/chaos
      - ./logs/nginx:/var/log/nginx
      - ./data/cache:/usr/local/nginx/cache
      - ./data/sock:/app/sock
    networks:
      safeline-ce:
        aliases: [safeline-tengine]

  luigi:
    image: chaitin/safeline-luigi:9.2.7
    restart: always
    environment:
      MGT_IP: safeline-mgt
      LUIGI_PG: postgres://safeline-ce:change-me-2026@safeline-pg/safeline-ce?sslmode=disable
    volumes:
      - ./data/luigi:/app/data
    depends_on:
      - detector
      - mgt
    networks:
      safeline-ce:
        aliases: [safeline-luigi]

  fvm:
    image: chaitin/safeline-fvm:9.2.7
    restart: always
    networks:
      safeline-ce:
        aliases: [safeline-fvm]

  chaos:
    image: chaitin/safeline-chaos:9.2.7
    restart: always
    environment:
      DB_ADDR: postgres://safeline-ce:change-me-2026@safeline-pg/safeline-ce?sslmode=disable
    volumes:
      - ./data/sock:/app/sock
      - ./data/chaos:/app/chaos
    networks:
      safeline-ce:
        aliases: [safeline-chaos]
```

整段编排里只有几处需要留意：

**数据库口令**：把 change-me-2026 换成自己的口令，POSTGRES\_PASSWORD、MGT\_PG、LUIGI\_PG、DB\_ADDR 四处要一致。

**管理端口 9443**：控制台的入口。浏览器打开 https://NAS地址:9443，自签证书会提示不安全，继续访问就行。

**反向代理端口**：tengine 在容器里监听 80 和 443；NAS 自己的管理页面通常占着这两个端口，所以编排里发布成 8080 和 8443。被占用就换成空闲端口，后面添加应用时按这个端口访问。

勾上「创建项目后立即启动」。第一次起来以后，在安装目录的终端里执行 **docker compose exec mgt /app/mgt-cli reset-admin --once**，会生成管理员口令；用 admin 和新口令登录控制台。

### 二、用帕斯内网穿透拿公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起，本地地址留默认的 127.0.0.1，本地端口填 9443（就是控制台端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址，先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 三、添加要保护的应用

登录控制台，点「防护应用」→「添加应用」：

**域名**：填你访问这个服务用的域名；没有域名可以先填一个占位，比如 nas.local，访问时带上对应的 Host。

**端口**：SafeLine 上监听的端口，默认 80；要用 HTTPS 就在旁边配上证书。

**上游服务器**：填被保护服务的地址和端口，比如 http://你的服务:8080，不支持带路径。

防护模式先用默认，点提交。之后把访问入口换成 SafeLine 的地址（比如 http://NAS地址:8080），请求就先经过 WAF，再到你的服务。

### 四、看拦截结果

正常请求照常返回 200；带注入、XSS 等特征的请求会被直接拦下，返回 403。攻击日志里能看到时间、来源 IP、攻击类型和请求地址，统计报表里请求次数、拦截次数、拦截率都会跟着变。

**注意：** 测试时只对自己的测试站点发测试请求，不要对别人的站点发。

### 几点说明

**数据在哪。** 配置和日志在安装目录的 data 下：postgres 存配置和日志索引，mgt、detector、nginx 等各有自己的目录。备份整个安装目录就够。

**管理页的证书。** 默认是自签证书，公网访问时浏览器会提示不安全，继续即可；在意的话可以给管理页配自己的证书。

**为什么改成端口映射。** 官方编排为了让反向代理拿到真实客户端 IP，用的是 host 网络。这里为了不和 NAS 自己的 80/443 冲突，改成 bridge 加端口映射，家用够用；如果要上线到正式环境，按官方方式调整。

**升级。** 换镜像 tag 后重新拉取、重启项目即可；升级前先备份 data 目录。

**资源。** 检测引擎比较吃内存，NAS 内存小于 4GB 的话，先看一眼剩余内存再装。

---

原文：<https://www.passnat.com/posts/safeline-leichi-waf-nas-docker-web-protection-intranet-penetration>


## 相关文章

- [Jellyfin 家庭影院：飞牛 NAS Docker 部署 + 内网穿透，出门手机接着看](jellyfin-nas-media-server-private-cinema.md)
- [Navidrome 飞牛 NAS 部署与公网听歌](navidrome-docker-private-music-server.md)
- [Mealie 飞牛 NAS 部署与手机采购清单](mealie-private-recipe-library-nas-deployment-35-languages-remote-access.md)
- [AdventureLog 自建旅行地图：NAS Docker 部署 + 行程分享](open-source-travel-log-adventurelog-docker-deploy-nas-personal-map.md)
- [qBittorrent 飞牛 NAS 部署与远程下载](qbittorrent-nas-remote-bt-downloader-deploy-intranet-penetration.md)
