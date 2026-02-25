# MySQL

Docker 容器化 MySQL 8.0 服务。

## 基本信息

| 配置项 | 值 |
|--------|-----|
| 镜像 | `mysql:8.0` |
| 容器名 | mysql-dev |
| 端口 | 21001（外部）→ 3306（内部） |
| 数据目录 | `/mnt/mysql-data` |
| 配置目录 | `./mysql-conf/` |
| 默认数据库 | devdb |

## 配置文件 (my.cnf)

位置：`./mysql-conf/my.cnf`

```ini
[mysqld]
# 设置字符集
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci

# 设置时区
default-time-zone = '+8:00'

# 性能相关配置
innodb_buffer_pool_size = 256M
innodb_log_file_size = 64M
max_connections = 200

# 安全设置
sql_mode = STRICT_TRANS_TABLES,NO_ZERO_DATE,NO_ZERO_IN_DATE,ERROR_FOR_DIVISION_BY_ZERO
```

## 连接

```bash
# 从宿主机连接
mysql -h 127.0.0.1 -P 21001 -u root -p

# 从容器内连接
docker exec -it mysql-dev mysql -u root -p
```

## 相关

- [[docker-compose]] - Docker 服务总览
