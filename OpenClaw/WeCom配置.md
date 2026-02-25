# WeCom 配置

企业微信（WeCom）通道配置，包括 Agent 模式和 Bot 模式。

## 安装 WeCom Channel

OpenClaw 需要安装企业微信通道插件才能对接企业微信。

**安装步骤**: https://github.com/YanHaidao/wecom

## Agent 模式 - IP 白名单

> ⚠️ 重要：企业微信 Agent 模式需要配置**可信 IP**，否则消息发送会失败（错误码 60020）。

**配置步骤：**
1. 登录企业微信管理后台
2. 应用管理 → 选择对应的应用
3. 找到「企业可信IP」设置
4. 添加服务器公网 IP

**注意：** 
- IP 白名单不支持域名，只能填写 IP 地址
- 如果服务器 IP 变更，需要同步更新白名单

## Nginx 反向代理

**配置文件**: `/etc/nginx/sites-available/openclaw-wecom`

```nginx
server {
    server_name api.momojie.online;

    location /openclaw/wecom/agent {
        proxy_pass http://127.0.0.1:18789/wecom/agent;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;
        client_max_body_size 10m;
    }

    location /openclaw/wecom/bot {
        proxy_pass http://127.0.0.1:18789/wecom/bot;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;
        client_max_body_size 10m;
    }

    listen 443 ssl;
}
```

## API 端点

| 用途 | URL |
|------|-----|
| Agent | `https://api.momojie.online/openclaw/wecom/agent` |
| Bot | `https://api.momojie.online/openclaw/wecom/bot` |

## 相关

- [[部署配置]] - OpenClaw 安装和迁移
- [[../Docker/docker-compose]] - Docker 服务（Clash 代理）
