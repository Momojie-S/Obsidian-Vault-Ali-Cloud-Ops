# Clash / Mihomo

Clash 代理服务（使用 Mihomo 内核，支持 TUN 模式）。

## 基本信息

| 配置项 | 值 |
|--------|-----|
| 镜像 | `metacubex/mihomo:latest` |
| 容器名 | clash |
| 网络模式 | host（共享主机网络栈） |
| 配置文件 | `./clash-conf/config.yml` |
| TUN 网卡 | `Meta` (28.0.0.1/30) |

## 配置更新

配置从订阅链接获取，使用脚本自动更新：

```bash
cd /home/momojie/env/docker/clash-conf

# 更新配置并启用 TUN 模式
./update-clash-config.sh --tun

# 仅更新配置（不启用 TUN）
./update-clash-config.sh

# 重启 Clash
cd /home/momojie/env/docker && docker compose restart clash
```

## 配置验证

使用 `mihomo -t` 验证配置格式。

## TUN 模式

TUN 模式实现系统级透明代理，所有程序的流量自动通过 Clash 分流。

### 工作原理

```
应用程序 → TUN 网卡(Meta) → Clash 规则匹配
                                    ↓
                    ┌───────────────┴───────────────┐
                    中国 IP/域名                        其他 IP
                    → DIRECT (直连)              → 代理节点
```

### 验证

```bash
# 检查 TUN 网卡
ip addr show Meta

# 查看分流日志
docker logs clash 2>&1 | grep "match"

# 测试国内直连
curl -s http://ip-api.com/json

# 测试国外代理
curl -s https://www.google.com -o /dev/null -w "%{http_code}"
```

## 自定义规则

`update-clash-config.sh` 会在配置中注入自定义规则：

### 直连规则

| 目标 | 说明 |
|------|------|
| `*.mojcn.com`, `*.cnmjin.net`, `*.cnmjcn.cyou` | 代理节点服务器（避免循环代理） |
| `*.bigmodel.cn`, `*.zhipuai.cn` | GLM 大模型 |
| `223.5.5.5`, `119.29.29.29`, `*.doh.pub` | DoH DNS |
| `*.qyapi.weixin.qq.com` | 企业微信 |

### Gemini 固定新加坡

Gemini API 有地区限制（不支持中国/香港），需固定走新加坡节点：

```
- DOMAIN-SUFFIX,generativelanguage.googleapis.com,新加坡-优化-Gemini-GPT
- DOMAIN-SUFFIX,ai.google.dev,新加坡-优化-Gemini-GPT
- DOMAIN-SUFFIX,gemini.google.com,新加坡-优化-Gemini-GPT
- DOMAIN-KEYWORD,gemini,新加坡-优化-Gemini-GPT
```

**原理：** Gemini 域名不在 fake-ip-filter 中，使用 fake-ip 让域名规则生效。

### fake-ip-filter 说明

以下域名返回真实 IP（用于 web_fetch 兼容）：
- `+.google.com`
- `+.googleusercontent.com`
- `+.gstatic.com`
- `+.youtube.com`, `+.ytimg.com`, `+.ggpht.com`
- `ai.google.dev`

**注意：** `+.googleapis.com` 已移除，让 Gemini API 走 fake-ip 模式。

## 监控与恢复

### watchdog.sh（自动监控）

每 2 分钟自动检测，失败时自动恢复：

```bash
cd /home/momojie/env/docker/clash-conf

# 查看状态
./watchdog.sh --status

# 手动触发检测
./watchdog.sh
```

**自动恢复流程：**
1. 容器异常 → 启动容器
2. TUN 接口丢失 → 重启 Clash
3. 网络不通 → 重启 Clash → 仍不通则禁用 TUN

**Cron 配置：** `/etc/cron.d/clash-watchdog`

**日志：** `./watchdog.log`

### reset-tun.sh（手动重置）

快速操作脚本：

```bash
cd /home/momojie/env/docker/clash-conf

# 重置 TUN（重启 Clash）
./reset-tun.sh

# 查看状态
./reset-tun.sh --status

# 停止 Clash
./reset-tun.sh --off

# 禁用 TUN 模式（切换回普通模式）
./reset-tun.sh --disable-tun
```

## HTTP 代理模式

TUN 模式下，HTTP 代理（7890 端口）仍然可用：

- `proxy_on` - 设置环境变量使用代理
- `proxy_off` - 清除代理环境变量
- `proxy_status` - 查看代理状态

详见：[[../服务器初始化#配置代理别名|服务器初始化 - 代理别名]]

## 相关

- [[docker-compose]] - Docker 服务总览
- [[../网络配置]] - 透明代理历史、DoH 配置
