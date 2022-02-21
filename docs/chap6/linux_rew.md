# Linux review

## **Linux 进行性能诊断**

### CPU利用率

CPU使用率，就是程序对CPU时间片的占用情况

**即`CPU使用率 = CPU时间片被程序使用的时间 / 总时间`。**

CPU利用率显示的是程序在运行期间实时占用的CPU百分比。


### CPU 平均负载

**CPU 平均负载，以及在 io 上被阻塞的任务。**

```
$ uptime 
23:51:26 up 21:31, 1 user, load average: 30.02, 26.43, 19.02
```

这三个值是系统计算的 1 分钟、5 分钟、15 分钟的指数加权的动态平均值

CPU的使用率还是100%，但是工作负载则变成2了。所以也就是说，CPU的工作负载越大，代表CPU必须要在不同的工作之间进行频繁的工作切换。无论CPU的利用率是高是低，跟后面有多少任务在排队(CPU负载)没有必然关系

###  **什么是load average？**

linux系统中的Load 是对当前CPU工作量的度量 (WikiPedia: **the system load is a measure of the amount of work that a computer system is doing**)。

**查看物理CPU个数**

```
cat /proc/cpuinfo | grep “physical id”| sort | uniq | wc -l
```

**查看每个物理CPU中core的个数(即核数)**

```
cat /proc/cpuinfo | grep “cpu cores” | uniq
```

**查看逻辑CPU的个数**

```
cat /proc/cpuinfo| grep “processor”| wc -l
```


### 2、如果CPU负载很高，利用率却很低该怎么办

**CPU负载很高，利用率却很低，说明处于等待状态的任务很多，负载越高，代表可能很多僵死的进程。通常这种情况是IO密集型的任务，大量任务在请求相同的IO，导致任务队列堆积。**

* 磁盘读写请求过多就会导致大量I/O等待
* MySQL中存在没有索引的语句或存在死锁等情况



### top

* The same output you can find using Linux uptime command.
* Row 2 shows the **number of process running on the server and their state**. （total,  running, sleeping, stopped, zombie)
	* Zombie process is a process that has completed execution but still has an entry in the process table.
* Row three shows the **CPU utilization** status on the server, you can find here **how much CPU is free and how much is utilizing by the system**.
* Row 4 shows the **memory utilization** on the server, you can find here how much memory is used, the same results you can find using free command.
	* **<mark>Total system memory, current used by memory system, free memory, total memory used by Buffers</mark>**
* **Row 5 shows the swap memory utilization on the server**, you can find here how much swap is being used, the same results you can find using free command.
	* **<mark>In general terms, swap is a part of the hard disk used as RAM on the system</mark>.**
	* **Total Swap memory in System** 
	* **Current USED Swap Memory by system**
	* **Free Swap Memory**
	* **Total Cached memory by system**
*  Row #6  **running process on servers and there additional details about them like below.**


### linux系统里，buffer和cache如何区分？

buffer和cache都是内存中的一块区域，

* 当CPU需要写数据到磁盘时，由于磁盘速度比较慢，所以CPU先把数据存进buffer，
* 当CPU需要从磁盘读入数据时，由于磁盘速度比较慢，可以把即将用到的数据提前存入cache，CPU直接从Cache中拿数据要快的多。


## **Linux 文件系统**

### **inode**

**文件系统包含元数据（索引数据）以及数据内容（数据块）**

**inode即为元数据，inode记录数据块的位置及索引信息（文件属性，ctime，atime，mtime等）**

* 文件的字节数，块数
* 文件拥有者的User ID
* 文件的Group ID
* 文件的读、写、执行权限
* 文件的时间戳，共有三个：ctime指inode上一次变动的时间，mtime指文件内容上一次变动的时间，atime指文件上一次打开的时间。
* 链接数，即有多少文件名指向这个inode
* 文件数据block的位置
* inode编号

### **inode的大小**

所以硬盘格式化的时候，操作系统自动将硬盘分成两个区域。

**一个是数据区，存放文件数据；另一个是inode区（inode table），存放inode所包含的信息**

**硬链接**

Unix/Linux系统允许，多个文件名指向同一个inode号码。

可以用不同的文件名访问同样的内容；对文件内容进行修改，会影响到所有文件名；**但是，删除一个文件名，不影响另一个文件名的访问。这种情况就被称为”硬链接”（hard link）**

`ln -i`

**软链接**

**文件A和文件B的inode号码虽然不一样，但是文件A的内容是文件B的路径。**

**ln创建的是硬链接，带-s参数的ln命令创建的是软链接。**

## **负载均衡**

**七层负载均衡**：就是可以根据访问用户的 HTTP 请求头、URL 信息将请求转发到特定的主机。

* DNS 重定向
* HTTP 重定向
* 反向代理

**四层负载均衡：**基于 IP 地址和端口进行请求的转发。

* 修改 IP 地址
* 修改 MAC 地址


**DNS 负载均衡 /  HTTP 负载均衡 / 反向代理负载均衡 / IP负载均衡 / 数据链路层负载均衡**

### **负载均衡算法**

* 随机
	* 随机算法
	* 加权随机算法

* 轮询
	* 轮询算法
	* 加权轮询算法
*  最小活跃数 / 源地址哈希 / 一致性哈希


## 常见网络编程面试题

* 网络协议是什么？

 简述TCP三次握手，四次断开，及其优点和缺点，同时相对于UDP的差别？

### **简述TCP三次握手，四次断开**

* `Client -> Server`:  `SYN_SENT ` -> SYN -> `SYN-RCV` (Listen)
* `Server -> Client`: `SYN-RCV` -> `SYN+ACK` -> ESTABLSHED
* `Client -> Server`: `ESTABLSHED ` -> `ACK+请求数据` -> `ESTABLSHED `

* 第一次握手：**客户端发送syn包(syn=j)到服务器，并进入`SYN_SEND`状态，等待服务器确认**；
* 第二次握手：**服务器收到syn包，必须确认客户的SYN（ack=j+1），同时自己也发送一个SYN包（syn=k），即SYN+ACK包，此时服务器进入`SYN_RECV`状态**；
* 第三次握手：**客户端收到服务器的`SYN＋ACK`包，向服务器发送确认包`ACK(ack=k+1)`，此包发送完毕，客户端和服务器进入ESTABLISHED状态**，完成三次握手。

### **四次断开：**

* 当主机A完成数据传输后，将控制位FIN置1，提出停止TCP连接的请求；
* 主机B收到FIN后对其作出响应，确认这一方向上的TCP连接将关闭，将ACK置1；
* 主机B再提出反方向的关闭请求，将FIN置1；
* 主机A对主机B的请求进行确认，将ACK置1，双方向的关闭结束

* A -> B (`FIN_WAIT1` -> FIN -> `CLOSE_WAIT`)
* B -> A (`CLOSE_WAIT` -> ACK -> `FIN_WAIT2`)
* B -> A (`LAST_ACK` -> FIN -> `TIME_WAIT`)
* A -> B (`TIME_WAIT` -> ACK) 

### netstat

```
netstat -nlp |grep LISTEN   //查看当前所有监听端口·
```

## **监听端口**

侦听端口是应用程序或进程在其上侦听的网络端口，充当通信端点。

每个监听端口都可以使用防火墙打开或关闭（过滤）。一般而言，开放端口是一个网络端口，它接受来自远程位置的传入数据包。

```
sudo netstat -tunlp
```

* `-t`-显示TCP端口。
* `-u` -显示UDP端口。
* `-n` -显示数字地址而不是解析主机。
* `-l` -仅显示监听端口。
* `-p` -显示侦听器进程的PID和名称。仅当你以root用户或 sudo 用户身份运行命令时，才会显示此信息。

```
Proto Recv-Q Send-Q Local Address   Foreign Address     State       PID/Program name      
tcp        0      0 0:22              0:*               LISTEN      445/sshd              
tcp        0      0 0:25              0:*               LISTEN      929/master            
tcp6       0      0 :::3306           ::*               LISTEN      534/mysqld            
tcp6       0      0 :::80             :::*              LISTEN      515/apache2           
tcp6       0      0 :::22             :::*              LISTEN      445/sshd              
tcp6       0      0 :::25             :::*              LISTEN      929/master            
tcp6       0      0 :::33060          :::*              LISTEN      534/mysqld            
udp        0      0 0:68              0:*                           966/dhclient  
```

* Proto-套接字使用的协议。
* Local Address -进程侦听的IP地址和端口号。
* PID/Program name -PID和进程名称。

```
sudo netstat -nlp | grep 22
```

```
tcp        0      0 0:22              0:*               LISTEN      445/sshd  
tcp6       0      0 :::22             :::*              LISTEN      445/sshd  
```

### **用ss**

检查监听端口

ss是新的netstat。它缺少netstat的某些功能，但是公开了更多的TCP状态，并且速度稍快。命令选项基本相同，因此从netstat到ss的转换并不困难。

要使用ss获取所有监听端口的列表，请输入：

```
sudo ss -tunlp
```

输出与netstat报告的输出几乎相同：

```
State    Recv-Q   Send-Q     Local Address:Port      Peer Address:Port                                                                                          
LISTEN   0        128              0:22             0:*      users:(("sshd",pid=445,fd=3))                                                          
LISTEN   0        100              0:25             0:*      users:(("master",pid=929,fd=13))                                                       
LISTEN   0        128                    *:3306                 *:*      users:(("mysqld",pid=534,fd=30))                                                       
LISTEN   0        128                    *:80                   *:*      users:(("apache2",pid=765,fd=4),("apache2",pid=764,fd=4),("apache2",pid=515,fd=4))     
LISTEN   0        128                 [::]:22                [::]:*      users:(("sshd",pid=445,fd=4))                                                          
LISTEN   0        100                 [::]:25                [::]:*      users:(("master",pid=929,fd=14))                                                       
LISTEN   0        70                     *:33060                *:*      users:(("mysqld",pid=534,fd=33))  
```

### **使用lsof**

检查监听端口

lsof是功能强大的命令行应用程序，可提供有关进程打开的文件的信息。

在Linux中，所有内容都是文件。你可以将套接字视为写入网络的文件。

要获取具有lsof的所有侦听TCP端口的列表，请输入：

```
sudo lsof -nP -iTCP -sTCP:LISTEN  
```

使用的选项如下:

* `-n`-不要将端口号转换为端口名称。
* `-p` -不解析主机名，显示数字地址。


-iTCP -sTCP:LISTEN -仅显示TCP状态为LISTEN的网络文件。

```
COMMAND   PID     USER   FD   TYPE DEVICE SIZE/OFF NODE NAME  
sshd      445     root    3u  IPv4  16434      0t0  TCP *:22 (LISTEN)  
sshd      445     root    4u  IPv6  16445      0t0  TCP *:22 (LISTEN)  
apache2   515     root    4u  IPv6  16590      0t0  TCP *:80 (LISTEN)  
mysqld    534    mysql   30u  IPv6  17636      0t0  TCP *:3306 (LISTEN)  
mysqld    534    mysql   33u  IPv6  19973      0t0  TCP *:33060 (LISTEN)  
apache2   764 www-data    4u  IPv6  16590      0t0  TCP *:80 (LISTEN)  
apache2   765 www-data    4u  IPv6  16590      0t0  TCP *:80 (LISTEN)  
master    929     root   13u  IPv4  19637      0t0  TCP *:25 (LISTEN)  
master    929     root   14u  IPv6  19638      0t0  TCP *:25 (LISTEN)  
```

* COMMAND，PID，USER-运行与端口关联的程序的名称，PID和用户。
* NAME -端口号。

要查找正在侦听特定端口（例如端口3306）的进程，可以使用：

```
sudo lsof -nP -iTCP:3306 -sTCP:LISTEN  
```
输出显示MySQL服务器使用端口3306:

```
COMMAND PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME  
mysqld  534 mysql   30u  IPv6  17636      0t0  TCP *:3306 (LISTEN)
```