# IOPS 监控脚本

磁盘 IOPS 实时监控。

## 位置

`/root/env/iops_monitor.sh`

## 功能

- 实时监控磁盘 IOPS
- 当 IOPS 超过阈值（默认 1000）时自动记录系统状态
- 记录高 IO 进程、系统负载、内存使用等信息

## 配置参数

| 参数 | 默认值 | 说明 |
|------|--------|------|
| SAMPLE_INTERVAL | 2s | 采样间隔 |
| SPIKE_THRESHOLD | 1000 | IOPS 飙升阈值 |
| SPIKE_COUNT | 3 | 连续触发次数 |
| CALM_WINDOW | 30s | 触发后冷静期 |

## 日志位置

- 主日志：`/root/env/log/iops_monitor/iops_spike.log`
- 详细日志：`/root/env/log/iops_monitor/spike_*.log`

## 运行

```bash
# 前台运行
/root/env/iops_monitor.sh

# 后台运行
nohup /root/env/iops_monitor.sh > /dev/null 2>&1 &
```
