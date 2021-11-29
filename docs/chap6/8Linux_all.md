# **L8 Linux 面试题 合集CDN**

## **Linux Command & Bash**

**1.查找文件后缀是log的三天前的文件删除和三天内没修改过的文件**

```
find / -name ".log" -mtime +3 -exec rm fr {} ; 

find /log ! -mtime -3
```

**2.写一个脚本将目录下大于100kb的文件移动到`/tmp`下**

```
fimd / -size +100k -exec mv {} /tmp ;
```

**3.将数据库备份并打包传递到远程服务器192.168.1.1的`/backup`目录下**

```
mysqldump -u root -p database > database.sql;

tar -czvf database.tar.gz database.sql ; 

rsync -avP ./database.tar.gz root@192.168.1.1:/backup
```

**4.日志如下统计访问ip最多的前10个**

```
awk '{print $1}'  *.log | sort | uniq -c | sort -nr | head -n
```

**5.把`/usr/local/`替换成其他的目录**

```
sed -i 's//usr/local//目录/g' 文件
```
**6.查看服务器程序运行级别和修改运行级别，和服务的运行级别**

* 查看：`who -r`
* 修改：`etc/inittab`
* 服务运行级别 `chkconfig –list vsftp`
* 修改：`chkconfig –level 345 vsftp on`

**7.用tcpdump截取本机`ip 192.168.23.1 80`端口的包**

```
tcpdump tcp port 80 host 192.168.23.1

Tcpdump -w test host 192.168.1.1 and tcp port 80
```

**8.用tcpdump截取ip `192.168.23.1`访问主机 ip `192.168.23.2` 的80端口的包**

```
tcpdump host 192.168.23.1 and 192.168.23.2 and dst port 80
```

**9.用`iptable`s将`192.168.0.100`的`80`端口映射到`59.15.17.231`的`8080`端口**

```
iptables -t nat -A PREROUTINT -p tcp -d 192.168.0.100 –dport 80 -j DNAT –to-destination 59.15.17.231:8080
```

**10.本机的80端口转发到8080**

```
iptables -t nat -A PREROUTING -p tcp –dport -j REDIRECT –to-ports 8080
```

**11.禁止一个用户登录，但可以使用ftp**

修改`etc/passwd` 最后一个字段 改成/`bin/nologin`

**12.获取1.txt中第二行第三列的数据，输出到2.txt**

```
cat 1.txt|awk ’NR==2{print $3}’ > 2.txt
```

**13.查看Linux系统当前单个共享内存段的最大值**

```
ipcs -a
```

**14.用什么命令查询指定IP地址的服务器端口**

```
nmap 127.0.0.1
```

**15.如何让history命令显示具体时间**

```
HISTTIMEFORMAT=”%Y-%m-%d %H:%M:%S ”
```

**16.查看Linux系统当前加载的库文件**

```
lsof |grep /lib
```

**17.查看当前系统某一硬件的驱动版本。比如网卡**

```
ethtool –i eth0
```

**18.DNS服务器有哪三种类型**

主 从 转发

**19.查看3306端口被谁占用**

```
lsof -i:3306
```

**20.查看占用内存最大的5个进程**

```
ps -aux | sort -k4nr | head -n 5
```

**21.查看占用内存最大的进程的PID和VSZ**

```
ps -aux|sort -k5nr|awk ’BEGIN{print ”PID VSZ”}{print $2,$5}’|awk ’NR<3′
```

**22. `lsof -p 12` 看进程号为12的进程打开了哪些文件**

**23.同时执行a和b等a和b都执行完执行c**

```
#!/bin/bash

./a.sh &

./b.sh &

wait

echo adf
```

**24.snmpdf 通过SNMP监视远程主机的磁盘空间**

```
snmpdf -v 1 -c public localhost
```

获取192.168.6.53的所有开放端口状态

```
snmpnetstat -v 2c -c public -a 192.168.6.53
```

**25.简述编译kernel的大体步骤**


(1)下载解压缩新版本的内核到/usr/src下

(2)将以前版本链接删除，建立新的连接

(3)编译内核，编译模块，安装模块

(4)修改grub.conf ，然后重启


**26.`diff/patch`的作用和用法**

**命令diff A B > C ,一般A是原始文件，B是修改后的文件，C称为A的补丁文件。**

patch A C 就能得到B, 这一步叫做对A打上了B的名字为C的补丁

27.执行 `bin/myprog` 返回`0` 打印ok `1`打印bad `2`打印error 其他打印 wrony

```
./bin/myprog

if [[ $? = 0 ]];then

echo”OK”

elif [[ $? =1 ]];then

echo”bad”

else

echo”error”

fi
```

**28.求一组数的最大值和最小值**

```
#!/bin/sh

min=$1

max=$1

sum=$1

shift

while [ $# -gt 0 ]

do

if [ $min -gt $1 ]

then

min=$1

fi

if [ $max -lt $1 ]

then

max=$1

fi

sum=`expr $sum +$1`

shift

done

sum=`echo ”$sum/5″`|bc -l

echo min=$min

echo max=$max

echo aver=$sum
```

**28.执行可执行程序test并把输出和错误写到err.log**

```
./test > & err.log
```

**29.用telnet连接校内服务器mail.xiaonei.com 发一封信**

```
mail -v -s ”hello” root@192.168.23.1
```

**30.添加路由表并查看**

```
route add -net 203.208.39.104 netmask 255.255.255.255 gw 192.168.1.1

netstat –r
```

**31.正则匹配ip**

```
((25[0-5]|2[0-4]d|1dd|[1-9]d|d).){3}(25[0-5]|2[0-4]d|1dd|[1-9]d|[1-9])
```


**32.把`/data`目录及其子目录下所有以扩展名`.txt`结尾的文件中包含`oldgirl`的字符串全部替换为`oldboy`**

```
find /data/ -type f -name "*.txt" | xargs sed -i 's/oldgirl/oldboy/g'
```

**33. 统计一下`/var/log/nginx/access.log` 日志中访问量最多的前十个IP?**

`cat access_log | awk ‘{print $1}’ | uniq -c|sort -rn|head -10`
或

`awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -nr -k1 | head -n 10`

* `uniq` - **删除排序文件中的重复行**
* `sort`**对于文本进行排序**
* `-l` **按照当前环境排序**.
* `-m` 合并已经排序好的文件,不排序.
* `-n` 按照字符串的数值顺序比较,暗含-b
* `-r` 颠倒比较的结果.

**34. 怎么查看当前系统中每个IP的连接数**：

```
# netstat -n | awk '/^tcp/ {print $5}' | awk -F: '{print $1}' | sort | uniq -c| sort –rn
```

* `sort`命令：进行排序，-r 反向排序 -n 使用纯数字进行排序
* `uniq` 将重复的数据仅仅列出一个来显示，uniq -c，进行计数
* `awk -F`: '{print $1}' 以F 为分界符，取出第一个：之前的数据


**35 怎么查看当前磁盘的IO：**

iostat 是 sysstat 工具集的一个工具，需要安装。

```
[root@localhost ~]# iostat -d-k 1 10
Linux 3.10.0-327.el7.x86_64 (localhost.localdomain) 03/23/2017 _x86_64_(2 CPU)
Device: tps kB_read/s kB_wrtn/s kB_read kB_wrtn
sda 16.60 597.83 29.44 384048 18909
scd0 0.03 0.10 0.00 66 0
dm-0 15.78 551.54 26.20 354311 16831
dm-1 0.22 1.97 0.00 1268 0
```

* 参数` -d `表示，显示设备（磁盘）使用状态；
* `-k` 某些使用block为单位的列强制使用`Kilobytes`为单位；
* `1 10` 表示，数据显示每隔1秒刷新一次，共显示10次。

2）使用iotop命令

iotop命令是一个用来监视磁盘I/O使用状况的top类工具。iotop具有与top相似的UI。Linux下的IO统计工具如iostat，nmon等大多数是只能统计到per设备的读写情况，如果你想知道每个进程是如何使用IO的就比较麻烦，使用iotop命令可以很方便的查看。


**36 查看当前连接数：**

```
[root@localhost ~]# cat /proc/sys/net/netfilter/nf_conntrack_count
0
```


**iptables的链接跟踪表最大容量配置文件如下：**

```
[root@localhost ~]# cat /proc/sys/net/netfilter/nf_conntrack_max
65536
```

## **Mysql**

**1.如何判断mysql主从是否同步？该如何使其同步？**

```
Slave_IO_Running
Slave_SQL_Running；
```

**2.mysql的innodb如何定位锁问题，mysql如何减少主从复制延迟？**

在使用 `show engine innodb status` 检查引擎状态时，发现了死锁问题

在5.5中，`information_schema `库中增加了三个关于锁的表（MEMORY引擎：`innodb_trx ##` 当前运行的所有事务`innodb_locks ##` 当前出现的锁`innodb_lock_waits ##` 锁等待的对应关系

**3.mysql如何减少主从复制延迟:**

* 1.从库硬件比主库差，导致复制延迟
* 2.主从复制单线程，如果主库写并发太大，来不及传送到从库，就会导致延迟。更高版本的mysql可以支持多线程复制
* **3.慢SQL语句过多**
* 4.网络延迟
* 5.master负载
	* **主库读写压力大，导致复制延迟，架构的前端要加`buffer`及缓存层**

另外， **2个可以减少延迟的参数:**
	
* `-slave-net-timeout=seconds` 单位为秒 默认设置为 3600秒  
	* **参数含义：当slave从主数据库读取log数据失败后，等待多久重新建立连接并获取数据**

* `-master-connect-retry=seconds` 单位为秒 默认设置为 60秒
	* **参数含义：当重新建立主从连接时，如果连接建立失败，间隔多久后重试。**

* 通常配置以上2个参数可以减少网络问题导致的主从数据同步延迟

**MySQL数据库主从同步延迟解决方案**

最简单的减少slave同步延时的方案就是在架构上做优化，尽量让主库的DDL快速执行。还有就是主库是写，对数据安全性较高，比如 `sync_binlog=1`，

Mysql开启`bin-log`日志使用`bin-log`时，默认情况下，并不是每次执行写入就与硬盘同步，这样在服务器崩溃是，就可能导致`bin-log`最后的语句丢失。
可以通过这个参数来调节，`sync_binlog=N`，使执行`N`次写入后，与硬盘同步。

**`1` 是最安全的，但是也是最慢的`innodb_flush_log_at_trx_commit= 1`**

之类的设置，而slave则不需要这么高的数据安全，完全可以讲`sync_binlog`设置为`0`或者关闭`binlog`，`innodb_flushlog`也可以设置为`0`来提高`sql`的执行效率。另外就是使用比主库更好的硬件设备作为`slave`。


**4. .如何重置mysql root密码？**

* **在已知MYSQL数据库的ROOT用户密码的情况下，修改密码的方法：**
	* 1、 在SHELL环境下，使用`mysqladmin`命令设置：`mysqladmin –u root –p password ”新密码“` 回车后要求输入旧密码
	* 2、 在mysql 环境中,使用`update`命令，直接更新`mysq`l库`user`表的数据：
		* `Update mysql.user set password=password(‘新密码’) where user=’root’;`
		* `flush privileges;`
		* 注意：mysql语句要以分号”；”结束
	* 3、 在mysql>环境中，使用grant命令，修改root用户的授权权限。
		* `grant all on *.* to root@’localhost’ identified by ‘新密码’； `
 
 * **如查忘记了mysql数据库的ROOT用户的密码，又如何做呢？方法如下：**
 	* 1、 关闭当前运行的mysqld服务程序：`service mysqld stop（要先将mysqld添加为系统服务）`
 	* 2、 使用`mysqld_safe`脚本以安全模式（不加载授权表）启动mysqld 服务
 		* `/usr/local/mysql/bin/mysqld_safe --skip-grant-table &`
 	* 3、 使用空密码的root用户登录数据库，重新设置ROOT用户的密码

```
＃mysql -u root
Mysql> Update mysql.user set password=password(‘新密码’) where user=’root’;
Mysql> flush privileges;
```


**5. mysql数据备份工具**

**mysqldump工具**

Mysqldump是mysql自带的备份工具，目录在bin目录下面：`/usr/local/mysql/bin/mysqldump`，支持基于innodb的热备份。但是由于是逻辑备份，所以速度不是很快，适合备份数据比较小的场景。Mysqldump完全备份+二进制日志可以实现基于时间点的恢复。

**基于LVM快照备份**

在物理备份中，有基于文件系统的物理备份（LVM的快照），也可以直接用tar之类的命令对整个数据库目录进行打包备份，但是这些只能进行泠备份，不同的存储引擎备份的也不一样，myisam自动备份到表级别，而innodb不开启独立表空间的话只能备份整个数据库。

**tar包备份**

**percona提供的xtrabackup工具**

支持innodb的物理热备份，支持完全备份，增量备份，而且速度非常快，支持innodb存储引起的数据在不同数据库之间迁移，支持复制模式下的从机备份恢复备份恢复，为了让xtrabackup支持更多的功能扩展，可以设立独立表空间，打开 `innodb_file_per_table`功能，启用之后可以支持单独的表备份。



## **网络**

**1. osi七层模型，tcp三次握手过程，tcp连接断开过程，什么情况下tcp进入`time_wait`?**

当关闭一个 socket 连接时，主动关闭一端的 socket 将进入`TIME_WAIT`状态，而被动关闭一方则转入`CLOSED`状态。

具体过程如下：

* 1、 客户端发送FIN报文段，进入`FIN_WAIT_1`状态。
* 2、 服务器端收到FIN报文段，发送ACK表示确认，进入`CLOSE_WAIT`状态。
* 3、 客户端收到FIN的确认报文段，进入`FIN_WAIT_2`状态。
* 4、服务器端发送FIN报文端，进入`LAST_ACK`状态。
* 5、 客户端收到FIN报文端，发送FIN的ACK，同时进入`TIME_WAIT`状态，启动TIME_WAIT定时器，超时时间设为2MSL。
* 6、 服务器端收到FIN的ACK，进入CLOSED状态。
* 7、客户端在2MSL时间内没收到对端的任何响应，`TIME_WAIT`超时，进入CLOSED状态。


**2 什么是跨站脚本攻击，有何危害，sql注入攻击如何防范？**

一般说来，在Web安全领域，常见的攻击方式大概有以下几种：

* SQL注入攻击(程序命令和用户数据（即用户输入）之间没有做到泾渭分明。这使得攻击者有机会将程序命令当作用户输入的数据提交给We程序，以发号施令，为所欲为)
* 采用预编译语句集，它内置了处理SQL注入的能力，只要使用它的setXXX方法传值即可。2使用正则表达式过滤传入的参数3字符串过滤4.jsp中调用该函数检查是否包函非法字符5.JSP页面判断代码：
* 跨站脚本攻击 – XSS(利用网站程序对用户输入过滤不足，输入可以显示在页面上对其他用户造成影响的HTML代码，从而盗取用户资料、利用用户身份进行某种动作或者对访问者进行病毒侵害的一种攻击方式)
* 跨站伪造请求攻击 - CSRF
* 文件上传漏洞攻击
* 分布式拒绝服务攻击 - DDOS

**3 lvs/nginx/haproxy优缺点**




## **存储**

答：**使用分布式存储，如mfs、hadoop等**