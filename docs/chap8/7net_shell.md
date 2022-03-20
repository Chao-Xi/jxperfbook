# **7 网络+数据库 Shell 脚本**

### **1、 编写脚本测试 192.168.4.0/24 整个网段中哪些主机处于开机状态,哪些主机处于关机状态(for 版本)**

```
#!/bin/bash

# 编写脚本测试 192.168.4.0/24 整个网段中哪些主机处于开机状态,哪些主机处于关机
# 状态(for 版本)
for i in {1..254}
do
  # 每隔0.3秒ping一次，一共ping2次，并以1毫秒为单位设置ping的超时时间
     ping ‐c 2 ‐i 0.3 ‐W 1 192.168.4.$i  &>/dev/null
    if  [ $? -eq 0 ];then
         echo "192.168.4.$i is up"
     else
         echo  "192.168.4.$i is down"
     fi
done
```

### **2、编写脚本测试 192.168.4.0/24 整个网段中哪些主机处于开机状态,哪些主机处于关机状态(while 版本)**

```
#!/bin/bash

# 编写脚本测试 192.168.4.0/24 整个网段中哪些主机处于开机状态,哪些主机处于关机
# 状态(while 版本) 
i=1
while [ $i -le 254 ]
do
     ping ‐c 2 ‐i 0.3 ‐W 1 192.168.4.$i  &>/dev/null
     if  [ $? -eq 0 ];then
         echo "192.168.4.$i is up"
    else
         echo  "192.168.4.$i is down"
     fi
     let i++
done
```

### **3 编写脚本测试 192.168.4.0/24 整个网段中哪些主机处于开机状态,哪些主机处于关机状态(多进程版)**

```
#!/bin/bash

# 编写脚本测试 192.168.4.0/24 整个网段中哪些主机处于开机状态,哪些主机处于关机
# 状态(多进程版)
 
#定义一个函数,ping 某一台主机,并检测主机的存活状态
myping(){
ping ‐c 2 ‐i 0.3 ‐W 1 $1  &>/dev/null
if  [ $? -eq 0 ];then
  echo "$1 is up"
else
  echo "$1 is down"
fi
}
for i in {1..254}
do
     myping 192.168.4.$i &
done
# 使用&符号,将执行的函数放入后台执行
# 这样做的好处是不需要等待ping第一台主机的回应,就可以继续并发ping第二台主机,依次类推。
```

### **4、使用死循环实时显示 eth0 网卡发送的数据包流量**

```
#!/bin/bash

# 使用死循环实时显示 eth0 网卡发送的数据包流量 
 
while :
do
   echo  '本地网卡 eth0 流量信息如下: '
    ifconfig eth0 | grep "RX pack" | awk '{print $5}'
    ifconfig eth0 | grep "TX pack" | awk '{print $5}'
     sleep 1
done
```

### **5 查看有多少远程的 IP 在连接本机**

```
#!/bin/bash

# 查看有多少远程的 IP 在连接本机(不管是通过 ssh 还是 web 还是 ftp 都统计) 
 
# 使用 netstat ‐atn 可以查看本机所有连接的状态,‐a 查看所有,
# -t仅显示 tcp 连接的信息,‐n 数字格式显示
# Local Address(第四列是本机的 IP 和端口信息)
# Foreign Address(第五列是远程主机的 IP 和端口信息)
# 使用 awk 命令仅显示第 5 列数据,再显示第 1 列 IP 地址的信息
# sort 可以按数字大小排序,最后使用 uniq 将多余重复的删除,并统计重复的次数
netstat -atn  |  awk  '{print $5}'  | awk  '{print $1}' | sort -nr  |  uniq -c
```

### **6 统计 13:30 到 14:30 所有访问 apache 服务器的请求有多少个**

```
#!/bin/bash

# 统计 13:30 到 14:30 所有访问 apache 服务器的请求有多少个
 
# awk 使用‐F 选项指定文件内容的分隔符是/或者:
# 条件判断$7:$8 大于等于 13:30,并且要求,$7:$8 小于等于 14:30
# 最后使用 wc ‐l 统计这样的数据有多少行,即多少个
awk -F "[ /:]" '$7":"$8>="13:30" && $7":"$8<="14:30"' /var/log/httpd/access_log |wc -l
```

### **7 统计 13:30 到 14:30 所有访问本机 Aapche 服务器的远程 IP 地址是什么** 

```
#!/bin/bash

# 统计 13:30 到 14:30 所有访问本机 Aapche 服务器的远程 IP 地址是什么 
# awk 使用‐F 选项指定文件内容的分隔符是/或者:
# 条件判断$7:$8 大于等于 13:30,并且要求,$7:$8 小于等于 14:30
# 日志文档内容里面,第 1 列是远程主机的 IP 地址,使用 awk 单独显示第 1 列即可
awk -F "[ /:]" '$7":"$8>="13:30" && $7":"$8<="14:30"{print $1}' /var/log/httpd/access_log
```


### **8 统计每个远程 IP 访问了本机 apache 几次?**

```
#!/bin/bash

# 统计每个远程 IP 访问了本机 apache 几次? 
awk  '{ip[$1]++}END{for(i in ip){print ip[i],i}}'  /var/log/httpd/access_log
```

### **9 检测 MySQL 数据库连接数量**

```
#!/bin/bash

# 检测 MySQL 数据库连接数量 
 
# 本脚本每 2 秒检测一次 MySQL 并发连接数,可以将本脚本设置为开机启动脚本,或在特定时间段执行
# 以满足对 MySQL 数据库的监控需求,查看 MySQL 连接是否正常
# 本案例中的用户名和密码需要根据实际情况修改后方可使用
log_file=/var/log/mysql_count.log
user=root
passwd=123456
while :
do
    sleep 2
    count=`mysqladmin  -u  "$user"  -p  "$passwd"   status |  awk '{print $4}'`
    echo "`date +%Y‐%m‐%d` 并发连接数为:$count" >> $log_file
done
```

### **10 检测 MySQL 服务是否存活**

```
#!/bin/bash

# 检测 MySQL 服务是否存活 
 
# host 为你需要检测的 MySQL 主机的 IP 地址,user 为 MySQL 账户名,passwd 为密码
# 这些信息需要根据实际情况修改后方可使用
host=192.168.51.198
user=root
passwd=123456
mysqladmin -h '$host' -u '$user' -p'$passwd' ping &>/dev/null
if [ $? -eq 0 ]
then
        echo "MySQL is UP"
else
        echo "MySQL is down"
fi
```

### **11 备份 MySQL 的 shell 脚本(mysqldump版本)**

```

#!/bin/bash

# 备份 MySQL 的 shell 脚本(mysqldump版本) 
 
# 定义变量 user(数据库用户名),passwd(数据库密码),date(备份的时间标签)
# dbname(需要备份的数据库名称,根据实际需求需要修改该变量的值,默认备份 mysql 数据库)
 
user=root
passwd=123456
dbname=mysql
date=$(date +%Y%m%d)
 
# 测试备份目录是否存在,不存在则自动创建该目录
[ ! -d /mysqlbackup ] && mkdir /mysqlbackup
# 使用 mysqldump 命令备份数据库
mysqldump -u "$user" -p "$passwd" "$dbname" > /mysqlbackup/"$dbname"-${date}.sql
```


### **12 监控 HTTP 服务器的状态(测试返回码)**

```
#!/bin/bash

# 监控 HTTP 服务器的状态(测试返回码)
 
# 设置变量,url为你需要检测的目标网站的网址(IP 或域名),比如百度
url=http://http://183.232.231.172/index.html
 
# 定义函数 check_http:
# 使用 curl 命令检查 http 服务器的状态
# ‐m 设置curl不管访问成功或失败,最大消耗的时间为 5 秒,5 秒连接服务为相应则视为无法连接
# ‐s 设置静默连接,不显示连接时的连接速度、时间消耗等信息
# ‐o 将 curl 下载的页面内容导出到/dev/null(默认会在屏幕显示页面内容)
# ‐w 设置curl命令需要显示的内容%{http_code},指定curl返回服务器的状态码
check_http()
{
        status_code=$(curl -m 5 -s -o /dev/null -w %{http_code} $url)
}
 
while :
do
        check_http
        date=$(date +%Y%m%d‐%H:%M:%S)
 
# 生成报警邮件的内容
        echo "当前时间为:$date
        $url 服务器异常,状态码为${status_code}.
        请尽快排查异常." > /tmp/http$$.pid
 
# 指定测试服务器状态的函数,并根据返回码决定是发送邮件报警还是将正常信息写入日志
        if [ $status_code -ne 200 ];then
                mail -s Warning root < /tmp/http$$.pid
        else
                echo "$url 连接正常" >> /var/log/http.log
        fi
        sleep 5
done
```

### **13 自动添加防火墙规则,开启某些服务或端口(适用于 RHEL7)**

```
#!/bin/bash

# 自动添加防火墙规则,开启某些服务或端口(适用于 RHEL7)
# 
# 设置变量定义需要添加到防火墙规则的服务和端口号
# 使用 firewall‐cmd ‐‐get‐services 可以查看 firewall 支持哪些服务
service="nfs http ssh"
port="80 22 8080"
 
# 循环将每个服务添加到防火墙规则中
for i in $service
do
    echo "Adding $i service to firewall"
    firewall‐cmd  --add-service=${i}
done
 
#循环将每个端口添加到防火墙规则中
for i in $port
do
    echo "Adding $i Port to firewall"
    firewall‐cmd --add-port=${i}/tcp
done
#将以上设置的临时防火墙规则,转换为永久有效的规则(确保重启后有效)
firewall‐cmd  --runtime-to-permanent
```


### **14 批量下载有序文件(pdf、图片、视频等等)**

```

#!/bin/bash

# 批量下载有序文件(pdf、图片、视频等等)
 
# 本脚本准备有序的网络资料进行批量下载操作(如 01.jpg,02.jpg,03.jpg)
# 设置资源来源的域名连接
url="http://www.baidu.com/"
echo  "开始下载..."
sleep 2
type=jpg
for i in `seq 100`
     echo "正在下载$i.$type"
  curl $url/$i.$type -o /tmp/${i}$type
     sleep 1
done
#curl 使用-o 选项指定下载文件另存到哪里.
```

### **15 循环关闭局域网中所有主机**

```
#!/bin/bash

# 循环关闭局域网中所有主机 
 
# 假设本机为 192.168.4.100,编写脚本关闭除自己外的其他所有主机
# 脚本执行,需要提前给所有其他主机传递 ssh 密钥,满足无密码连接
for i in {1..254}
do
  [ $i -eq 100 ] && continue
  echo "正在关闭 192.168.4.$i..."
  ssh 192.168.4.$i poweroff
done
```

### **16 获取本机 MAC 地址**

```
#!/bin/bash

# 获取本机 MAC 地址
ip a s | awk 'BEGIN{print  " 本 机 MAC 地 址 信 息 如 下 :"}/^[0‐9]/{print $2;getline;if($0~/link\/ether/){print $2}}' | grep -v lo:
 
# awk 读取 ip 命令的输出,输出结果中如果有以数字开始的行,先显示该行的地 2 列(网卡名称),
# 接着使用 getline 再读取它的下一行数据,判断是否包含 link/ether
# 如果保护该关键词,就显示该行的第 2 列(MAC 地址)
# lo 回环设备没有 MAC,因此将其屏蔽,不显示
```

### **17 显示本机 Linux 系统上所有开放的端口列表**

```

#!/bin/bash

# 显示本机 Linux 系统上所有开放的端口列表 
 
# 从端口列表中观测有没有没用的端口,有的话可以将该端口对应的服务关闭,防止意外的攻击可能性
ss -nutlp | awk '{print $1,$5}' | awk -F"[: ]" '{print "协议:"$1,"端口号:"$NF}' | grep "[0‐9]" | uniq
```



### **18 查看 KVM 虚拟机中的网卡信息(不需要进入启动或进入虚拟机)**

```
#!/bin/bash

# 查看 KVM 虚拟机中的网卡信息(不需要进入启动或进入虚拟机) 
 
# 该脚本使用 guestmount 工具,可以将虚拟机的磁盘系统挂载到真实机文件系统中
# Centos7.2 中安装 libguestfs‐tools‐c 可以获得 guestmount 工具
# 虚拟机可以启动或者不启动都不影响该脚本的使用
# 将虚拟机磁盘文件挂载到文件系统后,就可以直接读取磁盘文件中的网卡配置文件中的数据
clear
mountpoint="/media/virtimage"
[ ! -d $mountpoint ] && mkdir $mountpoint
read -p "输入虚拟机名称:" name
echo "请稍后..."
# 如果有设备挂载到该挂载点,则先 umount 卸载
if mount | grep -q "$mountpoint" ;then
  umount $mountpoint
fi
# 只读的方式,将虚拟机的磁盘文件挂载到特定的目录下,这里是/media/virtimage 目录
guestmount -r -d $name -i $mountpoint
echo
echo "‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐"
echo -e "\033[32m$name 虚拟机中网卡列表如下:\033[0m"
dev=$(ls /media/virtimage/etc/sysconfig/network‐scripts/ifcfg-* |awk -F"[/‐]" '{print $9}')
echo $dev
echo "‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐"
echo
echo
echo "+++++++++++++++++++++++++++++++++++++++++++"
echo -e "\033[32m 网卡 IP 地址信息如下:\033[0m"
for i in $dev
do
  echo -n "$i:"
  grep -q "IPADDR" /media/virtimage/etc/sysconfig/network‐scripts/ifcfg-$i || echo "未配置 IP地址"
  awk -F= '/IPADDR/{print $2}' /media/virtimage/etc/sysconfig/network-scripts/ifcfg-$i
done
echo "+++++++++++++++++++++++++++++++++++++++++++"
```

### **19 不登陆虚拟机,修改虚拟机网卡 IP 地址**

```
#!/bin/bash

# 不登陆虚拟机,修改虚拟机网卡 IP 地址 
 
# 该脚本使用 guestmount 工具,Centos7.2 中安装 libguestfs‐tools‐c 可以获得 guestmount 工具
# 脚本在不登陆虚拟机的情况下,修改虚拟机的 IP 地址信息
# 在某些环境下,虚拟机没有 IP 或 IP 地址与真实主机不在一个网段
# 真实主机在没有 virt‐manger 图形的情况下,远程连接虚拟机很麻烦
# 该脚本可以解决类似的问题
read -p "请输入虚拟机名称:" name
if virsh domstate $name | grep -q running ;then
  echo "修改虚拟机网卡数据,需要关闭虚拟机"
  virsh destroy $name
fi
mountpoint="/media/virtimage"
[ ! -d $mountpoint ] && mkdir $mountpoint
echo "请稍后..."
if mount | grep -q "$mountpoint" ;then
  umount $mountpoint
fi
guestmount  -d $name -i $mountpoint
read -p "请输入需要修改的网卡名称:" dev
read -p "请输入 IP 地址:" addr
# 判断原本网卡配置文件中是否有 IP 地址,有就修改该 IP,没有就添加一个新的 IP 地址
if grep -q "IPADDR"  $mountpoint/etc/sysconfig/network‐scripts/ifcfg‐$dev ;then
  sed -i "/IPADDR/s/=.*/=$addr/"  $mountpoint/etc/sysconfig/network‐scripts/ifcfg‐$dev
else
  echo "IPADDR=$addr" >> $mountpoint/etc/sysconfig/network‐scripts/ifcfg‐$dev
fi
# 如果网卡配置文件中有客户配置的 IP 地址,则脚本提示修改 IP 完成
awk -F= -v x=$addr '$2==x{print "完成..."}'  $mountpoint/etc/sysconfig/network‐scripts/ifcfg-$dev
```