---
title: "DocuSeal 自建电子签署：NAS Docker 部署 + 签署链接怎么发"
date: 2026-10-18
description: "两容器的开源电子签署工具：Compose 怎么起、数据库健康检查、模板和签名框怎么拖、签署链接怎么生成、对方怎么签、签好的 PDF 在哪里。"
canonical: https://www.passnat.com/posts/docuseal-nas-docker-e-signature-contract
---

## 项目介绍

DocuSeal 是一个开源的电子签署工具：把一份 PDF 合同传上去，在要签字的位置放一个签名框，填上对方的名字，它就会生成一个签署页地址。对方用手机或电脑打开，合同直接显示出来，手指画一个签名、填上日期，点完成，签好的 PDF 就回到你这边，签名笔迹和签字时间一起留档。

适合家里、小团队之间那些不值得打印、扫描、来回快递的文件：租房合同、装修验收单、活动安全承诺书、兼职协议、设备借用单。双方不用见面，也不用装任何软件，有浏览器就能签。

所有文件、签名记录、操作日志都存在你自己的 NAS 上，不经过第三方的签署平台。项目地址：[点我跳转](https://github.com/docusealco/docuseal)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示。DocuSeal 需要一个 PostgreSQL 数据库，一份 Compose 编排把两个服务一起起来。

### 一、想发链接给对方，先把 3000 放出去

只在局域网里签，这一步可以跳过，直接看第二步。要让不在你家网络里的人打开签署页，就在飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」：名字随便起（不超过 12 个字符），本地 IP 留默认的 127.0.0.1，本地端口填 **3000**，点开、按一下电源，状态变成运行中，详情里那个公网地址就是对方在外面要打开的网址。

注意：签署页地址是按你当前访问的域名生成的。所以一定要**先用公网地址打开 DocuSeal，再复制签署页地址**；如果从局域网的地址复制，发出去的地址里就是 192.168 开头的内网地址，对方在外面打不开。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 docuseal，路径在「我的文件」下面挑一个空文件夹，来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  docuseal:
    image: docuseal/docuseal:latest
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment:
      - DATABASE_URL=postgresql://docuseal:docuseal@db:5432/docuseal
    volumes:
      - ./data:/data
    depends_on:
      db:
        condition: service_healthy

  db:
    image: postgres:17-alpine
    restart: unless-stopped
    environment:
      - POSTGRES_USER=docuseal
      - POSTGRES_PASSWORD=docuseal
      - POSTGRES_DB=docuseal
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U docuseal -d docuseal"]
      interval: 5s
      timeout: 5s
      retries: 10
    volumes:
      - ./pgdata:/var/lib/postgresql/data
```

两个服务：**docuseal** 是应用，**db** 是数据库。编排里给数据库加了一段健康检查，应用会等它真正就绪再启动 —— 只写 depends\_on 不够，数据库还没起来时应用会连库失败、容器直接退出。

冒号前面那个 3000 是 NAS 上的端口，被别的项目占了就改它，后面的不用动。下面两个目录：**./data** 存上传的文档和签名，**./pgdata** 是数据库，都要留着。

勾上「创建项目后立即启动」，点确认。第一次启动要等数据库初始化，一两分钟后容器状态变成「运行中」，就可以打开网页了。

### 三、第一次打开：建管理员账号

浏览器打开 **http://NAS的局域网地址:3000**，第一次会跳到安装向导：名字、邮箱、公司名（随便写）、密码，还有一项 **App URL**。App URL 默认取你现在访问的地址，后面用公网地址访问时可以改成公网地址；它影响邮件里的链接，直接用签署页地址的话影响不大。

提交之后会先看到一个邮件推广页，点 Skip 跳过，进模板页。

### 四、做一个合同模板

模板页中间那块虚线框就是上传入口，把合同 PDF 拖进去（或者点一下选文件），它会直接进编辑器。左边是每一页的缩略图，中间是正文，右边是字段面板：Text、Signature、Initials、Date、Number、Checkbox……

从右边把 **Signature** 拖到合同里乙方签字的位置，再把 **Date** 拖到下面的日期处。字段大小可以拉伸，位置对准签字线就行。点右上角的 SAVE 保存模板。

如果你想让对方顺手填姓名、电话，也可以拖一个 Text 字段进去，发出去以后签署页会把它列成必填项。

### 五、发给对方

点 SEND，选 Detailed，填上对方的名字和邮箱（邮箱可以留空），点 ADD RECIPIENTS。这一步只是生成一份待签的提交，不会真的发邮件 —— 没配 SMTP 也能用。

回到模板页，Submissions 那一行里点 **COPY LINK**，这条就是签署页地址。把它用微信、短信、邮件随便哪种方式发给对方就行。

对方打开以后：整份合同直接显示，点一下签名框，用手指（或鼠标）画一个签名，点 NEXT，日期处可以点 Set Today，最后点 COMPLETE。签完会显示「Document has been signed」。

你这边刷新提交页，右边会显示对方的签名笔迹和签字时间；右上角 DOWNLOAD 可以下载签好字的 PDF，AUDIT LOG 里记着谁在什么时候打开、签署了这份文件。

### 六、几点说明

**数据库不要单独删。** 合同、签名、账号都在 PostgreSQL 里，./pgdata 删掉等于全部清空；备份就备份 ./data 和 ./pgdata 两个目录。

**第一次启动慢一点是正常的。** 数据库要初始化，应用要等健康检查通过。容器反复重启，先看数据库有没有起来（docker compose logs db）。

**没有中文界面。** 目前安装向导和后台是英文，签署页的合同内容是你自己的 PDF，中文照常显示。

**签署页是对外开放的。** 拿到地址的人就能看到合同内容，所以链接要单独发给对方，别贴在公开群里；发错了可以在提交页把这条提交归档，重新发一份。

**更新。** 拉新镜像重建容器即可，两个数据目录都不动。升级前先做一次备份。

---

原文：<https://www.passnat.com/posts/docuseal-nas-docker-e-signature-contract>
