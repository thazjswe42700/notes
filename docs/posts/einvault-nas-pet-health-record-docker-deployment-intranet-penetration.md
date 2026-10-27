---
title: "EinVault 宠物健康档案：飞牛 NAS Docker 部署 + 内网穿透外网访问"
date: 2026-10-04
description: "给毛孩子建一份电子档案：疫苗、驱虫、体重、看病记录都记在里面，到期设提醒，家里人一起记。本文在飞牛 NAS 上用 Docker Compose 部署 EinVault，用帕斯内网穿透开放到公网，含 ORIGIN、首启建管理员、体重单位这几处必看项。"
canonical: https://www.passnat.com/posts/einvault-nas-pet-health-record-docker-deployment-intranet-penetration
---

## 项目介绍

EinVault 是一个自己部署的宠物档案网站：给每只宠物建一份档案，疫苗、驱虫、看病、体重都记在里面，还能写日记、记遛狗喂饭这类日常，到期要做的事设个提醒，首页会列出来。

它最顺手的地方是**「到哪都能翻」**：带宠物去医院，医生问上一针疫苗是哪天打的、最近体重多少，掏出手机打开自己的网址就能查，不用再翻聊天记录和防疫本。数据全存在你自己的 NAS 上。

家里几个人可以一起记：管理员管账号，成员记健康，看护人只记遛狗喂饭这类日常，三种角色。

项目地址：[点我跳转](https://github.com/davefatkin/EinVault)

## 部署安装

下面在飞牛 NAS 上用 Docker Compose 装 EinVault，再用 [帕斯内网穿透](https://passnat.com) 把它开放到公网。

⚠ **顺序跟一般的项目反过来：先建隧道，再部署。** EinVault 要求编排里的 ORIGIN 必须跟你在浏览器地址栏里实际输入的地址一模一样。填错了页面照样能打开，但**所有提交表单的动作（包括第一次建管理员）都会被拒绝**，页面上只有一句 Cross-site POST form submissions are forbidden。所以要先拿到公网地址，编排里才填得对。

### 一、先建隧道，拿到公网地址（先别通电）

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起，接入节点和传输协议留默认，本地 IP 留它默认的 127.0.0.1，本地端口填 3446（下面编排里对外的那个端口）。

建好之后，列表里这一行就已经分配好了公网地址（形如 IP:端口），把它记下来。**先别按电源** —— 容器还没装，现在通电没有意义。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 einvault，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去。

```yaml
services:
  einvault:
    image: ghcr.io/davefatkin/einvault:latest
    container_name: einvault
    restart: unless-stopped
    network_mode: bridge
    ports:
      - "3446:3000"
    volumes:
      - ./data:/data
    user: "1000:1000"
    environment:
      ORIGIN: http://换成隧道的公网地址
      TZ: Asia/Shanghai
```

要改的只有两处，都在最后面：

① **ORIGIN 换成第一步记下来的那个公网地址**，写成 http://IP:端口 的样子，末尾不要带斜杠。

② TZ 是时区，国内填 Asia/Shanghai，不填的话时间按格林尼治算，跟你这边差八个小时。

冒号左边的 3446 是 NAS 上对外的端口，建隧道填的就是它；冒号右边的 3000 是容器里自己监听的端口，别改。编排里没有任何口令 —— 管理员账号是第一次打开网页时你自己建的。

数据都在项目文件夹里的 ./data：einvault.db 是数据库，上传的照片和文档也在里面。备份就把这个文件夹整个拷走。

勾上「创建项目后立即启动」，点确认。镜像下完就能起来，十几秒之后端口就通了。

### 三、通电，马上建管理员

回到 PassNAT 插件，点开这条隧道按一下电源，状态转成运行中，公网地址就通了。

🔴 **紧接着用公网地址打开它。** 第一次打开会自动跳到建管理员的页面（昵称、用户名、密码、确认密码），这个页面只在第一次出现，而且**谁先打开谁就是管理员** —— 隧道一开，公网上任何人都能访问到这一页。所以通电之后第一件事就是把管理员建好，别拖。

建好管理员之后会直接进到首页，不用再登录一次。界面是英文的，按钮不多：左边栏是 Dashboard、Journal、Health、Reminders、Documents，对着画面点就行。

### 四、添加你的宠物

进到首页，点「Add your first companion」。先选种类（狗、猫或其他），填名字，品种、性别、生日有就填。⚠ **Weight unit 一栏出厂是 lbs（磅），国内用的话选 kg**，选了之后后面记体重的表单也会默认 kg。

### 五、记疫苗、记体重、设提醒

**记疫苗和看病：** 左边栏点 Health，右上角「Add Event」。Title 写打的什么，Type 选 Vaccination（也可以是 Vet visit 看病、Medication 用药、Procedure 手术），Date 填日期，医生和诊所有就填。保存后这一条就进了下面的 Health Events 列表。

**记体重：** 同一页右上角「Log Weight」，填体重和日期。每记一次，上面的体重曲线就多一个点，涨了还是掉了一眼看出来。

**设提醒：** 左边栏点 Reminders，「Add Reminder」。Title 写要做的事，Type 选 Vaccination / Medication 这类，Due date 填哪天要做，还能勾 Recurring reminder 设成重复提醒。快到期的会列在首页的 Upcoming Reminders 里，行尾有个对勾，做完点一下。

### 六、让家里人一起记

点左下角你自己的头像，弹出的菜单里选 Users，再点「New user」。填昵称、用户名和一个初始密码，Role 选一个：

**Member** 可以记健康、设提醒；**Caretaker** 只能记遛狗、喂饭、便便这类日常，适合给老人或者帮忙照看的人；**Admin** 能管账号。建好把网址和账号发给对方就行。

### 几点说明

**全程用公网地址访问。** ORIGIN 填的是公网地址，所以用局域网 IP 打开时页面能看，但保存、登录这类提交会被拒绝，这不是坏了，换回公网地址就好。

**更新。** 镜像用的是官方写法的 latest，以后想升级，在 Docker 里把这个项目重新拉取镜像再启动就行，数据在 ./data 里不会丢。

---

原文：<https://www.passnat.com/posts/einvault-nas-pet-health-record-docker-deployment-intranet-penetration>


## 相关文章

- [Wishlist 全家礼物想单存 NAS](wishlist-family-gift-list-nas-deployment-passnat.md)
- [Lychee 自建私人相册：飞牛 NAS Docker 部署 + 内网穿透外网访问](lychee-nas-self-hosted-photo-album-share-link-password.md)
- [哪吒监控自建服务器探针：飞牛 NAS Docker 部署 + 内网穿透外网访问](nezha-server-monitoring-nas-docker-deployment-intranet-penetration.md)
- [自建书签管理器Linkding极简部署指南，NAS内网穿透远程访问书签收藏库](linkding-bookmark-manager-nas-remote-access-guide.md)
- [Wallabag 稍后读：飞牛 NAS Docker 部署 + 内网穿透外网访问](wallabag-docker-deploy-nas-internal-penetration-remote-access.md)
