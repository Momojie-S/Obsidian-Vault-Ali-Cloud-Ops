# FRPS

FRP 内网穿透服务端。

## 基本信息

| 配置项 | 值 |
|--------|-----|
| 镜像 | `stilleshan/frps:latest` |
| 容器名 | frps |
| 服务端口 | 22001 |
| SSH 映射端口 | 22002 |
| 配置文件 | `./frps/frps.ini` |

## 配置文件 (frps.ini)

```ini
[common]
# frp 服务端口
bind_port = 22001

# 认证令牌
authentication_method = token
token = LoveYebao0924.

# 心跳超时（秒）- 应大于客户端的 heartbeat_timeout（90秒）
heartbeat_timeout = 120

# TCP 多路复用 - 开启可减少连接数，提高性能
tcp_mux = true

# 每个代理的最大连接池大小
max_pool_count = 10

# 客户端连接超时（秒）
user_conn_timeout = 10

# 日志配置
log_file = /dev/stdout
log_level = info
log_max_days = 3
```

## 相关

- [[docker-compose]] - Docker 服务总览
