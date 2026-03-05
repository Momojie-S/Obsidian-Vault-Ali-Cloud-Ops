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

## 多账号配置

OpenClaw 支持多账号（Multi-account）矩阵隔离，每个账号可以绑定到不同的 agent。

**配置结构：**
- `channels.wecom.accounts.<accountId>` - 定义企业微信账号
- `agents.list` - 定义 OpenClaw agent
- `bindings` - 将账号绑定到 agent

**当前配置的账号：**
- `echo` → `main` agent（默认）
- `alpha` → `alpha` agent（开发专用）

**企业微信后台配置：**
1. 为每个账号创建独立的 Bot 和 Agent
2. 配置独立的回调 URL（见下方 API 端点）
3. **每个 Agent 都需要配置企业可信 IP**

## Nginx 反向代理

**配置文件**: `/etc/nginx/sites-available/openclaw-wecom`

**推荐路径（OpenClaw 3.1+）：**
- `/openclaw/plugins/wecom/agent/<accountId>`
- `/openclaw/plugins/wecom/bot/<accountId>`

**旧路径（兼容）：**
- `/openclaw/wecom/agent/<accountId>`
- `/openclaw/wecom/bot/<accountId>`

## API 端点

**Echo Account（默认）：**
| 模式 | URL |
|------|-----|
| Agent | `https://api.momojie.online/openclaw/plugins/wecom/agent/echo` |
| Bot | `https://api.momojie.online/openclaw/plugins/wecom/bot/echo` |

**Alpha Account（开发）：**
| 模式 | URL |
|------|-----|
| Agent | `https://api.momojie.online/openclaw/plugins/wecom/agent/alpha` |
| Bot | `https://api.momojie.online/openclaw/plugins/wecom/bot/alpha` |

**注意：** 企业微信回调 URL 需要精确匹配账号路径

## 相关

- [[部署配置]] - OpenClaw 安装和迁移
- [[../Docker/docker-compose]] - Docker 服务（Clash 代理）
