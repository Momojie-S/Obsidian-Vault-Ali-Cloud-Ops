# MOC - 运维笔记索引

> Map of Content - 阿里云服务器运维笔记导航

## 服务器配置

- [[服务器初始化]] - 用户创建、SSH、开发环境（Homebrew、Oh My Zsh、NVM、Vim、uv、pnpm）
- [[目录结构]] - `/root/env/` 目录组织
- [[端口分配]] - 端口占用总览，新服务部署前先查阅

## Docker 服务

- [[Docker/docker-compose]] - 容器服务总览
  - [[Docker/MySQL]] - MySQL 8.0 配置
  - [[Docker/FRPS]] - FRP 内网穿透服务端
  - [[Docker/Clash]] - Clash 代理服务

## OpenClaw

- [[OpenClaw/部署配置]] - 安装、必要配置、迁移步骤
- [[OpenClaw/WeCom配置]] - 企业微信 Agent/Bot 配置、IP 白名单、Nginx 反向代理

## 运维脚本

- [[运维脚本/IOPS监控]] - 磁盘 IOPS 监控脚本

---

## 快速链接

| 场景 | 笔记 |
|------|------|
| 服务器重置后第一步 | [[服务器初始化]] |
| 部署新服务前查端口 | [[端口分配]] |
| OpenClaw 迁移 | [[OpenClaw/部署配置#迁移步骤]] |
| 企业微信收不到消息 | [[OpenClaw/WeCom配置#IP 白名单]] |
| Docker 服务管理 | [[Docker/docker-compose#常用命令]] |
