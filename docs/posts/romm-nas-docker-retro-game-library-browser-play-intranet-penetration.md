---
title: "RomM 老游戏库：NAS Docker 部署 + 内网穿透"
date: 2026-10-19
description: "在 NAS 上用 Docker Compose 部署开源游戏库 RomM：把 ROM 按平台放好，扫描后自动匹配封面、简介和发行年份，浏览器里直接玩，存档留在自己家；用帕斯内网穿透拿公网地址，出门在手机也能打开。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/romm-nas-docker-retro-game-library-browser-play-intranet-penetration
---

## 项目介绍

RomM 是一个开源的 ROM 管理器：把老游戏按平台放进文件夹，扫一遍，封面、简介和发行年份自动配好；点开游戏详情，按「开始游玩」就在浏览器里跑起来，不用另外装模拟器。存档和即时存档也留在自己家里。

它管的是「游戏文件 + 元数据 + 在浏览器里玩」，不是云端游戏平台。界面默认中文，手机打开会自动变成竖版布局。

项目地址：[点我跳转](https://github.com/rommapp/romm)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 RomM 用 Docker Compose 装起来。编排里没有必须预填公网地址的字段，先装后建隧道也可以；本文按「先穿透、后部署」的顺序写，出门在手机或平板上也能打开自己的游戏库。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 8080（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 romm，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去，只需要改三处：

```yaml
services:
  romm:
    image: rommapp/romm:latest
    restart: unless-stopped
    environment:
      - DB_HOST=romm-db
      - DB_NAME=romm
      - DB_USER=romm-user
      - DB_PASSWD=换成你自己的数据库口令
      - ROMM_AUTH_SECRET_KEY=换成你自己的随机密钥
      - HASHEOUS_API_ENABLED=true
      - TZ=Asia/Shanghai
    volumes:
      - romm_resources:/romm/resources
      - romm_redis_data:/redis-data
      - ./library:/romm/library
      - romm_assets:/romm/assets
    ports:
      - "8080:8080"
    depends_on:
      romm-db:
        condition: service_healthy
        restart: true

  romm-db:
    image: mariadb:latest
    restart: unless-stopped
    environment:
      - MARIADB_ROOT_PASSWORD=换成你自己的数据库口令
      - MARIADB_DATABASE=romm
      - MARIADB_USER=romm-user
      - MARIADB_PASSWORD=换成你自己的数据库口令
    volumes:
      - mysql_data:/var/lib/mysql
    healthcheck:
      test: ["CMD", "healthcheck.sh", "--connect", "--innodb_initialized"]
      start_period: 30s
      start_interval: 10s
      interval: 10s
      timeout: 5s
      retries: 5

volumes:
  romm_resources:
  romm_redis_data:
  romm_assets:
  mysql_data:
```

**DB\_PASSWD 和 MARIADB\_PASSWORD**：填同一个数据库口令，两处要一致；

**MARIADB\_ROOT\_PASSWORD**：数据库的管理口令，也是自己定；

**ROMM\_AUTH\_SECRET\_KEY**：换成一长串随机字符，用来签发登录凭据。第一次启动前就填好，之后别再换，换了已登录的会话会失效。

端口 8080 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。勾上「创建项目后立即启动」，两个镜像下完就起来了；数据库要等健康检查通过，应用才会启动。

游戏文件放在编排映射的 ./library 目录里，按 roms/<平台>/ 的层级放，例如 roms/nes/xxx.nes。第一次启动会跑数据库迁移，大约一两分钟，这期间打不开页面是正常的。

### 三、第一次打开：三步向导

用公网地址打开，第一次会走三步向导：

**① 设置库结构**：选择 RomM 要管理的平台，已检测到的平台会被锁定。刚开始库是空的，可以直接下一步。

**② 创建管理员用户**：填用户名、邮箱和密码。这个账号拥有游戏库并管理其他用户，凭据要记好。

**③ 元数据来源**：没有填第三方密钥的话，保留默认的 Hasheous 就行；它按文件哈希匹配，能补上名字、简介和一部分封面。

走完向导进首页。左下角有语言选项，默认会跟随浏览器，简体中文已经内置。

### 四、把游戏放进库、扫描进来

把 ROM 按平台放进 ./library/roms/<平台>/ 目录，平台文件夹名用 RomM 认的短名，例如 nes、gb、snes、gba。放好以后，打开「设置 → 扫描」，扫描类型选「扫描」（快速扫描只查文件、不取元数据），点扫描按钮，右侧会实时显示进度。

扫描完成，同一个平台下的游戏会列出来，显示文件名和识别状态。首页的「最新添加」和封面墙这时就有内容了。以后再加游戏，重复这一步就行。

### 五、封面、详情和浏览器里游玩

首页按平台和最新添加排布，点开任意一个游戏进详情页：封面、平台、发行日期、简介、人数、类型和相似游戏都在这里。

点「开始游玩」进播放器页，再点一次「游玩」，游戏就在浏览器里跑起来了。键盘直接用；手机和平板会显示虚拟手柄。播放器底部有即时存档、读档、暂停、重启、控制设置这些选项，存档写回 NAS，换一台设备也能接着玩。

同一个地址在手机上打开，界面会自动切换成竖版，封面一排排滑过去，想玩哪个点哪个。

### 几点说明

**演示用的游戏。** 这一期用的是可以自由分发的 homebrew 游戏（Driar、Cheril the Goddess、Falling、Lala the Magical、Mega Mountain），都是 NES 平台。RomM 能扫描很多平台，但浏览器里能不能跑取决于 EmulatorJS 支持的核心，别把它当成「所有平台都能玩」。

**封面匹配可能张冠李戴。** Hasheous 按文件哈希和名字匹配，冷门或撞名的游戏可能配上别的封面。在游戏详情的「更多操作 → 编辑」里，可以替换封面、改名字和简介，也可以清掉匹配结果重新匹配。

**没有配置发信邮箱。** 不配 SMTP 不影响扫描和游玩；只有忘记密码时收不到找回邮件。

**明文 HTTP 访问。** 隧道是明文 HTTP，浏览器里的游戏和存档都能用；但别把它当成大门的锁，需要限制访问时在应用里给账号设强密码，或者以后换成 HTTPS 地址。

**更新。** 镜像标签用的是 latest，升级之前把 library 目录和数据库卷备份一份，再到 Docker 里重新拉取镜像、启动项目。

---

原文：<https://www.passnat.com/posts/romm-nas-docker-retro-game-library-browser-play-intranet-penetration>
