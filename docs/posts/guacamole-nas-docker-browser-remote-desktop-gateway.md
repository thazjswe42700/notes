---
title: "Guacamole 自建桌面网关：NAS Docker 部署 + 浏览器远控"
date: 2026-10-17
description: "开源的浏览器远程桌面网关：三个容器怎么分工、为什么必须共用 compose 网络、首次怎么建表、RDP 连接怎么填。"
canonical: https://www.passnat.com/posts/guacamole-nas-docker-browser-remote-desktop-gateway
---

## 项目介绍

Apache Guacamole 是 Apache 基金会的开源项目，做的是「无客户端远程桌面」：你在浏览器里打开一个网页，就能操作另一台电脑的桌面。那台电脑不用装任何客户端，只要它开了 RDP、VNC 或 SSH 就行。

它把东西拆成三块：**guacamole** 是网页和应用服务（登录、连接列表、权限都在这层）；**guacd** 是真正的协议代理，由它去跟目标机的 RDP / VNC / SSH 打交道，再把画面转成浏览器能显示的东西；**postgres** 存用户、连接和授权。三个一起跑，才算一套完整的桌面网关。

项目地址：[点我跳转](https://github.com/apache/guacamole-server)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示。三个容器用一份 Compose 编排起，占用一个 Docker 网段。

### 一、想在外面用，先把 8080 放出去

只在局域网里用，这一步可以跳过，直接看第二步。要出门用手机或公司电脑连回家，就在飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」：名字随便起（不超过 12 个字符），本地 IP 留默认的 127.0.0.1，本地端口填 **8080**，点开、按一下电源，状态变成运行中，详情里那个公网地址就是你在外面要打开的网址。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 guacamole，路径在「我的文件」下面挑一个空文件夹，来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  db:
    image: postgres:15-alpine
    restart: unless-stopped
    environment:
      POSTGRES_DB: guacamole_db
      POSTGRES_USER: guac
      POSTGRES_PASSWORD: 换成你自己的口令
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U guac -d guacamole_db"]
      interval: 5s
      retries: 12

  guacd:
    image: guacamole/guacd:latest
    restart: unless-stopped

  guacamole:
    image: guacamole/guacamole:latest
    restart: unless-stopped
    ports:
      - "8080:8080"
    depends_on:
      db:
        condition: service_healthy
      guacd:
        condition: service_started
    environment:
      GUACD_HOSTNAME: guacd
      POSTGRESQL_HOSTNAME: db
      POSTGRESQL_DATABASE: guacamole_db
      POSTGRESQL_USER: guac
      POSTGRESQL_PASSWORD: 换成你自己的口令

volumes:
  pgdata:
```

**三个服务必须在同一个网络里。** 你看最后一段环境变量：**GUACD\_HOSTNAME: guacd**、**POSTGRESQL\_HOSTNAME: db**，填的就是上面两个服务的名字 —— 少了这个默认网络，这几个名字解析不出来，guacamole 连不上 guacd 也连不上数据库，网页能开、一登录就报错。所以别再给三个服务各写一行 network\_mode: bridge。

两个口令位（db 和 guacamole 里各一处）填成一样的东西，换成你自己的；它们只是占位，别照抄。8080 是网页端口，NAS 上这个端口如果被别的项目占了，把前面那个数字改掉就行。

勾上「创建项目后立即启动」，点确认。三个镜像下完，db 和 guacd 先起来，guacamole 会等数据库健康检查通过再启动。

### 三、第一次要先建表

空数据库里没有表，网页打开也是空的。官方的做法是用镜像里的脚本生成一次表结构，喂给 postgres：先生成 SQL，再导进去。

```bash
docker run --rm guacamole/guacamole /opt/guacamole/bin/initdb.sh --postgresql > initdb.sql
cat initdb.sql | docker exec -i guacamole-db-1 psql -U guac -d guacamole_db
```

跑完库里有二十来张表，再刷新网页就能登录了。这一步只需要做一次，重装或者换库才要再来一遍。

### 四、登录，第一件事改密码

默认账号是 **guacadmin / guacadmin** —— 全世界都知道。登录进去第一件事，右上角用户名 →「设置」→「用户」，点开 guacadmin 改一个新口令，再保存。这个网页要是被放到公网上，弱口令等于把家里的电脑交给别人。

### 五、新建一条连接（以 RDP 为例）

「设置」→「连接」→「新建连接」。表单分几段，填这几格就够：

**名称**：自己看得懂就行，比如「家里那台电脑」；**协议**：选 RDP（Windows 桌面）；**网络** 里的**主机名**填那台电脑在局域网里的地址，**端口**留空就是默认的 3389；**认证** 里填那台电脑的用户名和密码。

Windows 那边要打开「远程桌面」开关，账号要有密码。保存之后回到首页，「全部连接」里就多了一行，点它就开始连。

### 六、连上之后

画面直接在网页里出来，整屏就是那台电脑的桌面，鼠标键盘都跟着走。左上角碰一下会出来一条菜单：返回连接列表、上传下载文件、调参数（分辨率、剪贴板）。剪贴板两边是通的，复制一段字可以直接粘过去；文件也能直接传，不用装同步盘。

手机浏览器同样能开，横屏用起来最舒服 —— 这就是「无客户端」的好处：换台设备只要记得地址和账号。

### 几点说明

**别把 8080 直接暴露到公网。** Guacamole 本身只有账号密码一层，公网裸奔会被扫。外面要用就走隧道（第一步那种），或者前面再套一层带口令的反向代理。

**guacd 不需要端口映射。** 它只跟 guacamole 在同一个 Docker 网络里说话，宿主上不用开 4822；开了反而是多余的入口。

**initdb 只需要跑一次。** 重复跑会因为表已存在报错，不影响已经起好的服务，但没必要。

**端口冲突。** 宿主的 8080 很热门，被占了就改编排里冒号前面那个数字，容器里的 8080 不用动。

**更新。** 三个镜像一起更新（guacamole 和 guacd 版本要对得上），数据库那份数据卷留着，连接和用户不会丢。

---

原文：<https://www.passnat.com/posts/guacamole-nas-docker-browser-remote-desktop-gateway>
