# Docker Compose 服务

位置：`/root/env/docker/`

## docker-compose.yml

```yaml
services:
  mysql:
    image: mysql:8.0
    container_name: mysql-dev
    environment:
      MYSQL_ROOT_PASSWORD: LoveYebao0924.
      MYSQL_DATABASE: devdb
      MYSQL_USER: devuser
      MYSQL_PASSWORD: LoveYebao0924.
    ports:
      - "21001:3306"
    volumes:
      # 使用额外的磁盘空间来存储 MySQL 数据
      - /mnt/mysql-data:/var/lib/mysql
      # 可选：挂载配置文件
      - ./mysql-conf:/etc/mysql/conf.d:ro
    restart: unless-stopped
    networks:
      - dev-network

  frps:
    image: stilleshan/frps:latest
    container_name: frps
    restart: unless-stopped
    ports:
      - "22001:22001"  # frp 服务端口
      - "22002:22002"  # SSH 映射端口（可按需添加更多端口）
    volumes:
      - ./frps/frps.ini:/frp/frps.ini:ro
    networks:
      - dev-network

  clash:
    image: dreamacro/clash
    container_name: clash
    network_mode: "host"  # 共享主机网络栈
    restart: unless-stopped
    volumes:
      - ./clash-conf/config.yml:/root/.config/clash/config.yaml:ro

networks:
  dev-network:
    driver: bridge
```

## 服务列表

| 服务 | 容器名 | 端口 | 用途 | 详情 |
|------|--------|------|------|------|
| MySQL | mysql-dev | 21001:3306 | 开发数据库 | [[MySQL]] |
| FRPS | frps | 22001, 22002 | 内网穿透服务端 | [[FRPS]] |
| Clash | clash | host 模式 | 代理服务 | [[Clash]] |

## 常用命令

```bash
# 进入目录
cd /root/env/docker

# 启动所有服务
docker compose up -d

# 查看服务状态
docker compose ps

# 查看日志
docker compose logs -f mysql

# 重启服务
docker compose restart <服务名>
```
