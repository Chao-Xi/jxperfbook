## 1 数据库慢查询激增怎么办

### 1、问题定位：从告警到根因的精准狙击

**1. 快速止血：建立应急响应机制**


通过监控平台（如Prometheus + Grafana）捕获数据库QPS突增、CPU使用率超阈值（>80%）、慢查询数量激增（如MySQL `Slow_queries`每分钟超过100次）

```
-- 实时监控慢查询数量
SHOW GLOBAL STATUS LIKE 'Slow_queries';
```

**紧急限流**

立即限制高危操作的并发量，防止雪崩效应：

```
-- 动态限制最大连接数（临时降低至200）
SET GLOBAL max_connections = 200;

-- 使用pt-kill终止耗时超过10秒的查询
pt-kill --busy-time 10 --kill --victims all --print h=127.0.0.1
```

### 2. 根因分析：工具链组合拳

慢日志分析

**提取Top 10慢查询，定位问题SQL**：

```
# 按总耗时排序慢查询
mysqldumpslow -s t -t 10 /var/log/mysql/slow.log
```

```
Count: 200  Time=5.12s (1024s)  Lock=0.00s (0s)  Rows=100.0 (20000), user@host
  SELECT * FROM orders WHERE status='pending' AND create_time > '2023-01-01';
```

**执行计划解读**

使用EXPLAIN分析索引有效性

```
EXPLAIN SELECT * FROM orders WHERE status='pending';
```

关键指标：

* **type: ALL** → 全表扫描，需添加索引
* **Extra: Using filesort** → 排序逻辑需优化

**资源瓶颈定位**

排查服务器资源是否过载：

```
top -c           # 查看CPU占用最高的进程
iostat -x 1     # 监控磁盘I/O（%util > 90%表示瓶颈）
dstat --tcp      # 检查网络连接数激增
```

## 2、问题解决：精准优化与架构升级

### 1. SQL与索引优化

```
ALTER TABLE orders ADD INDEX idx_status_create_time (status, create_time);
```

**索引失效案例**

*  **隐式类型转换**：`WHERE user_id = '123'`（**user_id为INT） → 移除引号**
*  **索引列运算**：`WHERE YEAR(create_time) = 2023` → 改写为范围查询

**SQL重写技巧**

**优化复杂子查询为JOIN操作：**

```
-- 原语句（耗时5s）
SELECT * FROM orders WHERE status IN (SELECT status FROM config WHERE type='order');

-- 优化为JOIN（耗时0.2s）
SELECT o.* FROM orders o 
JOIN config c ON o.status = c.status 
WHERE c.type='order'
```

### **2. 数据库参数调优**

**InnoDB引擎优化**

```
# my.cnf调整示例
innodb_buffer_pool_size = 80G       # 物理内存的70%~80%
innodb_flush_log_at_trx_commit = 2  # 平衡性能与持久化
```

**连接池配置**

```
# 应用端配置（HikariCP）
maximumPoolSize: 100
connectionTimeout: 3000
```

### **3. 架构级解决方案**

**读写分离**

```
App → ProxySQL → MySQL Master（写）
                  ↘ MySQL Replica1（读）
                  ↘ MySQL Replica2（读）
```

**分库分表**

* 垂直拆分：按业务模块拆分（订单库、用户库）
* 水平拆分：按时间或ID范围分片（`orders_2023`、`orders_2024`）

### **3、团队协作：从故障到预防的闭环**

**1. 故障复盘模板**

阶段  关键动作  输出物

* 应急 | 	限流、回滚、扩容 | 故障时间线记录
* 根因 | 	 SQL分析、资源监控、代码Review |  根因分析报告
* 改进 | 	 索引优化、参数调整、架构升级 | 技术方案PRD
* 预防 | 慢查询日报、压测流程、巡检自动化 | 巡检脚本+监控看板

**2. 长效预防机制**

```
-- 生成每日慢查询TOP 10
pt-query-digest /var/log/mysql/slow.log --filter '$event->{arg} =~ m/WHERE/' --limit 10
```

**自动化巡检**

```
# 伪代码：检查缺失索引
for table in get_all_tables():
    if not has_index(table, 'status'):
        send_alert(f"表 {table} 缺少status字段索引")
```


