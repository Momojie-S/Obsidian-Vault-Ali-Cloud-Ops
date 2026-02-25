# Clash

Clash 代理服务。

## 基本信息

| 配置项 | 值 |
|--------|-----|
| 镜像 | `dreamacro/clash` |
| 容器名 | clash |
| 网络模式 | host（共享主机网络栈） |
| 配置文件 | `./clash-conf/config.yml` |

## 配置

> Clash 配置文件较大，包含代理节点信息，直接查看源文件：`/root/env/docker/clash-conf/config.yml`

## 使用

服务器上通过代理别名使用：
- `proxy_on` - 开启代理（端口 7890）
- `proxy_off` - 关闭代理
- `proxy_status` - 查看状态

详见：[[../服务器初始化#配置代理别名|服务器初始化 - 代理别名]]

## 相关

- [[docker-compose]] - Docker 服务总览
