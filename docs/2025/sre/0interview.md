# 1 SRE 面试

### 1 Linux 如何查看和杀死进程

```
ps -aux 
ps -ef

# pid 父进程pid, 进程启动时间， 谁启动这个进程

top  #实时信息

kill / kill -9
```

### 2 硬链接and软连接

* **硬链接 是同一个文件不同的访问入口 所有的链接都共享一个inode 和同一个数据块**


* 软连接更像一个文件的快捷访问，存储源文件的绝对路径，一旦源文件删掉，软连接就失效



### 3 常用的Linux 文件系统类型

主要有两个

- 1 EXT4 最大支持1EB的文件系统，16TB的文件大小， **特点是延迟分配，有数据写入才会分配一块空间**
- 2 XFS **高性能文件系统，能够处理非常高并发的镀锡操作**


### 4 Linux中配置程序开机自启动

**1 rc.local 文件里面加入 脚本启动的命令** 开机过程中，系统会默认加载这个系统

**2 把脚本做出系统服务， 创建一个service 文件，把启动脚本的一些命令写进去**

Systeml enable 来设置它的开机自启动

### 5 CPU 突然飙升

1. Top 所有进程的详细情况，找到CPU占用率高的进程，进入进程所在的目录
2. 查看日志 结合 strce -p pid /  perf top -p pid
3. 第三方程序， kill

### 6 网站访问慢

1. 网络资源  ping 外网
2. 服务器硬件资源 top
3. 软件本身 日志查看
4. 数据块查询慢 数据块性能查询工具


### 7 Nginx 负载模式

轮询 / 加权轮询 / ip hash / 最少链接 / 加权最少链接

### 8 keepalived 工作原理

VRRP 协议选举出来一个master 主节点和多个backup节点

**主节点负责流量分配** 同时给下面的backup节点发送vrrp 协议加密通告信息

当backup节点搜不到通告信息，就认为mater 节点不可用， 优先级最高的backup 节点就会成为新的master


### 9 LVS vs Nginx vs Haproxy

LVS: 四层转发， url 不可转发

Haproxy 七层转发 专业代理服务器

Nginx web 服务器 ，支持 4+7层转发

### 10 提高Mysql的安全性

1. **修改mysql默认端口，不使用3306**
2. **设置访问ip白名单，单台服务器只允许127访问**
3. 设置mysql root用户为强密码，且只允许通过本地连接

4. 为应用数据库设置独立的链接账号，且以最小权限原则进行权限配置，仅允许操作自己应用相关的数据

5. **在服务程序的配置文件中不允许使用明文配置 mysql 密码，（我们一般用aes 算法 配置文件里面存密文）**

6. 关注 数据库相关cve漏洞信息，对出现的安全问题进行及时处理。

7. 若数据库链接需要通过网络传输，则需要配置 ssl 加密传输


### 11 栈内存和堆内存的区别

栈内存存储比较小的东西，like调入一个函数，把函数的参数信息放在栈内存，当函数调用结束，栈内存会被释放掉

递归函数一直被调用，会导致栈内存的溢出， 

堆内存存储一般比较大的的数据，或者不确定大小的数据。新建一个对象用来存储数据的链表


### 12 RAID 是什么

**存储虚拟化的技术**，多个物理磁盘组合在一起，形成一个逻辑上存储单元，存储量变大，安全性提高

**RAID0 把数据分散到不同的硬盘中，提高性能，但是安全性不高** 某一块坏了，数据丢失

 
**RAID1 数据完整的在复制一份，存下来，对于大的逻辑硬盘每个数据是存了两份**  利用率50%

**RAID5 奇偶校验和数据信息， 都会会存在多个硬盘当中**，一块坏了，也能根据奇偶校验信息恢复

### 13 du vs df


Du 查看文件和目录的磁盘使用情况。 便利一个目录地下所有的文件。 计算统计这些东西，所有加在一起，占了多少空间

**Df 显示文件系统所有的总空间， 已经使用的空间和剩余的空间。 基于文件系统的元数据， 来计算磁盘空间的。**

**当文件有多个硬链接，du会计算很多次，df 只计算一次**

**文件被删除， du 判断这个文件不存在了， 空间不会被计算**  df 整个系统层面来计算，它认为这个空间被占用了


### 14 进程优先级

静态的 设置优先级 nice 

正在运行的renice -n nice -p pid 

### 15 Mysql主从

数据同步方式 

Master 记录所有的操作方式，保存在binlog 日志里面，一个二进制的文件

创建一个线程，会把二进制文件传给slave. Slave 读取二进制文件。把信息记录下来，保证了master节点和slave 节点信息的一致


### 16 PDO pending 的原因

1. 硬件资源不足
2. 镜像获取失败
3. 网络插件的原因，网络无法链接
4. pod需要绑定一个PVC， 但是现在没有可用的。Pod创建的请求，scheduler 没有来得及去处理它，没有找到合适资源和node


### 17 shell的三剑客

**1 grep -> 搜索**

```
grep “error” app.log
```


**2 sed -> 加载数据和删除，替换**

```
# A替换成B
sed -i 's/A/B/g' app.txt

# 把C删除
sed -i /s/C//g' app.txt
```

**2 awk -> 把文件按行的形式全部读取出来**

* 默认空格进行切边
* -F 参数指定一个分隔符
	* if 判断
    * begin end
  

### 18 top 展示那些信息

Top 展示系统的是实体信息

1. 时间，用户，负载
2. 进程
3. CPU
4. 内存
5. 交换区
6. 具体进程的状态 （优先级， PID)


### 19 proc 目录

虚拟文件系统，存储系统当时运行的一个状态

### 20 pod的状态

Pending  running succeeded Failed Unknown

### 21 pod如何通信

1.不同的pod同一个节点中

- 通过Node节点的本地网络进行通信
- pod的IP地址进行通信

1.不同的pod在不同的节点（Node)

- pod的IP地址进行通信
- 通过service 通信
- 跨节点的网络插件
- k8s集群中忧有一个**路由表**

### 21 Scheduler 调度流程

Scheduler 持续监听API server 的实物状态

一旦有一个被调度的pod或者正在运行的pod需要被调度

1. 筛选Node,筛选Node, 零容忍，存储等下新 =》 筛选集
2. 根据策略匹配Node，确定node节点之后。pod信息和Node节点进行绑定写入Etcd中


### 22 Nginx 如何找出访问次数最多的IP

Nginx 的 access.log 每次访问都会记录一条数据

`awk uniq -c` 循环的去统计每一个IP的访问次数  ， `sort -nr` 进行排序

`head -n 1` 命令输出第一行 IP访问最高的地址


### 23 Mysql 主从有延迟怎么办

1. 对比主从服务器的硬件配置，主服务器的负载情况，负载量比较大。 Binlog 日志写入会有延迟
2. 数据库的性能监控工具，是否存在很多慢SQL
3. 从网络层面，是否忧网络延迟
4. 主从服务时间不一致

### 23 TCP TIME_WAIT

**TCP 建立链接之后，如果有一方，发起了断开连接的请求。 主动关闭的一方会进入`time_wait`**

### 24 lsof

**打开系统中已经打开的文件和相关的进程**

1. 当前打开的文件被哪一个进程所打开
2. 某一个进程打开了那些文件
3. 端口被那个进程占用
4. 列出所有tcp udp 的链接状态
5. 当前用户打开了那些文件

### 25 Linux 执行命令很慢

**主要原因是缓存的原因**

1 **lscpu 看一下CPU 的缓存**

2 `free -mh` 系统磁盘文件的缓存状态


### 26 二三层交换机的区别

**2层交换机** 使用Mac的寻址， 采用的存储，转发的方式进行数据的转化


**3层交换机** 他是具备的**路由**的转发功能，它能够连接外网，同时还可以选择路由转发的线路

### 27 MySQL 的读写分离

数据大的读取和数据的写入放在不同的服务器上进行

- 1 数据同步， master上面的操作要写入到binlog 日志， binlog 日志与Slave节点，来同步进行写入
- 2 master 作为写入节点， slave 节点作为读取节点

ACID 

* 持久性 数据一旦改变是持久的
* 原子性 事务要不全面被执行，要不不执行
* 一致性 实物开启之后， 数据库大的完整性没有被破坏
* 隔离性 同一个事务，只处理一个请求， 不同事务不同的请求

### 28 Docker 端口的映射机制

将docker 容器的内部端口，映射到主机的端口

`docker run -p 1234:56 test`

容器的56端口映射到主机的1234端口

### 29 docker 的镜像文件存在哪里

**/var/lib/docker 目录下面**


### 30 如何优化docker镜像

1. 使用**更轻量的基础镜像**
2. 尽量减少镜像的层数， 命令进行合并
3. 对于零时和多余文件删除

### 31 高可用架构

1. 负载均衡， Nginx 或者 HAPROXY 云平台的额LB
2. 集群化的管理 k8s
3. 数据块的高可用
4. 存储的高可用  磁盘 RAID5, 分布式存储
5. 故障转移 keepalived

### 32 磁盘空间不足

* `du -sh` 某些空间占用比较大的文件, 归档或者log文件可以删除或者转移
* df -h 磁盘挂载情况
* yum clean all

### 32 优化K8S集群性能

1. pod设置CPU和内存来做资源限制
2. 节点的亲和和反亲和，优化调度
3. 高新能的硬件存储
4. 网络插件进行优化
5. Prometheus 和  Grafana 对整个集群进行监控

### 33 突发流量高峰

1. 限流： API 网关和Nginx进行限流
2. 静态资源用CDN分配
3. 热点资源用Redis 缓存下来
4. 云平台的自动扩展功能

### 34 SSL证书的问题

1. Openssl 查看 证书的内容，确认证书是否过期
2. 检查web服务器 Nginx 对于SSL 配置的
3. SSL 的测试工具，测试一下证书的配置


### 35 优化MYSQL数据库的性能

1. 索引，对常用的查询字段加上索引
2. 慢查询用explain进行分析，它的执行计划
3. 大的表分库分表
4. 缓冲池的大小进行调整
5. 查询热度高的数据，用redis 进行查询


### 36 Redis的优化

- 限制redis对于内存的使用
- redis数据进行持久化
- 集群 redis cluster , 来做分布式
- redis 缓存设置合理的过期时间
- 监控redis 的状态


### 37  Redis内存暴增怎么办

1. Rediscli 查找大key或者热点key
2. 分析内存碎片率
3. 是否开启maxmemory, 淘汰策略
4. 大key拆分， 开启淘汰策略


### 38 MongDB的查询变慢

1. 分析一下慢查询
2. 添加索引
3. 分析一下慢查询
4. 分片数据进行平衡
5. 检查分片键的选择是否合理

### 39 ES报错 too many open files

1. ES日志
2. 系统对文件打开数量的限制 ulimit -a
3. Limits.conf
4. 修改ES的配置文件，把系统参数调大65535

### 40 CICD大的安全

1. 测试，生产，开发环境分离
2. 代码打包进行安全漏洞的扫描
3. 对不同账号角色分配最小的权限
4. 保证正常的使用， 对日常操作的日志全部保留下来，定期审计

### A Kubernetes pod is not starting up and is going in a pending state, how will you troubleshoot this?

**1. Check Pod Events & Details**

```
kubectl describe pod <pod-name> -n <namespace>
```

Focus on the Events section. Common causes include:

- **Insufficient CPU/Memory**: "0/X nodes available: Insufficient cpu/memory"
- **Node Selector Mismatch**: "node(s) didn't match Pod's node affinity"
- **Taints/Tolerations**: "node(s) had untolerated taint {taint-key}"
- **PVC Issues**: "waiting for persistentvolumeclaim <pvc-name> to be bound"

**2. Verify Node Availability**

```
kubectl get nodes
```

Ensure nodes are Ready. If not:

- Check node status: `kubectl describe node <node-name>`
- Investigate node issues (e.g., disk pressure, network errors, kubelet failures).

**3. Check Resource Quotas**

```
kubectl get resourcequotas -n <namespace>
kubectl describe resourcequota <quota-name> -n <namespace>
```

Ensure the pod isn't blocked by CPU/memory/storage quotas.

**4. Inspect Persistent Volume Claims (PVCs)**

```
kubectl get pvc -n <namespace>
kubectl describe pvc <pvc-name> -n <namespace>
```

- If PVC is Pending, check storage class/provisioner issues.
- Verify sufficient storage capacity.

**5. Validate Node Affinity/Taints**

- Node Selector/Affinity:
	- Match pod's nodeSelector/affinity rules to node labels.

- Taints & Tolerations:
	- Ensure pod tolerations match node taints (e.g., for dedicated nodes).

**6. Check Resource Requests/Limits**

```
kubectl get pod <pod-name> -o yaml -n <namespace>
```

Verify CPU/memory requests aren’t exceeding node capacity.

Compare pod requests to node allocatable resources:

```
kubectl describe node <node-name> | grep -A 10 "Allocated resources"
```

**7. Review Scheduler Logs**

Access scheduler logs for scheduling errors:

```
kubectl logs -n kube-system <kube-scheduler-pod-name>
```

Look for warnings like:

* PodFitsResources failed → Resource starvation.
* PodFitsHostPorts → Port conflict.

**8. Check for Cluster Autoscaler Issues**

If using autoscaling (e.g., AWS/GCP), verify:

- Autoscaler pod is running: kubectl get pods -n kube-system
- Cluster has capacity to scale (e.g., max nodes not reached).

**9. Validate Pod Security Policies (PSP) or Admission Controllers**

- If PSP is enabled, ensure the pod has necessary permissions.
- Check for admission controller errors in scheduler logs.

**10. Debug with Ephemeral Containers (Kubernetes ≥v1.23)**

Attach a debug container to inspect the pod's environment:

```
kubectl debug -it <pod-name> --image=busybox --target=<container-name>
```

Common Solutions:

- Resource Starvation: Scale nodes, reduce pod requests, or free up resources.
- Taints/Tolerations: Add tolerations to pod spec or remove node taint.
- PVC Stuck: Delete/recreate PVC, verify storage class exists.
- Node Selector Mismatch: Adjust pod’s nodeSelector or label nodes properly.
- Scheduler Issues: Restart kube-scheduler if logs indicate crashes

**Final Checks:**

- Kubernetes Version: Check for known bugs in your version.
- Events Are Your Friend: Always start with kubectl describe pod.

By systematically addressing these areas, you’ll resolve 95% of Pending pod issues. Start with pod events → resources → PVCs → scheduling constraints.

好的，这是您提供的 Kubernetes Pod 处于 Pending 状态排查指南的中文翻译：

---

要排查 Kubernetes Pod 卡在 **Pending** 状态的问题，请遵循以下结构化步骤：

### 1. **检查 Pod 事件和详情**
   ```bash
   kubectl describe pod <pod名称> -n <命名空间>
   ```
   **重点关注 `Events` 事件部分**。常见原因包括：
   - **CPU/内存不足**: "0/X nodes available: Insufficient cpu/memory" (0/X 个节点可用：CPU/内存不足)
   - **节点选择器不匹配**: "node(s) didn't match Pod's node affinity" (节点与 Pod 的节点亲和性不匹配)
   - **污点/容忍度问题**: "node(s) had untolerated taint {污点键}" (节点存在未被 Pod 容忍的污点)
   - **PVC (持久卷声明) 问题**: "waiting for persistentvolumeclaim <pvc名称> to be bound" (等待持久卷声明 <pvc名称> 完成绑定)

---

### 2. **验证节点可用性**
   ```bash
   kubectl get nodes
   ```
   确保所有节点状态为 **Ready (就绪)**。如果不是：
   - 检查节点状态详情：`kubectl describe node <节点名称>`
   - 调查节点问题（如磁盘压力、网络错误、kubelet 故障）。

---

### 3. **检查资源配额 (Resource Quotas)**
   ```bash
   kubectl get resourcequotas -n <命名空间>
   kubectl describe resourcequota <配额名称> -n <命名空间>
   ```
   确保 Pod 没有被命名空间的 CPU/内存/存储配额所限制。

---

### 4. **检查持久卷声明 (PVCs)**
   ```bash
   kubectl get pvc -n <命名空间>
   kubectl describe pvc <pvc名称> -n <命名空间>
   ```
   - 如果 PVC 状态为 **Pending**，检查存储类 (StorageClass) 或存储供应器 (Provisioner) 问题。
   - 验证存储容量是否充足。

---

### 5. **验证节点亲和性 (Affinity)/污点 (Taints)**
   - **节点选择器/亲和性 (Node Selector/Affinity)**:
     检查 Pod 的 `nodeSelector` 或 `affinity` 规则是否与节点标签匹配。
   - **污点与容忍度 (Taints & Tolerations)**:
     确保 Pod 的容忍度 (`tolerations`) 能够匹配节点的污点 (例如专用节点上的污点)。

---

### 6. **检查资源请求/限制 (Requests/Limits)**
   ```bash
   kubectl get pod <pod名称> -o yaml -n <命名空间>
   ```
   - 验证 Pod 的 CPU/内存 `requests` (请求值) 是否超过了节点容量。
   - 将 Pod 的资源请求与节点的可分配资源进行比较：
     ```bash
     kubectl describe node <节点名称> | grep -A 10 "Allocated resources" (已分配资源)
     ```

---

### 7. **查看调度器日志 (Scheduler Logs)**
   访问 Kubernetes 调度器日志以查找调度错误信息：
   ```bash
   kubectl logs -n kube-system <kube-scheduler-pod名称>
   ```
   查找类似如下的警告：
   - `PodFitsResources failed` → 资源不足。
   - `PodFitsHostPorts` → 端口冲突。

---

### 8. **检查集群自动伸缩器 (Cluster Autoscaler) 问题**
   - 如果启用了集群自动伸缩 (例如 AWS/GCP)，请验证：
     - 自动伸缩器 Pod 是否在运行：`kubectl get pods -n kube-system`
     - 集群是否有容量进行扩展（例如，未达到最大节点数）。

---

### 9. **验证 Pod 安全策略 (PSP) 或准入控制器 (Admission Controllers)**
   - 如果启用了 PSP (Pod Security Policies)，确保 Pod 具有必要的权限。
   - 在调度器日志中检查准入控制器相关的错误。

---

### 10. **使用临时容器调试 (Kubernetes ≥v1.23)**
   附加一个调试容器来检查 Pod 的环境：
   ```bash
   kubectl debug -it <pod名称> --image=busybox --target=<容器名称>
   ```

---

### 常见解决方案：
- **资源不足 (Resource Starvation)**: 扩展节点数量、降低 Pod 的资源请求、或释放现有资源。
- **污点/容忍度 (Taints/Tolerations)**: 在 Pod 规约中添加容忍度 (`tolerations`) 或移除节点的污点 (`taint`)。
- **PVC 卡住 (PVC Stuck)**: 删除并重新创建 PVC，确保引用的存储类 (`StorageClass`) 存在且可用。
- **节点选择器不匹配 (Node Selector Mismatch)**: 调整 Pod 的 `nodeSelector` 或正确地为节点打标签。
- **调度器问题 (Scheduler Issues)**: 如果日志显示调度器崩溃，尝试重启 kube-scheduler Pod。

### 最后要点：
- **Kubernetes 版本**: 检查您使用的版本是否存在已知的相关 Bug。
- **事件是关键**: `kubectl describe pod` 命令输出的事件 (`Events`) 始终是最重要的起点。

通过系统地排查这些方面，您将能解决 95% 的 Pod 卡在 Pending 状态的问题。排查顺序建议：**Pod 事件 → 资源问题 → PVC 问题 → 调度约束**。



### 第一部分：PostgreSQL 基础

#### 1 PostgreSQL 的关键特性有哪些？

ACID 合规性、MVCC（多版本并发控制）、JSON/BSON 支持、全文搜索、自定义数据类型、索引（GIN, GiST, BRIN）、复制、分区、逻辑与物理备份。

#### 2 PostgreSQL 支持哪些数据类型？

数值型、文本型、日期/时间型、几何型、JSON/JSONB、UUID、数组、范围类型、复合类型、自定义类型。

#### 3 PostgreSQL 中的 MVCC 如何工作？

- 每个事务看到数据库在特定时间点的快照。
- **修改会写入新版本，Vacuum 进程负责清理旧版本**。


### 第二部分：管理与性能

#### 1 如何监控 PostgreSQL 性能？

使用 `pg_stat_activity`、`pg_stat_user_tables`、`auto_explain`、`pgBadger` 及外部工具如 Prometheus、Grafana。

#### 2 什么是 Autovacuum？为何重要？

自动清理死元组（Dead Tuples），防止表膨胀并维持性能。

#### 3 如何检查表膨胀？

使用 `pg_stat_user_tables`、`pgstattuple` 或工具如 `pg_bloat_check`。

#### 4 如何优化 PostgreSQL 性能？

调整 `shared_buffers`、`work_mem`、`maintenance_work_mem`、`effective_cache_size` 和 `wal_buffers`。

**有效使用索引并定期执行 VACUUM/ANALYZE。**

#### 5 什么是 WAL 文件？

**预写日志（Write-Ahead Logs）：通过先将更改写入磁盘再应用到数据文件，确保数据持久性。**

### 第三部分：备份与恢复

#### 1 PostgreSQL 的备份类型有哪些？

SQL 转储（`pg_dump`）、文件系统级备份、持续归档（WAL）、基础备份（`pg_basebackup`）。

#### 2. 如何对数据库进行逻辑备份？

**使用 `pg_dump` 或 `pg_dumpall`。**

#### 3. 如何从转储文件恢复数据库？

**使用 psql 或 `pg_restore`（取决于备份格式）**

#### 4 什么是 PITR（时间点恢复）？

**使用基础备份 + WAL 文件将数据库恢复到特定时间点。**


#### 5 如何设置 PITR 的持续归档？

**启用 `archive_mode = on`，并配置 `archive_command` 将 WAL 文件复制到安全位置。**

### 第四部分：复制与高可用

#### 1. 什么是流复制？

一种通过网络连接将 WAL 实时发送到副本的方法。

#### 2. 物理复制与逻辑复制的区别？

- 物理复制字节级变更（基础备份 + WAL），
- 逻辑复制在表级别使用 SQL 语句复制变更。

#### 3. 如何设置流复制？

**使用 `pg_basebackup` 复制数据目录，配置` primary_conninfo` 并使用复制槽。**

#### 4. 什么是复制槽？

一种保留 WAL 文件直到被副本消费的机制，防止异步复制中的数据丢失。

#### 5. 什么是 pg_hba.conf？


PostgreSQL 的客户端认证配置文件。

#### 6. 如何监控复制延迟？
 
**通过查询 `pg_stat_replication` 视图（如 `write_lag`, `replay_lag` 字段）。**

#### 7. 能否仅复制特定表？

可以，使用逻辑复制及发布/订阅模型。

#### 8. 如何故障转移到备库？

使用 pg_ctl promote 提升备库，或通过 Patroni、repmgr、Pacemaker 等工具配置。

#### 9. 什么是 repmgr？

**用于 PostgreSQL 复制管理的工具，支持自动故障转移、监控和管理。**

### 第五部分：多数据中心与分布式部署

#### 1. 如何跨数据中心部署 PostgreSQL？

**使用异步复制并处理网络延迟**。工具如 BDR、Citus 或 Bucardo 可用于多主或分片架构。

#### 2. 多数据中心复制的挑战？

网络延迟、数据一致性、故障转移协调与冲突解决。

#### 3. 什么是 BDR（双向复制）？

支持 PostgreSQL 多主复制的扩展。

#### 5. 如何处理复制冲突？

在逻辑复制或 BDR 中需定义冲突解决规则，或设计无冲突模式。

#### 6. 如何确保分布式架构的高可用？

结合复制、监控、故障转移编排及基于 DNS 或代理的重定向。

### 第六部分：安全与访问控制

#### 1. 如何管理用户角色？

使用 CREATE ROLE、GRANT、REVOKE 命令。

#### 2. 如何进行用户认证？

通过 `pg_hba.conf` 配置认证方式（如 md5, scram-sha-256, peer, trust, 证书认证）。

#### 3. 什么是行级安全（RLS）？

通过 CREATE POLICY 控制用户对行的访问权限。

#### 5. 如何启用 SSL 连接？

配置 `ssl = on`，并提供 `ssl_cert_file` 和 `ssl_key_file`。

#### 6. 如何审计数据库活动？

使用日志（`log_statement, log_duration`）或扩展如 pgAudit。

### 第七部分：故障排查与维护

#### 1. 如何识别慢查询？
   
使用 `pg_stat_statements、EXPLAIN ANALYZE、auto_explain` 或日志工具。

#### 2. VACUUM 的作用？

**清理死元组并释放空间。ANALYZE 更新查询计划的统计信息。**

#### 3. VACUUM FULL 与常规 VACUUM 的区别？
   
**VACUUM FULL 重写表并回收磁盘空间，但会锁表。**

#### 4. 死锁的成因与预防？

多个事务相互等待资源。通过一致的锁顺序和短事务预防。

#### 7. 如何查看当前锁？

查询 `pg_locks` 并与 `pg_stat_activity` 关联。


### 第八部分：高级主题

#### 1. 如何实现并行查询？

对适用查询使用多工作进程（基于优化器决策）。

#### 2. 什么是分区？

按范围/列表/哈希规则将大表拆分为小表。

#### 3. 如何分析与优化查询？

**使用 EXPLAIN 和 EXPLAIN ANALYZE，检查索引并考虑重写。**

#### 4. 什么是 CTE？有何作用？
 
公共表表达式（Common Table Expressions）；用于编写可读的子查询和递归查询。

#### 5. 如何升级 PostgreSQL？

使用 `pg_upgrade` 原地升级，或通过转储/恢复迁移。

## 🛠️ PostgreSQL（SRE 专项）

#### 1. 如何设计高可用集群？

**说明主-备流复制、复制槽、同步/异步模式及自动故障转移工具**（如 repmgr、Patroni 或自定义脚本）。

#### 2. 如何监控和缓解复制延迟？

**监控指标（`pg_stat_replication` → `write_lag`, `replay_lag`）**、告警阈值、连接优化、跨数据中心网络延迟。

#### 3. 多数据中心如何实现 PITR？
   
基础备份 + 通过 `archive_command` 归档 WAL，按时间戳恢复，考虑网络因素。

#### 4. 如何在线变更表结构（如加列/索引）？

使用非阻塞操作：`ALTER TABLE ... ADD COLUMN`、`CREATE INDEX CONCURRENTLY`，并在副本滚动更新。

#### 5. 如何预防表/索引膨胀？
   
**调优 Autovacuum、手动执行 VACUUM/ANALYZE**、通过 `pg_stat_user_tables` 监控、使用 `pg_bloat_check` 工具。

#### 6. 如何排查生产环境慢查询？
   
使用 `EXPLAIN ANALYZE`、`pg_stat_statements`、检查索引、I/O 等待、优化器误判；建议日志记录与分析策略。

#### 7. 描述备份与灾难恢复流程

**逻辑备份（`pg_dump`）、物理备份（`pg_basebackup`）、WAL 归档、跨数据中心备份、恢复脚本、灾备演练**。

#### 8. 逻辑复制的价值？如何用于分片？

发布/订阅模型、表过滤、模式同步影响，使用 pglogical 或内置逻辑复制。

#### 9 如何最小化停机升级？

使用 `pg_upgrade --link`、通过复制逻辑升级、或基于副本的蓝绿部署。

#### 10. Prometheus/Grafana 监控哪些指标？

复制延迟、活跃连接数、锁、长查询、索引使用率、磁盘 I/O、`pg_stat_connections`、`pg_stat_bgwriter`。


### 🌐 HTTP（SRE 专项）

#### HTTP 请求的生命周期？

DNS 解析 → TCP 握手 → TLS 握手 → HTTP 请求/响应 → 连接关闭或保持（Keep-Alive）。

#### SRE 需关注的 HTTP 状态码？

**2xx（成功）、3xx（重定向循环）、4xx（客户端错误）、5xx（服务端错误），及其触发条件。**

#### HTTP Keep-Alive 与连接池的作用？

复用 TCP/TLS 连接降低延迟与资源消耗；禁用 Keep-Alive 的场景。

#### 如何监控 HTTP 端点性能？

使用 APM、合成监控、真实用户监控、延迟百分位（p95/p99）、错误预算跟踪。

#### HTTP/2 的优势与 SRE 关注点？

多路复用、头部压缩、服务端推送；

需警惕队头阻塞（HOL blocking）和资源优先级问题。

#### 如何调试 HTTP 性能退化？

使用请求追踪、分布式追踪、慢响应日志、瀑布流分析。

#### REST 与 gRPC 的 SRE 视角对比？

REST：人类可读、客户端开销大；

gRPC：二进制传输、基于 HTTP/2、强契约；需权衡性能、工具链和可观测性。

#### HTTP 的重试与幂等性？

幂等方法（GET/PUT）的安全重试（含退避机制），非幂等请求的处理策略。

#### 如何保护生产环境 HTTP 流量？

TLS 终止、HSTS 策略、证书轮换、漏洞扫描、安全头部（CSP, X-Frame-Options 等）。

#### SRE 需防御哪些 HTTP 层攻击？

**DDoS 攻击、速率限制、流量整形、熔断机制、WAF（Web 应用防火墙）、异常检测。**


### Jenkins 

#### 01 Jenkins是什么，其主要功能是什么？

**Jenkins是一个开源的持续集成和持续交付工具**。它的主要功能是自动化构建、测试和部署软件项目。

#### 02 Jenkins如何实现持续集成？

Jenkins通过不断监测版本控制系统（如Git、Subversion等）中的代码变化，触发构建过程，并进行自动化的编译、测试和部署，从而实现持续集成。


#### 03 Jenkins的工作原理是什么？

Jenkins采用主从架构，主节点负责任务调度和分发，从节点负责具体的构建任务。

**它监听版本控制系统的变化，触发构建过程，并提供丰富的插件和扩展机制来支持各种开发和部署需求。**

#### 04 Jenkins的主要组件有哪些？

Jenkins的主要组件包括主**节点（Master）、从节点（Slave/Agent）、任务（Job）、构建（Build）、插件（Plugin）**

#### 06 Jenkins的主要配置文件是什級？

Jenkins的主要配置文件是config.xml，其中包含了Jenkins的全局配置信息，如邮件通知、权限管理、节点配置等。

#### 07 Jenkins构建过程中常见的问题有哪些？

构建过程中可能会遇到各种问题，如构建失败、权限不足、代码拉取失败等。具体问题可能因项目和环境而异。

#### 08 如何配置Jenkins实现定时构建？

可以在任务的配置页面中，通过“Build Triggers”选项配置定时构建。使用Cron表达式或简单的定时规则来指定构建的触发时间。

#### 11 Jenkins如何与版本控制系统集成？

Jenkins支持多种版本控制系统，如Git、SVN等。在Jenkins的任务配置页面中，通过 “Source Code Management"选项可以配置与版本控制系统的集成。需要设置代码仓库地址、认证信息等，以便Jenkins能够拉取代码进行构

#### 12 如何创建一个Jenkins任务（Job）？

可以通过Jenkins的Web界面，选择“New Item"来创建一个新的任务。在任务配置页面，可以设置任务的名称、触发器（如定时构建、轮询SCM等）、构建步骤（如执行Shell命令、调用Maven构建等）以及构建后的操作（如发送邮件通知、发布构建产物等）

#### 13 Jenkins的构建过程是怎样的？

Jenkins的构建过程通常包括以下几个步骤：

* 代码拉取（从版本控制系统拉取代码）、
* 编译（执行编译命令，如Maven的mvn clean install）、
* 测试（执行测试脚本，生成测试报告）、
* 打包（将构建产物打包成可发布的格式，如JAR、WAR等）
* 以及部署（将构建产物部署到目标环境


