# **4 基本 shell 脚本 系统+文件+卷+时间**

### **1 编写hello world脚本**

```
#!/bin/bash

# 编写hello world脚本
 
echo "Hello World!"
```

### **2、通过位置变量创建 Linux 系统账户及密码**

```
#!/bin/bash

# 通过位置变量创建 Linux 系统账户及密码
 
#$1 是执行脚本的第一个参数,$2 是执行脚本的第二个参数
useradd    "$1" 
echo "$2"  |  passwd  ‐‐stdin  "$1"
```

### **3、备份日志**

```
#!/bin/bash
# 每周 5 使用 tar 命令备份/var/log 下的所有日志文件
# vim  /root/logbak.sh
# 编写备份脚本,备份后的文件名包含日期标签,防止后面的备份将前面的备份数据覆盖
# 注意 date 命令需要使用反引号括起来,反引号在键盘<tab>键上面
tar  -czf  log-`date +%Y%m%d`.tar.gz  /var/log 
 
# crontab ‐e  #编写计划任务,执行备份脚本
00  03  *  *  5  /root/logbak.sh
```

### **4 猜数字游戏**

```
#!/bin/bash

# 脚本生成一个 100 以内的随机数,提示用户猜数字,根据用户的输入,提示用户猜对了,
# 猜小了或猜大了,直至用户猜对脚本结束。
 
# RANDOM 为系统自带的系统变量,值为 0‐32767的随机数
# 使用取余算法将随机数变为 1‐100 的随机数
num=$[RANDOM%100+1]
echo "$num"
 
# 使用 read 提示用户猜数字
# 使用 if 判断用户猜数字的大小关系:‐eq(等于),‐ne(不等于),‐gt(大于),‐ge(大于等于),
# ‐lt(小于),‐le(小于等于)
while  :
do
  read -p "计算机生成了一个 1‐100 的随机数,你猜: " cai
    if [ $cai -eq $num ]
    then
         echo "恭喜,猜对了"
         exit
      elif [ $cai -gt $num ]
      then
             echo "Oops,猜大了"
        else
             echo "Oops,猜小了"
   fi
done
```


### **5 编写脚本:提示用户输入用户名和密码,脚本自动创建相应的账户及配置密码。如果用户不输入账户名,则提示必须输入账户名并退出脚本;如果用户不输入密码,则统一使用默认的 123456 作为默认密码。**

```
#!/bin/bash

# 编写脚本:提示用户输入用户名和密码,脚本自动创建相应的账户及配置密码。如果用户
# 不输入账户名,则提示必须输入账户名并退出脚本;如果用户不输入密码,则统一使用默
# 认的 123456 作为默认密码。
 
read -p "请输入用户名: " user
#使用‐z 可以判断一个变量是否为空,如果为空,提示用户必须输入账户名,并退出脚本,退出码为 2
#没有输入用户名脚本退出后,使用$?查看的返回码为 2
if [ -z $user ];then
     echo "您不需输入账户名"
   exit 2
fi
#使用 stty ‐echo 关闭 shell 的回显功能
#使用 stty  echo 打开 shell 的回显功能
stty -echo
read -p "请输入密码: " pass
stty echo
pass=${pass:‐123456}
useradd "$user"
echo "$pass" | passwd ‐‐stdin "$user"
```

### **6 输入三个数并进行升序排序**

```
#!/bin/bash

# 依次提示用户输入 3 个整数,脚本根据数字大小依次排序输出 3 个数字
read -p "请输入一个整数:" num1
read -p "请输入一个整数:" num2
read -p "请输入一个整数:" num3
# 不管谁大谁小,最后都打印 echo "$num1,$num2,$num3"
# num1 中永远存最小的值,num2 中永远存中间值,num3 永远存最大值
# 如果输入的不是这样的顺序,则改变数的存储顺序,如:可以将 num1 和 num2 的值对调
tmp=0
# 如果 num1 大于 num2,就把 num1 和和 num2 的值对调,确保 num1 变量中存的是最小值
if [ $num1 -gt $num2 ];then   
  tmp=$num1
  num1=$num2
  num2=$tmp
fi
# 如果 num1 大于 num3,就把 num1 和 num3 对调,确保 num1 变量中存的是最小值
if [ $num1 -gt $num3 ];then   
    tmp=$num1
    num1=$num3
    num3=$tmp
fi
# 如果 num2 大于 num3,就把 num2 和 num3 对标,确保 num2 变量中存的是小一点的值
if [ $num2 -gt $num3 ];then
    tmp=$num2
    num2=$num3
    num3=$tmp
fi
echo "排序后数据(从小到大)为:$num1,$num2,$num3"
```

### **7 石头、剪刀、布游戏**

```
#!/bin/bash

# 编写脚本,实现人机<石头,剪刀,布>游戏
game=(石头 剪刀 布)
num=$[RANDOM%3]
computer=${game[$num]}
# 通过随机数获取计算机的出拳
# 出拳的可能性保存在一个数组中,game[0],game[1],game[2]分别是 3 中不同的可能
 
echo "请根据下列提示选择您的出拳手势"
echo "1.石头"
echo "2.剪刀"
echo "3.布"
 
read -p "请选择 1‐3:" person
case  $person  in
1)
  if [ $num -eq 0 ]
  then
    echo "平局"
    elif [ $num -eq 1 ]
    then
      echo "你赢"
  else
    echo "计算机赢"
  fi;;
2)   
  if [ $num -eq 0 ]
  then
    echo "计算机赢"
    elif [ $num -eq 1 ]
    then
      echo "平局"
  else
    echo "你赢"
  fi;;
3)
  if [ $num -eq 0 ]
  then
    echo "你赢"
    elif [ $num -eq 1 ]
    then
      echo "计算机赢"
  else
    echo "平局"
  fi;;
*)
  echo "必须输入 1‐3 的数字"
esac
```


### **8 编写脚本,显示进度条**

```

#!/bin/bash

# 编写脚本,显示进度条
jindu(){
while :
do
     echo -n '#'
     sleep 0.2
done
}
jindu &
cp -a $1 $2
killall $0
echo "拷贝完成"
```

### **9、进度条,动态时针版本；定义一个显示进度的函数,屏幕快速显示`|  / ‐ \`**

```
#!/bin/bash

# 进度条,动态时针版本
# 定义一个显示进度的函数,屏幕快速显示|  / ‐ \
rotate_line(){
INTERVAL=0.5  #设置间隔时间
COUNT="0"     #设置4个形状的编号,默认编号为 0(不代表任何图像)
while :
do
  COUNT=`expr $COUNT + 1` #执行循环,COUNT 每次循环加 1,(分别代表4种不同的形状)
  case $COUNT in          #判断 COUNT 的值,值不一样显示的形状就不一样
  "1")                    #值为 1 显示‐
          echo -e '‐'"\b\c"
          sleep $INTERVAL
          ;;
    "2")                  #值为 2 显示\\,第一个\是转义
          echo -e '\\'"\b\c"
          sleep $INTERVAL
          ;;
    "3")                  #值为 3 显示|
          echo -e "|\b\c"
          sleep $INTERVAL
          ;;
   "4")                   #值为 4 显示/
          echo -e "/\b\c"
          sleep $INTERVAL
          ;;
    *)                    #值为其他时,将 COUNT 重置为 0
          COUNT="0";;
    esac
done
}
rotate_line
```

### **10 `9*9` 乘法表**

```
#!/bin/bash

# 9*9 乘法表(编写 shell 脚本,打印 9*9 乘法表) 
for i in `seq 9`
do
    for j in `seq $i`
     do
         echo -n "$j*$i=$[i*j]  "
     done
    echo
done
```

### **11、使用 user.txt 文件中的人员名单,在计算机中自动创建对应的账户并配置初始密码本脚本执行,需要提前准备一个 user.txt 文件,该文件中包含有若干用户名信息**

```

#!/bin/bash

# 使用 user.txt 文件中的人员名单,在计算机中自动创建对应的账户并配置初始密码
# 本脚本执行,需要提前准备一个 user.txt 文件,该文件中包含有若干用户名信息
for i in `cat user.txt`
do
     useradd  $i
     echo "123456" | passwd ‐‐stdin $i
done
```

### **12、编写批量修改扩展名脚本**

```
#!/bin/bash

# 编写批量修改扩展名脚本,如批量将 txt 文件修改为 doc 文件 
# 执行脚本时,需要给脚本添加位置参数
# 脚本名  txt  doc(可以将 txt 的扩展名修改为 doc)
# 脚本名  doc  jpg(可以将 doc 的扩展名修改为 jpg)
 
for i in `ls *.$1`
do
     mv $i ${i%.*}.$2
done
```


### **13、点名器脚本**

```
#!/bin/bash

# 编写一个点名器脚本
 
# 该脚本,需要提前准备一个 user.txt 文件
# 该文件中需要包含所有姓名的信息,一行一个姓名,脚本每次随机显示一个姓名
while :
do
#统计 user 文件中有多少用户
line=`cat user.txt |wc ‐l`
num=$[RANDOM%line+1]
sed -n "${num}p"  user.txt
sleep 0.2
clear
done
```

### **14 对 100 以内的所有正整数相加求和(1+2+3+4...+100)**

```

#!/bin/bash

# 对 100 以内的所有正整数相加求和(1+2+3+4...+100)
 
#seq 100 可以快速自动生成 100 个整数
sum=0
for i in `seq 100`
do
    sum=$[sum+i]
done
echo "总和是:$sum"
```

### **15 打印国际象棋棋盘**

```

#!/bin/bash

# 打印国际象棋棋盘
# 设置两个变量,i 和 j,一个代表行,一个代表列,国际象棋为 8*8 棋盘
# i=1 是代表准备打印第一行棋盘,第 1 行棋盘有灰色和蓝色间隔输出,总共为 8 列
# i=1,j=1 代表第 1 行的第 1 列;i=2,j=3 代表第 2 行的第 3 列
# 棋盘的规律是 i+j 如果是偶数,就打印蓝色色块,如果是奇数就打印灰色色块
# 使用 echo ‐ne 打印色块,并且打印完成色块后不自动换行,在同一行继续输出其他色块
for i in {1..8}
do
    for j in {1..8}
    do
      sum=$[i+j]
    if [  $[sum%2] -eq 0 ];then
       echo -ne "\033[46m  \033[0m"
    else
      echo -ne "\033[47m  \033[0m"
    fi
    done
    echo
done
```


### **16 统计当前 Linux 系统中可以登录计算机的账户有多少个**

```

#!/bin/bash

# 统计当前 Linux 系统中可以登录计算机的账户有多少个
#方法 1:
grep "bash$" /etc/passwd | wc -l
#方法 2:
awk -f: '/bash$/{x++}end{print x}'  /etc/passwd
```


### **17 统计`/var/log` 有多少个文件,并显示这些文件名**

```
#!/bin/bash

# 统计/var/log 有多少个文件,并显示这些文件名 
# 使用 ls 递归显示所有,再判断是否为文件,如果是文件则计数器加 1
cd  /var/log
sum=0
for i in `ls -r *`
do
   if [ -f $i ];then
       let sum++
         echo "文件名:$i"
     fi
done
echo "总文件数量为:$sum"
```

### **18 自动为其他脚本添加解释器信息**

```
#!/bin/bash

# 自动为其他脚本添加解释器信息#!/bin/bash,如脚本名为 test.sh 则效果如下: 
# ./test.sh  abc.sh  自动为 abc.sh 添加解释器信息
# ./test.sh  user.sh  自动为 user.sh 添加解释器信息
 
# 先使用 grep 判断对象脚本是否已经有解释器信息,如果没有则使用 sed 添加解释器以及描述信息
if  !  grep  -q  "^#!"  $1; then
sed  '1i #!/bin/bash'  $1
sed  '2i #Description: '
fi
# 因为每个脚本的功能不同,作用不同,所以在给对象脚本添加完解释器信息,以及 Description 后还希望
# 继续编辑具体的脚本功能的描述信息,这里直接使用 vim 把对象脚本打开,并且光标跳转到该文件的第 2 行
vim +2 $1
```

### **19 自动对磁盘分区、格式化、挂载**

```
 #!/bin/bash
 
# 自动对磁盘分区、格式化、挂载
# 对虚拟机的 vdb 磁盘进行分区格式化,使用<<将需要的分区指令导入给程序 fdisk
# n(新建分区),p(创建主分区),1(分区编号为 1),两个空白行(两个回车,相当于将整个磁盘分一个区)
# 注意:1 后面的两个回车(空白行)是必须的!
fdisk /dev/vdb << EOF
n
p
1
 
 
wq
EOF
 
#格式化刚刚创建好的分区
mkfs.xfs   /dev/vdb1
 
#创建挂载点目录
if [ -e /data ]; then
exit
fi
mkdir /data
 
#自动挂载刚刚创建的分区,并设置开机自动挂载该分区
echo '/dev/vdb1     /data    xfs    defaults        1 2'  >> /etc/fstab
mount -a
```

### **20 自动优化 Linux 内核参数**

```
#!/bin/bash

# 自动优化 Linux 内核参数
 
#脚本针对 RHEL7
cat >> /usr/lib/sysctl.d/00‐system.conf <<EOF
fs.file‐max=65535
net.ipv4.tcp_timestamps = 0
net.ipv4.tcp_synack_retries = 5
net.ipv4.tcp_syn_retries = 5
net.ipv4.tcp_tw_recycle = 1
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_fin_timeout = 30
#net.ipv4.tcp_keepalive_time = 120
net.ipv4.ip_local_port_range = 1024  65535
kernel.shmall = 2097152
kernel.shmmax = 2147483648
kernel.shmmni = 4096
kernel.sem = 5010 641280 5010 128
net.core.wmem_default=262144
net.core.wmem_max=262144
net.core.rmem_default=4194304
net.core.rmem_max=4194304
net.ipv4.tcp_fin_timeout = 10
net.ipv4.tcp_keepalive_time = 30
net.ipv4.tcp_window_scaling = 0
net.ipv4.tcp_sack = 0
EOF
 
sysctl –p
```

### **21 将文件中所有的小写字母转换为大写字母**

```

#!/bin/bash

# 将文件中所有的小写字母转换为大写字母 
 
# $1是位置参数,是你需要转换大小写字母的文件名称
# 执行脚本,给定一个文件名作为参数,脚本就会将该文件中所有的小写字母转换为大写字母
tr "[a‐z]" "[A‐Z]" < $1
```

### **22 使用脚本自动创建逻辑卷**

```

#!/bin/bash

# 使用脚本自动创建逻辑卷 
 
# 清屏,显示警告信息,创建将磁盘转换为逻辑卷会删除数据
clear
echo -e "\033[32m           !!!!!!警告(Warning)!!!!!!\033[0m"
echo
echo "+++++++++++++++++++++++++++++++++++++++++++++++++"
echo "脚本会将整个磁盘转换为 PV,并删除磁盘上所有数据!!!"
echo "This Script will destroy all data on the Disk"
echo "+++++++++++++++++++++++++++++++++++++++++++++++++"
echo
read -p "请问是否继续 y/n?:" sure
 
# 测试用户输入的是否为 y,如果不是则退出脚本
[ $sure != y ] && exit
 
# 提示用户输入相关参数(磁盘、卷组名称等数据),并测试用户是否输入了这些值,如果没有输入,则脚本退出
read -p "请输入磁盘名称,如/dev/vdb:" disk
[ -z $disk ] && echo "没有输入磁盘名称" && exit
read -p "请输入卷组名称:" vg_name
[ -z $vg_name ] && echo "没有输入卷组名称" && exit
read -p "请输入逻辑卷名称:" lv_name
[ -z $lv_name ] && echo "没有输入逻辑卷名称" && exit
read -p "请输入逻辑卷大小:" lv_size
[ -z $lv_size ] && echo "没有输入逻辑卷大小" && exit
 
# 使用命令创建逻辑卷
pvcreate $disk
vgcreate $vg_name $disk
lvcreate -L ${lv_size}M -n ${lv_name}  ${vg_name}
```

### **23 显示 CPU 厂商信息**

```
#!/bin/bash

# 显示 CPU 厂商信息 
awk '/vendor_id/{print $3}' /proc/cpuinfo | uniq
```

### **24 删除某个目录下大小为 0 的文件**

```

#!/bin/bash

# 删除某个目录下大小为 0 的文件
 
#/var/www/html 为测试目录,脚本会清空该目录下所有 0 字节的文件
dir="/var/www/html"
find $dir -type f -size 0 -exec rm -rf {} \;
```

### **25 查找 Linux 系统中的僵尸进程**

```
#!/bin/bash

# 查找 Linux 系统中的僵尸进程
 
# awk 判断 ps 命令输出的第 8 列为 Z 是,显示该进程的 PID 和进程命令
ps aux | awk '{if($8 == "Z"){print $2,$11}}'
```

### **26 提示用户输入年份后判断该年是否为闰年**

```

#!/bin/bash

# 提示用户输入年份后判断该年是否为闰年
 
# 能被4整除并且并不能被100整除的年份是闰年
# 能被400整除的年份也是闰年
read -p "请输入一个年份:" year
 
if [ "$year" = "" ];then
    echo "没有输入年份"
    exit
fi
#使用正则测试变量 year 中是否包含大小写字母
if [[ "$year" =~ [a‐Z] ]];then
    echo "你输入的不是数字"
    exit
fi
# 判断是否为闰年
if [ $[year % 4] -eq 0 ] && [ $[year % 100] -ne 0 ];then
    echo "$year年是闰年"  
elif [ $[year % 400] -eq 0 ];then
    echo "$year年是闰年"
else
    echo "$year年不是闰年"
fi
```

### **27 Shell 脚本的 fork 炸弹**

```

#!/bin/bash

# Shell 脚本的 fork 炸弹 
 
# 快速消耗计算机资源,致使计算机死机
# 定义函数名为.(点), 函数中递归调用自己并放入后台执行
.() { .|.& };.
```

### **28 显示当前计算机中所有账户的用户名称**

```

 #!/bin/bash
 
# 显示当前计算机中所有账户的用户名称
 
# 下面使用3种不同的方式列出计算机中所有账户的用户名
# 指定以:为分隔符,打印/etc/passwd 文件的第 1 列
awk -F: '{print $1}' /etc/passwd
 
# 指定以:为分隔符,打印/etc/passwd 文件的第 1 列
cut -d: -f1 /etc/passwd
 
# 使用 sed 的替换功能,将/etc/passwd 文件中:后面的所有内容替换为空(仅显示用户名)
sed 's/:.*//' /etc/passwd
```

### **29 制定目录路径,脚本自动将该目录使用 tar 命令打包备份到`/data`目录**

```
#!/bin/bash

# 制定目录路径,脚本自动将该目录使用 tar 命令打包备份到/data目录 
 
[ ! -d /data ] && mkdir /data
[ -z $1 ] && exit
if [ -d $1 ];then
  tar -czf /data/$1.-`date +%Y%m%d`.tar.gz $1
else
    echo "该目录不存在"
fi
```

### **30 显示进度条(回旋镖版)**

```
#!/bin/bash

# 显示进度条(回旋镖版)
 
while :
do
  clear
  for i in {1..20}
  do
    echo ‐e "\033[3;${i}H*"
    sleep 0.1
    done
    clear
    for i in {20..1}
    do
    echo ‐e "\033[3;${i}H*"
    sleep 0.1
    done
    clear
done
```

### **31 修改 Linux 系统的最大打开文件数量**

```

#!/bin/bash

# 修改 Linux 系统的最大打开文件数量 
 
# 往/etc/security/limits.conf 文件的末尾追加两行配置参数,修改最大打开文件数量为 65536
cat >> /etc/security/limits.conf <<EOF
* soft nofile  65536
* hard nofile  65536
EOF
```

### **32 自动修改计划任务配置文件**

```
#!/bin/bash

# 自动修改计划任务配置文件 
 
read -p "请输入分钟信息(00‐59):" min
read -p "请输入小时信息(00‐24):" hour
read -p "请输入日期信息(01‐31):" date
read -p "请输入月份信息(01‐12):" month
read -p "请输入星期信息(00‐06):" weak
read -p "请输入计划任务需要执行的命令或脚本:" program
echo "$min $hour $date $month $weak $program" >> /etc/crontab
```

### **33 使用脚本循环创建三位数字的文本文件(111-999 的文件)**

```

#!/bin/bash

# 使用脚本循环创建三位数字的文本文件(111-999 的文件) 
 
for i in {1..9}
do
  for j in {1..9}
  do
    for k in {1..9}
    do
      touch /tmp/$i$j$k.txt
    done
    done
done
```

### **34、统计 Linux 进程相关数量信息**

```
#!/bin/bash

# 统计 Linux 进程相关数量信息 
 
running=0
sleeping=0
stoped=0
zombie=0
# 在 proc 目录下所有以数字开始的都是当前计算机正在运行的进程的进程 PID
# 每个 PID 编号的目录下记录有该进程相关的信息
for pid in /proc/[1‐9]*
do
  procs=$[procs+1]
  stat=$(awk '{print $3}' $pid/stat)
# 每个 pid 目录下都有一个 stat 文件,该文件的第 3 列是该进程的状态信息
    case $stat in
    R)
    running=$[running+1]
    ;;
    T)
    stoped=$[stoped+1]
    ;;
    S)
    sleeping=$[sleeping+1]
    ;;
    Z)
     zombie=$[zombie+1]
     ;;
    esac
done
echo "进程统计信息如下"
echo "总进程数量为:$procs"
echo "Running 进程数为:$running"
echo "Stoped 进程数为:$stoped"
echo "Sleeping 进程数为:$sleeping"
echo "Zombie 进程数为:$zombie"
```

### **35 从键盘读取一个论坛积分,判断论坛用户等级**

```
#!/bin/bash

# 从键盘读取一个论坛积分,判断论坛用户等级
 
#等级分类如下:
#  大于等于 90        神功绝世
#  大于等于 80,小于 90       登峰造极
#  大于等于 70,小于 80       炉火纯青
#  大于等于 60,小于 70       略有小成
#  小于 60               初学乍练
read -p "请输入积分(0‐100):" JF
if [ $JF -ge 90 ] ; then
  echo "$JF 分,神功绝世"
elif [ $JF -ge 80 ] ; then
    echo "$JF 分,登峰造极"
elif [ $JF -ge 70 ] ; then
    echo "$JF 分,炉火纯青"
elif [ $JF -lt 60 ] ; then
    echo "$JF 分,略有小成"
else
    echo "$JF 分,初学乍练"
fi
```

### **36 判断用户输入的数据类型(字母、数字或其他)**

```
#!/bin/bash

# 判断用户输入的数据类型(字母、数字或其他) 
read -p "请输入一个字符:" KEY
case "$KEY" in
  [a‐z]|[A‐Z])
    echo "字母" 
    ;;
  [0‐9])
    echo "数字" 
    ;;
  *)
    echo "空格、功能键或其他控制字符"
esac
```

### **37 显示进度条(数字版)**

```

#!/bin/bash

# 显示进度条(数字版) 
# echo 使用‐e 选项后,在打印参数中可以指定 H,设置需要打印内容的 x,y 轴的定位坐标
# 设置需要打印内容在第几行,第几列
for i in {1..100}
do
    echo -e "\033[6;8H["
    echo -e "\033[6;9H$i%"
    echo -e "\033[6;13H]"
    sleep 0.1
done
```

### **38 打印斐波那契数列**

```

#!/bin/bash

# 打印斐波那契数列(该数列的特点是后一个数字,永远都是前 2 个数字之和) 
 
# 斐波那契数列后一个数字永远是前 2 个数字之和
# 如:0  1  1  2  3  5  8  13 ... ...
list=(0 1)
for i in `seq 2 11`
do
  list[$i]=`expr ${list[‐1]} + ${list[‐2]}`
done
echo ${list[@]}
```

### **39 判断用户输入的是 Yes 或 NO**

```
#!/bin/bash

# 判断用户输入的是 Yes 或 NO 
 
read -p  "Are you sure?[y/n]:"  sure
case  $sure  in
  y|Y|Yes|YES)  
    echo "you enter $a"
    ;;
    n|N|NO|no)
     echo "you enter $a"
     ;;
    *)
     echo "error";;
esac
```

### **40 将 Linux 系统中 UID 大于等于 1000 的普通用户都删除**

```
#!/bin/bash

# 将 Linux 系统中 UID 大于等于 1000 的普通用户都删除 
 
# 先用 awk 提取所有 uid 大于等于 1000 的普通用户名称
# 再使用 for 循环逐个将每个用户删除即可
user=$(awk -F: '$3>=1000{print $1}' /etc/passwd)
for i in $user
do
     userdel -r $i
done
```

### **41 使用脚本开启关闭虚拟机**

```
#!/bin/bash

# 使用脚本开启关闭虚拟机 
 
# 脚本通过调用virsh命令实现对虚拟机的管理,如果没有该命令,需要安装 libvirt‐client 软件包
# $1是脚本的第1个参数,$2是脚本的第2个参数
# 第1个参数是你希望对虚拟机进行的操作指令,第2个参数是虚拟机名称
case $1 in
  list)
    virsh list --all
    ;;
  start)
    virsh start $2
    ;;
  stop)
    virsh destroy $2
    ;;
  enable)
    virsh autostart $2
    ;;
  disable)
    virsh autostart --disable $2
    ;;
  *)
    echo "Usage:$0 list"
    echo "Usage:$0 [start|stop|enable|disable]  VM_name"
    cat << EOF
    #list      显示虚拟机列表
    #start     启动虚拟机
    #stop      关闭虚拟机
    #enable    设置虚拟机为开机自启
    #disable   关闭虚拟机开机自启功能
    EOF
    ;;
esac
```

### **42 调整虚拟机内存参数的 shell 脚本**

```
#!/bin/bash

# 调整虚拟机内存参数的 shell 脚本 
 
# 脚本通过调用 virsh 命令实现对虚拟机的管理,如果没有该命令,需要安装 libvirt‐client 软件包
cat << EOF
1.调整虚拟机最大内存数值
2.调整实际分配给虚拟机的内存数值
EOF
read -p "请选择[1‐2]:" select
case $select in
  1)
      read -p "请输入虚拟机名称" name
      read -p "请输入最大内存数值(单位:k):" size
      virsh setmaxmem $name --size $size --config
      ;;
  2)
      read -p "请输入虚拟机名称" name
      read -p "请输入实际分配内存数值(单位:k):" size
      virsh setmem $name $size
      ;;
  *)
      echo "Error"
      ;;
esac
```

### **43 破解虚拟机密码,无密码登陆虚拟机系统**

```
#!/bin/bash

# 破解虚拟机密码,无密码登陆虚拟机系统 
 
# 该脚本使用 guestmount 工具,Centos7.2 中安装 libguestfs‐tools‐c 可以获得 guestmount 工具
 
read -p "请输入虚拟机名称:" name
if virsh domstate $name | grep -q running ;then
  echo "破解,需要关闭虚拟机"
  virsh destroy $name
fi
mountpoint="/media/virtimage"
[ ! -d $mountpoint ] && mkdir $mountpoint
echo "请稍后..."
if mount | grep -q "$mountpoint" ;then
  umount $mountpoint
fi
guestmount -d $name -i $mountpoint
# 将 passwd 中密码占位符号 x 删除,该账户即可实现无密码登陆系统
sed -i "/^root/s/x//" $mountpoint/etc/passwd
```

### **44 Shell 脚本对信号的处理,执行脚本后,按键盘 Ctrl+C 无法终止的脚本**

```
#!/bin/bash

# Shell 脚本对信号的处理,执行脚本后,按键盘 Ctrl+C 无法终止的脚本 
 
# 使用 trap 命令可以拦截用户通过键盘或 kill 命令发送过来的信号
# 使用 kill ‐l 可以查看 Linux 系统中所有的信号列表,其中 2 代表 Ctrl+C
# trap 当发现有用户 ctrl+C 希望终端脚本时,就执行 echo "暂停 10s";sleep 10 这两条命令
# 另外用户使用命令:[ kill ‐2 脚本的 PID ] 也可以中断脚本和 Ctrl+C 一样的效果,都会被 trap 拦截
trap 'echo "暂停 10s";sleep 10' 2
while :
do
  echo "go go go"
done
```

### **45 一键配置 VNC 远程桌面服务器(无密码版本)**

```
#!/bin/bash

# 一键配置 VNC 远程桌面服务器(无密码版本)
 
# 脚本配置的 VNC 服务器,客户端无需密码即可连接
# 客户端仅有查看远程桌面的权限,没有鼠标和键盘的操作权限
 
rpm --quiet -q tigervnc‐server
if [  $? -ne  0 ];then
  yum  -y  tigervnc‐server
fi
x0vncserver AcceptKeyEvents=0 AlwaysShared=1 \
AcceptPointerEvents=0 SecurityTypes=None  rfbport=5908
```

### **46 关闭 SELinux**

```
#!/bin/bash

# 关闭 SELinux 
 
sed -i  '/^SELINUX/s/=.*/=disabled/' /etc/selinux/config
setenforce 0
```

### **47 查看所有虚拟机磁盘使用量以及CPU使用量信息**

```
#!/bin/bash

# 查看所有虚拟机磁盘使用量以及CPU使用量信息 
 
virt‐df
read -n1 "按任意键继续" key
virt‐top
```

### **48 使用 shell 脚本打印图形**

```

#!/bin/bash

# 使用 shell 脚本打印如下图形: 
 
# 打印第一组图片
# for(())为类 C 语言的语法格式,也可以使用 for i  in;do  ;done 的格式替换
# for((i=1;i<=9;i++))循环会执行 9 次,i 从 1 开始到 9,每循环一次 i 自加 1
clear
for (( i=1; i<=9; i++ ))
do
  for (( j=1; j<=i; j++ ))
  do
    echo -n "$i"
  done
  echo ""
done
read  -n1  "按任意键继续"  key
#打印第二组图片
clear
for (( i=1; i<=5; i++ )) 
do
  for (( j=1; j<=i; j++ ))
  do
    echo -n " |"
  done
  echo "_ "
done
read  -n1  "按任意键继续"  key
#打印第三组图片
clear
for (( i=1; i<=5; i++ ))
do
  for (( j=1; j<=i; j++ ))
  do
    echo -n " *"
  done
  echo ""
done
for (( i=5; i>=1; i‐‐ ))
do
  for (( j=1; j<=i; j++ ))
  do
    echo -n " *"
  done
  echo ""
done
```

### **49 根据计算机当前时间,返回问候语,可以将该脚本设置为开机启动**

```

#!/bin/bash

# 根据计算机当前时间,返回问候语,可以将该脚本设置为开机启动 
 
# 00‐12 点为早晨,12‐18 点为下午,18‐24 点为晚上
# 使用 date 命令获取时间后,if 判断时间的区间,确定问候语内容
tm=$(date +%H)
if [ $tm -le 12 ];then
  msg="Good Morning $USER"
elif [ $tm -gt 12 -a $tm -le 18 ];then
    msg="Good Afternoon $USER"
else
    msg="Good Night $USER"
fi
echo "当前时间是:$(date +"%Y‐%m‐%d %H:%M:%S")"
echo -e "\033[34m$msg\033[0m"
```

### **50 读取用户输入的账户名称,将账户名写入到数组保存**

```

#!/bin/bash

# 读取用户输入的账户名称,将账户名写入到数组保存 
 
# 定义数组名称为 name,数组的下标为 i,小标从 0 开始,每输入一个账户名,下标加 1,继续存下一个账户
# 最后,输入 over,脚本输出总结性信息后脚本退出
i=0
while :
do
  read -p "请输入账户名,输入 over 结束:" key
  if [ $key == "over" ];then 
    break
    else
    name[$i]=$key
    let i++
    fi
done
echo "总账户名数量:${#name[*]}"
echo "${name[@]}"
```

### **51 判断文件或目录是否存在**

```
#!/bin/bash

# 判断文件或目录是否存在 
 
if [ $# -eq 0 ] ;then
echo "未输入任何参数,请输入参数"
echo "用法:$0 [文件名|目录名]"
fi
if [ -f $1 ];then
  echo "该文件,存在"
  ls -l $1
else
  echo "没有该文件"
fi
if [ -d  $1 ];then
     echo "该目录,存在"
     ls -ld  $2
else
     echo "没有该目录"
fi
```

### **52 打印各种格式的时间**

```
!/bin/bash

# 打印各种时间格式 
 
echo "显示星期简称(如:Sun)"
date +%a
echo "显示星期全称(如:Sunday)"
date +%A
echo "显示月份简称(如:Jan)"
date +%b
echo "显示月份全称(如:January)"
date +%B
echo "显示数字月份(如:12)"
date +%m
echo "显示数字日期(如:01 号)"
date +%d
echo "显示数字年(如:01 号)"
date +%Y echo "显示年‐月‐日"
date +%F
echo "显示小时(24 小时制)"
date +%H
echo "显示分钟(00..59)"
date +%M
echo "显示秒"
date +%S
echo "显示纳秒"
date +%N
echo "组合显示"
date +"%Y%m%d %H:%M:%S"
```

### **53 使用 egrep 过滤 MAC 地址**

```

#!/bin/bash

# 使用 egrep 过滤 MAC 地址 
 
# MAC 地址由 16 进制组成,如 AA:BB:CC:DD:EE:FF
# [0‐9a‐fA‐F]{2}表示一段十六进制数值,{5}表示连续出现5组前置:的十六进制
egrep "[0‐9a‐fA‐F]{2}(:[0‐9a‐fA‐F]{2}){5}" $1
```

### **54 统计双色球各个数字的中奖概率**

```
#!/bin/bash
 
# 统计双色球各个数字的中奖概率 
 
# 往期双色球中奖号码如下:
# 01 04 11 28 31 32  16
# 04 07 08 18 23 24  02
# 02 05 06 16 28 29  04
# 04 19 22 27 30 33  01
# 05 10 18 19 30 31  03
# 02 06 11 12 19 29  06
# 统计篮球和红球数据出现的概率次数(篮球不分顺序,统计所有篮球混合在一起的概率)
awk '{print $1"\n"$2"\n"$3"\n"$4"\n"$5"\n"$6}' 1.txt | sort | uniq -c | sort
awk '{print $7}' 1.txt | sort | uniq -c | sort
```

### **55 使用awk编写的wc程序**

```
#!/bin/bash

# 使用awk编写的wc程序 
 
# 自定义变量 chars 变量存储字符个数,自定义变量 words 变量存储单词个数
# awk 内置变量 NR 存储行数
# length()为 awk 内置函数,用来统计每行的字符数量,因为每行都会有一个隐藏的$,所以每次统计后都+1
# wc 程序会把文件结尾符$也统计在内,可以使用 cat ‐A 文件名,查看该隐藏字符
awk '{chars+=length($0)+1;words+=NF} END{print NR,words,chars}' $1
```