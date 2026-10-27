# 云微 WechatOnCloud：把微信挂在 NAS 上，浏览器随时随地收发消息

> 在自己的 NAS 上跑一个**官方原版**电脑微信，浏览器打开就能扫码登录、收发消息、传文件。手机存储被聊天记录塞满、公司电脑不让装微信、换设备想接着聊，这几种场景都能用。

## 这个项目解决什么问题

云微（WechatOnCloud）把官方 Linux 版微信装进容器，再把画面串到网页上——**微信客户端一行代码都没改**，所以不存在改版客户端带来的封号风险类型（但仍需注意下文「先用小号试」）。

结构是「**一个面板 + 多个实例**」：

- **面板**负责登录、管理、分配
- 每新建一个**实例**就是一个独立容器，应用类型可选：微信 / QQ / Chromium 浏览器
- Chromium 实例适合常驻邮箱、各类管理后台这类「要一直保持登录」的网页
- 同一个实例换电脑、换手机打开，看到的是**同一个会话**
- 可以给家人建子账号，只让他看到分给他的实例

官方仓库：<https://github.com/Gloridust/WechatOnCloud>

## 前置要求

| 项 | 要求 |
|---|---|
| 硬件 | 能跑 Docker 的 NAS（本文在飞牛 fnOS 实测） |
| 内存 | 面板很轻；**每个已登录的微信实例空闲约 0.6 GB，操作时 1–1.5 GB**。8 GB 内存挂 2–3 个实例比较从容 |
| 磁盘 | 实例镜像约 3 GB，微信安装包约 200 MB，另加聊天记录 |
| 外网访问 | 可选。需要在外网打开时用内网穿透，**建议先建隧道再部署**（编排里要填隧道地址） |

## 快速开始

### 第 1 步：先建内网穿透隧道

飞牛「应用中心」装 PassNAT 插件 → 打开 → 「创建隧道」：

- 隧道名称：随意，**不超过 12 个字符**
- 本地 IP：默认 `127.0.0.1`
- 本地端口：`36080`（与下面 compose 里冒号左边的端口一致）
- 传输协议：默认 TCP

建好后点开隧道 → 按电源 → 状态变「运行中」，详情里形如 `IP:端口` 的那串就是公网地址，记下来。

### 第 2 步：新建 Compose 项目

飞牛「Docker」→「Compose」→ 新增项目，项目名 `yunwei`，来源选「创建 docker-compose.yml」，粘贴：

```yaml
services:
  woc:
    image: gloridust/woc-panel:latest
    container_name: woc-panel
    environment:
      - WOC_WECHAT_IMAGE=gloridust/wechat-on-cloud:latest
      - PANEL_ADMIN_USER=admin
      - PANEL_ADMIN_PASSWORD=<改成你自己的强密码>
      - PANEL_ALLOWED_HOSTS=<隧道给的地址>
      - PANEL_DATA=/data/accounts.json
      - TZ=Asia/Shanghai
    volumes:
      - ./data:/data
      - /var/run/docker.sock:/var/run/docker.sock
    ports:
      - 36080:8080
    restart: unless-stopped
```

### 第 3 步：改两个必填项

| 变量 | 填什么 | 不改会怎样 |
|---|---|---|
| `PANEL_ADMIN_PASSWORD` | 一个够长的新密码 | 默认 `admin / wechat`，等于没锁门。**面板背后登着你的微信，谁能打开谁就能以你的身份发消息** |
| `PANEL_ALLOWED_HOSTS` | 隧道给的地址：TCP 隧道只写 IP 不带端口；HTTP/HTTPS 隧道写域名，都不带 `http://` 和路径 | 面板只认局域网地址，外网打开直接报 `400 Host header not allowed` |

- 密码只在**第一次启动**时写进账号文件，之后要改在面板里点「修改密码」
- 面板需要挂载 `/var/run/docker.sock`，因为每新建一个实例它都要自己起容器
- 端口 `36080` 被占用就换一个，隧道里的本地端口同步改

### 第 4 步：登录面板、建实例

1. 浏览器打开 `http://NAS地址:36080`，用 `admin` + 你设的密码登录（先局域网验证）
2. 左下角「管理」→「新建实例」→ 应用类型选「微信」→ 起个名字（如「工作号」）→ 创建
3. 首次创建要拉约 3 GB 的实例镜像
4. 实例起来后「管理」→「下载安装」，面板会从腾讯官方下载 Linux 版微信（约 200 MB）
5. 「进入实例」→ 手机微信扫码登录 → 网页里收发消息、拖文件进去发送

想挂 QQ 或常驻浏览器：新建实例时应用类型选「QQ」或「Chromium」，Chromium 不用下载，建完就能用。

### 第 5 步：在外网打开

用隧道地址访问（HTTP/HTTPS 隧道会先要 PassNAT 的网页访问用户名密码），再输面板账号密码。手机 Safari/Chrome 打开后「添加到主屏幕」，用起来像独立 App。

## 常见问题

**Q：会不会被微信判定为新设备、强制下线？**
有可能。云微用的是官方原版客户端，但它跑在服务器环境里，微信可能识别为新设备。**第一次用建议先拿不常用的号试几天**，稳定了再挂主号。

**Q：聊天记录存在哪？重启会丢吗？**
每个实例的数据在自己的数据卷里，聊天记录和收到的文件都存在 NAS 上，重启、升级实例都不会丢。删除实例时默认保留数据卷，可另选彻底清除。

**Q：卡在「创建容器失败……timeout」？**
先在 Docker「镜像」里手动拉一次 `gloridust/wechat-on-cloud:latest`，再回面板点「创建并启动」。

**Q：外网打开报 `400 Host header not allowed`？**
`PANEL_ALLOWED_HOSTS` 没填或填错。TCP 隧道只写 IP（不带端口），HTTP/HTTPS 隧道写域名（不带协议和路径）。

**Q：怎么升级？**
面板里有「一键升级全部实例」，拉新镜像重建、保留聊天记录；面板自身升级就在 Docker 里重新拉镜像、重启项目。

**Q：中文输入法怎么用？**
用你本地的输入法直接打，画面里输入即可。

## 安全与暴露面

这个面板等于**你微信的控制台**，安全要求比一般自托管服务高：

- `PANEL_ADMIN_PASSWORD` 必须改掉默认值，用够长的强密码
- 传输协议选 HTTP 时，务必在隧道表单里填「网页访问用户名」和「网页访问密码」（最多 12 个字符，超了会创建失败），打开网页先过 PassNAT 这一道门
- 有条件选 HTTPS 隧道：需要填 Crt 证书路径和 Key 密钥路径（NAS 上的绝对路径，证书需与域名对应），见 [PassNAT HTTPS 文档](https://doc.passnat.com/docs/app/https)
- 不用的时候把隧道关掉

## 原文

完整部署过程（含截图和逐步说明）：<https://www.passnat.com/posts/wechatoncloud-nas-docker-wechat-in-browser-intranet-penetration>
