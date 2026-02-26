# OpenClaw 代理配置调研

## 调研时间
2026-02-26

## 背景

需要让 OpenClaw 的 `web_fetch` 工具能够访问外网（如 GitHub 文档）。

## 方案调研

### 方案 1：iptables 透明代理（已放弃）

详见 `网络配置.md#透明代理`

放弃原因：全量劫持风险大，Clash 节点不稳定会影响整个服务器网络。

### 方案 2：环境变量代理

在 `~/.openclaw/.env` 配置代理环境变量：

```bash
http_proxy=http://127.0.0.1:7890
https_proxy=http://127.0.0.1:7890
HTTP_PROXY=http://127.0.0.1:7890
HTTPS_PROXY=http://127.0.0.1:7890
```

OpenClaw 会自动加载此文件（源码：`src/infra/dotenv.ts`）。

## 源码分析

### web_fetch 代理支持

查看 OpenClaw 源码（`src/agents/tools/web-fetch.ts`、`web-guarded-fetch.ts`、`fetch-guard.ts`）：

1. `web_fetch` 调用 `fetchWithWebToolsNetworkGuard`
2. 传入参数 `proxy: "env"`
3. 底层使用 undici 的 `EnvHttpProxyAgent`

```typescript
// src/infra/net/fetch-guard.ts
if (params.proxy === "env" && hasEnvProxyConfigured()) {
  dispatcher = new EnvHttpProxyAgent();
}
```

支持的代理环境变量：
- `HTTP_PROXY` / `http_proxy`
- `HTTPS_PROXY` / `https_proxy`
- `ALL_PROXY` / `all_proxy`

### SSRF 保护

web_fetch 有 SSRF 保护，会检查目标 IP 是否为私有地址。

但已配置 `dangerouslyAllowPrivateNetwork: true`，允许私有网络。

## 测试结果

| 测试项 | 结果 |
|--------|------|
| 环境变量加载 | ✅ Gateway 进程有 `HTTP_PROXY` 等变量 |
| curl + 代理访问外网 | ✅ 正常，返回代理 IP |
| web_fetch 访问 httpbin.org/ip | ⚠️ 返回真实 IP（代理未生效） |
| web_fetch 访问 api.ipify.org | ❌ fetch failed |

**结论：环境变量已加载，但 `EnvHttpProxyAgent` 未生效**

## 问题分析

可能原因：
1. Node.js 内置 fetch 对 undici dispatcher 支持不完整
2. `EnvHttpProxyAgent` 实现存在 bug
3. undici 版本问题（OpenClaw 使用 `undici: ^7.22.0`）

## 临时解决方案

使用 `exec + curl` 代替 `web_fetch`：

```bash
curl -x http://127.0.0.1:7890 -s "https://example.com"
```

## 后续行动

1. 向 OpenClaw 提 Issue 报告 `EnvHttpProxyAgent` 不工作
2. 考虑使用 Firecrawl 作为 web_fetch 后端（支持代理）
3. 或开发一个简单的 wrapper skill，用 exec + curl 模拟 web_fetch

## 相关文件

- OpenClaw 源码：`/tmp/openclaw-src/src/agents/tools/web-fetch.ts`
- 代理环境变量：`~/.openclaw/.env`
- Clash 配置：`/home/momojie/env/docker/clash-conf/config.yml`
