# 2 SRE Linux All

## Linux常用命令之文件管理

### 1、ls命令

```
ls  [选项] [文件]



"-a" ：显示所有文件和目录（.开头的隐藏文件也会列出）

"-l" ：显示当前目录下文件的详细信息。

"-d" ：显示目录本身，一般结合-l使用

"-i"  ：显示文件时，同时显示文件的索引节点

"-r"  ：倒序显示文件

"-h" ：以人们可读的方式显示文件大小，比如1k,200M，2G等）
```

常用操作：

```
ls               //查看当前目录下的所有目录和文件

ls -a            //查看当前目录下所有的目录和文件（包含隐藏文件）

ls -l            // 列表查看当前目录下所有目录和文件，与“ll”效果一样

ls /             //  查看指定目录下的所有目录和文件
```


### 2、cp命令

```
cp  [选项]   源文件   目标文件


"-r"  ：递归复制，用于复制目录

"-i" ：询问，如果目标文件已经存在，则会询问是否覆盖。默认设置

"-d" ：如果源文件为软链接（对硬链接无效），则复制出的目标文件也为软链接

"-p"：复制后目标文件保留源文件的属性（包括所有者，所属组，权限和时间）

"-a" ：相当于-d、-p、-r选项的集合。
```

常用操作：

```
cp  /tmp/tool   /opt         //将/tmp/tool目录复制到/opt目录下
cp  /tmp/tool/*  /opt       //将/tmp/tool目录下的文件复制到/opt目录下
cp -r /tmp/tool/ /opt       //将/tmp/tool目录下的文件及目录复制到/opt目录下
```

### 4、mkdir命令

```
mkdir  [选项]  目录..

"-p"  ：创建多级目录


mkdir  dir                       // 创建目录dir
mkdir  -p  dir/dir1/dir2         //创建多级目录
```

### 5、mv命令

```
mv  file1   file2    //移动file1到file2中，如果不存在file2，相当于重命名file1，同样适用于目录。
mv  /opt/*    /tmp   //将/opt目录下所有文件移动到/tmp目录下
```

### 6、rm命令

```

"-i" ：删除之前需要确定

"-r" ：递归删除，用于删除目录

"-f" ：强制删除，不需要确认


rm   file1               //删除当前目录下的文件
rm -f  file1             //强制删除文件
rm  -rf  dir            //强制删除dir目录及dir目录里的所有目录和文件
rm -rf  *                //删除当前目录的所有目录和文件，慎用
rm -rf  /*              //慎用，跑路命令。
```

### 7、ln命令

语法：

```
ln  [选项]   源文件   链接文件



"-s"  ：创建符号链接

"-f"  ：强制执行，覆盖现有文件


ln  test.txt   hard_link       //创建一个硬链接，删除了源文件，链接文件仍然可用

ln -s  test.txt   soft_link    // 创建一个软链接，删除了源文件，链接文件不可用，相当于window的快捷方式。
```

### 9、chown命令

```
chown  [选项]  属主权限.组权限  文件/目录


chown -R rudy.rudy   /tmp       # 将/tmp的属主和属组修改为rudy权限。

```

### 10、chattr命令

```
chattr [-RV][-v<版本编号>][+/-/=<属性>][文件或目录...]



-R 递归处理，将指定目录下的所有文件及子目录一并处理。
-V 显示指令执行过程。
　　+<属性> 开启文件或目录的该项属性。
　　-<属性> 关闭文件或目录的该项属性。
　　=<属性> 指定文件或目录的该项属性。

属性：

a：让文件或目录仅供附加用途。
c：将文件或目录压缩后存放。
i：不得任意更动文件或目录。
u：预防意外删除。


[root@localhost ~]# chattr +a /etc/passwd     //只能向文件中添加数据，而不能删除，多用于服务器日志文件安全 。   -a  可去掉该属性
[root@localhost ~]# chattr +i /etc/passwd     //设定文件不能被删除、改名、写入或新增内容   -i  可去掉该属性
```

### 11、lsattr命令

```

[root@localhost ~]# lsattr /etc/passwd    //查看文件扩展属性
----ia---------- /etc/passwd
```

### 12、xargs命令

作用：将标准输入转换成命令行参数

```
"-n"    ：指定每行的最大参数量
"-i"    ：以{}替代前面的结果


xargs -n  3   <   test.txt     //每行最多输出三个字符

xargs    ：所有输出都变为一行


find  /  -name  "*.log" | xargs -i  mv  {}  dir1  //将以.log结尾的所有文件移动到dir1目录下
find  /   -name  "*.log"  | xargs rm -rf          //将以.log结尾的所有文件删除。等同于find  / -name  "*.log" -exec rm -rf {} \;
```

### file：显示文件的类型

```

[root@localhost ~]# file anaconda-ks.cfg 
anaconda-ks.cfg: ASCII text
```

Linux文件类型说明：


普通文件类型：Linux中最多的一种文件类型, 包括 纯文本文件(ASCII)；二进制文件(binary)；数据格式的文件(data);各种压缩文件.第一个属性为 [-]

目录文件： 能用 # cd 命令进入的。第一个属性为 [d]，例如 [drwxrwxrwx]

块设备文件 ：就是存储数据以供系统存取的接口设备，简单而言就是硬盘。例如一号硬盘的代码是 /dev/hda1等文件。第一个属性为 [b]

字符设备文件：即串行端口的接口设备，例如键盘、鼠标等等。第一个属性为 [c]

套接字文件：这类文件通常用在网络数据连接。可以启动一个程序来监听客户端的要求，客户端就可以通过套接字来进行数据通信。第一个属性为 [s]，最常在 /var/run目录中看到这种文件类型

管道文件：FIFO也是一种特殊的文件类型，它主要的目的是，解决多个程序同时存取一个文件所造成的错误。FIFO是first-in-first-out(先进先出)的缩写。第一个属性为 [p]

链接文件：分类硬链接和软连接，类似Windows下面的快捷方式。第一个属性为 [l]，例如 [lrwxrwxrwx]


### md5sum：计算和校验文件的MD5值

```

选项： -c                 //从指定文件中读取MD5校验值，并进行校验 

[root@localhost ~]# md5sum anaconda-ks.cfg    //输出两部分，第一部分是md5值，后面是文件名

d862f17d783eaf1abcf4f376b5c85527  anaconda-ks.cfg 

[root@localhost ~]# md5sum anaconda-ks.cfg >md5.log   //生成校验文件

[root@localhost ~]# md5sum -c md5.log      //使用-c参数检查，结果OK表示文件没有变化
anaconda-ks.cfg: OK
```

## Linux常用命令之文件处理

### cat命令——查看文件内容

```

[root@localhost ~]# cat /etc/passwd     //查看文件内容
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin


[root@localhost ~]# cat >> /etc/fstab << EOF     //追加内容到文件尾部
> /dev/sdb  /data    xfs defaults   0 0
> EOF



[root@localhost ~]# cat  /dev/null  > file.txt      //清空文件内容
```

### more命令——分页显示文件内容



cat命令是查看所有内容，对于内容较长的文件无法一屏显示所有内容，所以就需要分页显示内容，more命令就有这种分页显示功能。

```
[root@localhost ~]# more /var/log/messages      //分页查看


空格键    查看下一页
回车键    查看下一行
b键      查看前一页
q键      退出查看页面
/file   查看包含file的内容
```

### less命令——分页显示文件内容

```
[root@localhost ~]# less /var/log/messages      //分页查看


空格键   查看下一页
回车键   查看下一行
b键     查看前一页
q键     退出查看页面
/字符串   向下查看包含file的内容
?字符串   向上搜索内容
n        向后查找下一个匹配的文本
g       移动到第一行
G      移动到最后一行
```


总结下more 和 less的区别:

- less可以按键盘上下方向键显示上下内容,more不能通过上下方向键控制显示

- less不必读整个文件，加载速度会比more更快

- less退出后shell不会留下刚显示的内容,而more退出后会在shell上留下刚显示的内容


### head命令——显示文件内容头部

```
[root@localhost ~]# head /etc/passwd    //默认显示文件10行
[root@localhost ~]# head -n 20 /etc/passwd  //显示文件的前20行
```

### tail命令——显示文件内容尾部

```
root@localhost ~]# tail -n 20 /var/log/messages    //显示文件的最后20行


[root@localhost ~]# tail -f /var/log/messages         //动态追踪日志信息
```

### cut命令——从文件中提取一段文字并输出

* -c	以字符为单位进行分割
* -d 自定义分隔符，默认以tab键为分隔符
* -f 指定显示哪个区域，常和-d使用

```
cut -c 2-10 test.txt   //剪切每行2-10位置的字符


cut -d :  -f  1  /etc/passwd     //指定以：作为分割符，-f指定显示第一个区域
```

### split命令——分割文件

```
split -l  10  test.txt   new_         //每10行分割一次，分割的文件名以new_开头

split -l  10  -d  test.txt   new_     //参数-d使用数字后缀

split -b 500M -d   test.txt   new_    //每500M分割一次
```

### sort命令——文件排序

- -n  依照数值大小进行排序
- -r	倒序排序
- -t  指定分隔符
- -k	按指定区间排序

```
sort -n test.txt        //按照数值大小进行排序

sort -nr  test.txt     //sort默认按照从小到大排序，使用-r选项就可以实现从大到小

sort -t " " -k2   test.txt     //以空格为分隔符，按照第二列进行排序
```

### uniq命令——去除重复行

* -c	去除重复行，并计算每行出现的次数

```
uniq -c test.txt             

sort -n test.txt | uniq -c   //结合sort使用，先排序后去重
```

### wc命令——统计文件的行数、单词或字节数

- -l   统计行数
- -c   统计字节数
- -w   统计单词数
- -L   打印最长行的长度

```

[root@localhost ~]# wc /etc/passwd   //不加任何参数会打印出行数，单词数据，字节数等三个参数
20  28 901 /etc/passwd

[root@localhost ~]# cat /var/log/messages |wc -l   //通过管道符来统计文件的行数
```

### tee命令——多重定向

用于将数据重定向到文件，同时提供一份重定向数据的副本输出到屏幕上

```
ls   | tee -a  ls.txt     //将输出内容记录到ls.txt文件中，并输出到屏幕上
```

## 3 Linux常用命令之信息显示

### uname命令——显示系统信息

- -a  显示系统所有相关的信息
- -m	显示计算机硬件架构
- -n	显示主机名称
- -r	显示内核发行版本号
- -s	显示内核名称
- -v  显示内核版本
- -o  显示操作系统名称

```
[root@localhost ~]# uname -a    //显示系统所有相关的信息
Linux localhost.localdomain 3.10.0-1160.el7.x86_64 #1 SMP Mon Oct 19 16:18:59 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux


[root@localhost ~]# uname -r    //显示内核发行版本
3.10.0-1160.el7.x86_64

[root@localhost ~]# uname -v
#1 SMP Mon Oct 19 16:18:59 UTC 2020
```

### hostname命令——显示或设置系统的主机名

/etc/hosts   ：配置域名的文件

/etc/hostname  ：centos7修改主机名的文件

/etc/sysconfig/network ：centos6修改主机名的文件

```

[root@localhost ~]# hostname liyongbin     //临时设置文件名，重启后失效

[root@localhost ~]# hostname   //查看主机名
liyongbin

[root@localhost ~]# hostnamectl set-hostname rudy   //永久修改文件名，重启不失效，也可以直接修改/etc/hostname文件

[root@localhost ~]# hostname 
rudy

[root@localhost ~]# hostname -I  //显示主机的所有IP地址，不依赖DNS解析，有多少块网卡就有多少个IP地址
10.13.2.13
```

### dmesg命令——系统启动异常诊断

dmesg用于显示内核环形缓冲区（kernel-ring  buffer）的内容。保存在/var/log目录下

```

[root@localhost ~]# dmesg |grep -i error     //查看系统启动过程中的错误信息
[    0.955079] BERT: Boot Error Record Table support is disabled. Enable it by using bert_enable as kernel parameter.
```

### du命令——显示目录或文件所占用的磁盘空间


- -s	显示总计容量
- -h  以人为可读的形式显示，以K，M，G为单位
- -m   以MB为单位
- --exclude=<目录或文件》  忽略指定的目录或文件

```

[root@localhost ~]# du -sh *   //查看当前目录所有子目录和文件的大小

0  anaconda-ks.cfg

4.0K  md5.log
4.0K  test.txt
[root@localhost ~]# du -sh md5.log         //查看hosts文件大小
4.0K  md5.log
```

### date命令——显示和设置时间

```

-d   显示字符串所指的日期与时间
-s   指定当前系统时间
-u   打印或设置协调世界时（UTC


%F	显示年月日
%T	显示时分秒
%Y	显示年份
%m	显示月份
%d	显示一个月的第几天
%H	显示时
%M	显示分
%S	显示秒
%w	显示星期几



[root@localhost ~]# date       //显示当前时间
2023年 10月 22日 星期日 15:29:40 CST

[root@localhost ~]# date +%F         //显示年月日
2023-10-22

[root@localhost ~]# date +%T         //显示时分秒
15:33:35

[root@localhost ~]# date -s "2024-10-22 15:30:00"     //设置指定时间
2024年 10月 22日 星期二 15:30:00 CST

[root@localhost ~]# date 
2024年 10月 22日 星期二 15:30:06 CST

[root@localhost ~]# date +%F -d "100day"       //显示100天后的时间
2024-01-30

[root@localhost ~]# date +%F -d "-100day"    //显示100天前的时间
2023-07-14

[root@localhost ~]# date +"%Y-%m-%d %H:%M:%S"   //指定格式显示当前时间
2023-10-22 15:37:24
```

### echo命令——显示一行文本


```
[root@localhost ~]# echo $PATH       //打印环境变量
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin

[root@localhost ~]# echo manager > test.sh    //将打印字符重定向到文本中

[root@localhost ~]# cat test.sh 
manager

[root@localhost ~]# echo manager |passwd --stdin root   //修改密码
更改用户 root 的密码 。
passwd：所有的身份验证令牌已经成功更新。
```

### watch命令——监视命令执行情况

watch 命令以周期性的方式执行给定的命令，并全屏显示执行结果。watch 可以帮助监测一个命令的运行结果，省得我们一遍遍地手动运行。


-n   指定监测间隔，单位秒。默认 2s，不能低于 0.1s
-d  高亮显示最近两次更新之后的差异


```

[root@localhost ~]# watch -n 1 -d netstat -lntup  //每隔 1s 高亮显示网络连接数的变化情况。
[root@localhost ~]# watch uptime   //每2s显示负载情况

Ctrl+C退出watch命令界面
```

### which命令——显示命令的全路径


```

[root@localhost ~]# which   date     //查看date命令的全路径
/usr/bin/date

[root@localhost ~]# which which     //如果设置了别名，那么使用which功能还将会显示别名
alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'
  /usr/bin/alias
  /usr/bin/which

  
[root@localhost ~]# which shutdown poweroff    //同时显示多个
/usr/sbin/shutdown
/usr/sbin/poweroff
```

### 详解/bin,/sbin,/usr/sbin,/usr/bin 目录区别、

/sbin 和/bin

1. 从命令功能区分

**/sbin 下的命令属于基本的系统命令，如shutdown，reboot，用于启动系统，修复系统**

**/bin下存放一些普通的基本命令，如ls,chmod等，这些命令在Linux系统里的配置文件脚本里经常用到**

2. 从用户权限角度区分

/sbin目录下的命令通常只有管理员才可以运行

/bin下的命令管理员和一般的用户都可以使用。

/bin是系统的一些指令，主要放置一些系统的必备执行命令

```
cat、cp、chmod df、dmesg、gzip、kill、ls、mkdir、more、mount、rm、su、tar等
```

/sbin一般是指超级用户指令。主要放置一些系统管理的必备程式

```
dump、fdisk、halt、ifconfig、ifup、 ifdown、init、insmod ，lsmod、reboot、shutdown 等。
```

## 4 Linux常用命令之文件备份和压缩

### tar命令——压缩备份

tar命令是linux非常使用频率非常高的一个命令。打包是指将一大堆文件或目录变成一个总的文件，压缩则是将一个大的文件通过一些压缩算法变成一个小文件。

```

-c 创建一个新的tar压缩包
-x 解压tar包
-t 查看压缩包的内容
-C 指定解压路径
-f 指定要处理的文件名
-j 使用bzip2方式进行压缩或解压
-z 使用gzip方式进行压缩或解压
--exclude=PATHERN	打包时排除不需要处理的文件或目录
-v 显示详细过程
```

```

1、 解压缩包到指定位置

[root@localhost ~]# tar -zxvf jdk-8u202-linux-x64.tar.gz -C /data  
[root@localhost ~]# ll /data
总用量 0
drwxr-xr-x 7 10 143 245 12月 16 2018 jdk1.8.0_202

2、 压缩文件

[root@localhost data]# tar -zcvf jdk8.tar.gz jdk1.8.0_202/
[root@localhost data]# ll
总用量 189500
drwxr-xr-x 7   10  143       245 12月 16 2018 jdk1.8.0_202
-rw-r--r-- 1 root root 194045097 10月 22 20:26 jdk8.tar.gz

3、 查看压缩包内容
[root@localhost data]# tar -tf jdk8.tar.gz
```

### gzip命令——压缩或解压文件

gzi**p命令用于将一个大的文件通过压缩算法变成一个小的文件。gzip命令不能直接压缩目录，因此目录需要先用tar打包成一个文件，然后tar再调用gzip进行压缩。**

```
-d	  解开压缩文件
-v   显示执行过程
-c   将内容输出到标准输出，不改变原始文件
```

常用操作：

```
1、压缩文件，默认不保留源文件，将源文件做成.gz压缩包
[root@localhost ~]# gzip hosts    
[root@localhost ~]# ll
总用量 8
-rw------- 1 root root 1506 3月  13 2022 anaconda-ks.cfg
-rw-r--r-- 1 root root   94 10月 22 20:36 hosts.gz
drwxr-xr-x 2 root root    6 10月 22 20:36 test

2、解开压缩包
[root@localhost ~]# gzip -d hosts.gz 
[root@localhost ~]# ll
总用量 8
-rw------- 1 root root 1506 3月  13 2022 anaconda-ks.cfg
-rw-r--r-- 1 root root  177 10月 22 20:36 hosts
drwxr-xr-x 2 root root    6 10月 22 20:36 test

3、压缩目录（压缩失败，只能压缩文件，不可压缩目录）
[root@localhost ~]# gzip test/
gzip: test/ is a directory -- ignored

4、保留源文件压缩
[root@localhost ~]# gzip -c hosts >hosts.gz
[root@localhost ~]# ll
总用量 12
-rw------- 1 root root 1506 3月  13 2022 anaconda-ks.cfg
-rw-r--r-- 1 root root  177 10月 22 20:36 hosts
-rw-r--r-- 1 root root   94 10月 22 20:44 hosts.gz
```

### zip命令——打包和压缩文件

zip压缩格式是Windows与Linux等多平台通用的压缩格式。和gzip命令相比，zip命令压缩文件不仅不会删除源文件，而且还可以压缩目录。

```

-r   递归压缩目录和文件
-x   压缩文件时排除某个文件
```

```
、压缩文件
[root@localhost ~]# cp /etc/services .


[root@localhost ~]# ll
总用量 660
-rw------- 1 root root   1506 3月  13 2022 anaconda-ks.cfg
-rw-r--r-- 1 root root 670293 10月 22 20:50 services
[root@localhost ~]# zip services.zip ./services 
  adding: services (deflated 80%)

  
[root@localhost ~]# ll
总用量 796
-rw------- 1 root root   1506 3月  13 2022 anaconda-ks.cfg
-rw-r--r-- 1 root root 670293 10月 22 20:50 services
-rw-r--r-- 1 root root 136227 10月 22 20:50 services.zip


2、压缩目录
[root@localhost data]# zip jdk.zip ./jdk1.8.0_202/    //没有压缩
updating: jdk1.8.0_202/ (stored 0%)
[root@localhost data]# zip -r jdk.zip ./jdk1.8.0_202/    
[root@localhost data]# ll
总用量 192112
drwxr-xr-x 7   10  143       245 12月 16 2018 jdk1.8.0_202
-rw-r--r-- 1 root root 196719566 10月 22 20:53 jdk.zip
```

### unzip命令——解压zip命令压缩的文件

- -d 指定解压的路径
- -v 显示解压详细过程，默认选项
- -o 解压时不提示是否覆盖文件

```
1、默认解压到当前目录
[root@localhost data]# unzip jdk.zip
[root@localhost data]# ll
总用量 192112
drwxr-xr-x 7 root root       245 12月 16 2018 jdk1.8.0_202
-rw-r--r-- 1 root root 196719566 10月 22 20:53 jdk.zip

2、解压到指定目录
[root@localhost data]# unzip -d /root jdk.zip   //解压到/root目录
[root@localhost data]# ll /root/
总用量 4
-rw------- 1 root root 1506 3月  13 2022 anaconda-ks.cfg
drwxr-xr-x 7 root root  245 12月 16 2018 jdk1.8.0_202

3、再次解压不提示，直接覆盖
[root@localhost data]# unzip -d /root jdk.zip    //再次执行会提示是否覆盖
Archive:  jdk.zip
replace /root/jdk1.8.0_202/javafx-src.zip? [y]es, [n]o, [A]ll, [N]one, [r]ename: 
error:  invalid response [{ENTER}]
replace /root/jdk1.8.0_202/javafx-src.zip? [y]es, [n]o, [A]ll, [N]one, [r]ename: ^C[root@localhost data]# 
[root@localhost data]# unzip -o -d /root jdk.zip   //添加-o操作后就不会询问是否覆盖。
```

### scp命令——远程复制文件

scp使用的是ssh协议。每次都是全量复制。适合第一次复制。

```
-r    递归复制
-p    保留文件原始属性
-P    端口号  指定传输的端口号
-q    不显示传输进度
```

```
1、将本机的文件复制到远端主机，需要知道远端的服务器密码
 scp -rp jdk1.8.0_202/ root@10.10.10.3:/data  

root是远端用户，也可以是其他普通用户
10.10.10.3是远端IP地址
/data是远端服务器的目录，要写绝对路径

2、将远端主机文件复制到本机。
[root@localhost data]# scp root@10.10.10.3:/etc/services /data
root@10.10.10.3's password:       //输入远端主机密码
services         100%  655KB  64.4MB/s   00:00 

3、指定端口复制，有些不是常规端口时就需要指定
[root@localhost data]# scp -P 22 root@10.10.10.3:/etc/hosts /data
root@10.10.10.3's password: 
hosts              100%  158   110.7KB/s   00:00
```

### rsync命令——文件同步工具


rsync具有可使本地和远程两台主机之间的数据快速复制同步镜像、远程备份的功能，这个功能类似于ssh带的scp命令，**但是又优于scp命令的功能，scp每次都是全量拷贝，而rsync可以增量拷贝**。

```

-z  传输时进行压缩
-a  以递归方式传输文件，并保持原有属性，相当于-rtopgDI
-r  递归复制传输
-t  保持文件的时间信息

-o  保留文件的属主信息
-p  保留文件的权限
-g  保留文件的属组信息
-D	保留设备文件信息
-l  保留软链接
--exclude-from=file  排除指定的文件不需要传输
--delete   使目标目录内容和源保持目录一直，删除不同的文件
```

1、本地模式：用于本机传输文件，相当于cp命令

```

rsync   选项    源文件   目标文件

[root@localhost ~]# rsync -av /etc/hosts  /tmp
sending incremental file list
hosts


sent 267 bytes  received 35 bytes  604.00 bytes/sec
total size is 177  speedup is 0.59
[root@localhost ~]# ll /tmp
总用量 4
-rw-r--r-- 1 root root 177 5月   6 2022 hosts

```

2、远程访问模式：将文件传输给远端主机或拉去远端主机的文件


```
拉取：rsync  选项   用户@主机：源文件   目标文件

推送：rsync  选项  源文件  用户@主机:目标文件

1、拉取远程文件
[root@localhost ~]# rsync -avz root@10.10.10.3:/etc/hostname ./ 
2、拉取远程目录下的所有文件
[root@localhost ~]# rsync -avz root@10.10.10.3:/root/  /data/   
3、本地文件推送给远程目录
[root@localhost ~]# rsync -avz /data  root@10.10.10.3:/tmp/
```


### Linux常用命令之用户管理

* useradd命令——创建用户

```

// 创建用户的同时还会创建一个与用户名相同的用户组
useradd  test  

// 创建用户rudy，属于mysql组，uid为1002 
useradd -g mysql  -u 1002  rudy   

// 创建禁止登陆的用户 ，/sbin/nologin表示禁止登录
useradd -M -s  /sbin/nologin   rudy
```

* **usermod命令——修改用户信息**

```

// 修改家目录和uid
usermod -d /dataroot/rudy -u 1002 rudy

// 将rudy用户名称更改为zhangsan
usermod -l zhangsan  rudy

// 将rudy添加到这些组里面
usermod -G public,wangluo rudy

```

* **userdel命令——删除用户及该用户相关的文件**

```

// 不加参数删除用户，家目录还存在
userdel rudy

// 加-r参数删除用户，连同家目录一起删除
userdel -r rudy

// 强制删除用户
userdel -l -r  rudy
```

在实际工作中尽量不要使用userdel删除用户，而是采用在/etc/passwd里注释用户的方法，防止用户误删带来的系统及服务不正常。


* groupadd命令——创建新的用户组

groupadd命令的用途一般不大，因为useradd命令在创建用户的同时还会创建与用户同名的用户组。

```

添加GID为1020的rudy组
groupadd -g 1020  rudy
```

* groupdel命令——删除用户组

```
groupdel rudy
```

* **passwd命令——修改用户密码**

```

1、修改自身密码
[root@localhost ~]# passwd root
更改用户 root 的密码 。
新的 密码：
重新输入新的 密码：
passwd：所有的身份验证令牌已经成功更新。


2、一条命令修改密码，但不安全。
[root@localhost ~]# echo manager |passwd --stdin root
更改用户 root 的密码 。
passwd：所有的身份验证令牌已经成功更新
```

* su命令——切换用户

```
# 只切换用户，不切换家目录
su liyb  

# 切换家目录，
su - root
# 让系统每一次开机时能自动以普通用户启动指定的服务脚本
cat  /etc/rc.local
su - liyb -c '/bin/sh  /data/scripts/install.sh'
```

* **last命令——显示用户登录列表**

```
[root@harbor ~]# last
root     pts/0        172.16.1.203     Fri Dec 29 11:36   still logged in
root     pts/0        172.16.1.203     Thu Dec 28 13:58 - 14:09  (00:11)
root     pts/0        172.16.1.203     Thu Dec 28 13:06 - 13:11  (00:05)
root     pts/0        172.16.1.203     Thu Dec 28 11:47 - 12:07  (00:19)
root     pts/0        172.16.1.203     Thu Dec 28 10:23 - 10:38  (00:14)
root     pts/0        172.16.1.203     Thu Dec 28 10:12 - 10:17  (00:05)
```


* **lastb命令——显示用户登录失败的记录**

lastb命令可以从日志文件/var/log/btmp中读取信息，并显示用户登录失败的记录，用于发现系统登录异常

```
[root@harbor ~]# lastb
rudyli   pts/0                         Fri Dec 29 12:46 - 12:46  (00:00)
Administ ssh:notty    172.16.1.203     Thu Dec 28 13:58 - 13:58  (00:00)
Administ ssh:notty    172.16.1.203     Thu Dec 28 11:47 - 11:47  (00:00)
Administ ssh:notty    172.16.1.203     Mon Dec 25 11:19 - 11:19  (00:00)
Administ ssh:notty    172.16.1.203     Mon Dec 25 11:19 - 11:19  (00:00)
Administ ssh:notty    172.16.1.203     Mon Dec 25 11:19 - 11:19  (00:00)
```


* lastlog命令：显示所有用户的最近登录记录

从日志文件/var/log/lastlog中读取信息，并显示所有用户的最近登录记录，用于查看系统是否有异常登录


## 2 Linux常用命令之进程管理

### 1 ps命令——查看进程

选项

```
a   显示与终端相关的所有进程，包含每个进程的完整路径
u   显示进程的用户信息
x   显示与终端无关的所有进程
-e  显示所有进程
-f  额外显示UID，PPID，STIME栏位信息。
-u  显示指定用户相关的进程信息
```

**1、输出每个进程信息**

```
[root@harbor ~]# ps -ef
UID         PID   PPID  C STIME TTY          TIME CMD
root          1      0  0 12月25 ?      00:00:57 /usr/lib/systemd/systemd --switched-root --system --deserialize 22
root          2      0  0 12月25 ?      00:00:00 [kthreadd]
root          4      2  0 12月25 ?      00:00:00 [kworker/0:0H]

2、查看特定进程信息
[root@harbor ~]# ps -ef |grep nginx

3、BSD语法显示每个进程信息
[root@harbor ~]# ps aux
```

输出说明：

```
USER：该进程属于的用户。
PID：该进程的进程号。
%CPU：该进程使用掉的CPU资源百分比。
%MEM：该进程所占用的物理内存百分比。
VSZ：该进程使用掉的虚拟内存量（单位为Kbytes）。
RSS：该进程占用的固定的内存量（单位为Kbytes）。
TTY：该进程是在哪个终端机上面运作的，若与终端机无关，则显示“?”，
另外，tty1-tty6是本机上面的登入者进程，若为pts/0等，则表示为由网络连接进主机的进程。
STAT：该进程目前的状态，主要的状态包括如下几种。
R：正在运行，或者是可以运行。
S：正在中断睡眠中，可以由某些信号（signal）唤醒。
D：不可中断睡眠。·T：正在侦测或者是停止了。
Z：已经终止，但是其父进程无法正常终止它，从而变成zombie（僵尸）进程的状态。
+：前台进程。
l：多线程进程。
N：低优先级进程。
<：高优先级进程。
s：进程领导者。
L：已将页面锁定到内存中。
START：该进程被触发启动的时间。
TIME：该进程实际使用CPU运作的时间。
COMMAND：该进程的实际命令。
```

### kill命令——终止进程

能中止你希望停止的进程

```
-l	列出全部的信号名称
-s 指定要发送的信号
```

显示系统的所有信号

```
[root@localhost ~]# kill -l
 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL       5) SIGTRAP
 6) SIGABRT      7) SIGBUS       8) SIGFPE       9) SIGKILL     10) SIGUSR1
11) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM
16) SIGSTKFLT   17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP
21) SIGTTIN     22) SIGTTOU     23) SIGURG      24) SIGXCPU     25) SIGXFSZ
26) SIGVTALRM   27) SIGPROF     28) SIGWINCH    29) SIGIO       30) SIGPWR
31) SIGSYS      34) SIGRTMIN    35) SIGRTMIN+1  36) SIGRTMIN+2  37) SIGRTMIN+3
38) SIGRTMIN+4  39) SIGRTMIN+5  40) SIGRTMIN+6  41) SIGRTMIN+7  42) SIGRTMIN+8
43) SIGRTMIN+9  44) SIGRTMIN+10 45) SIGRTMIN+11 46) SIGRTMIN+12 47) SIGRTMIN+13
48) SIGRTMIN+14 49) SIGRTMIN+15 50) SIGRTMAX-14 51) SIGRTMAX-13 52) SIGRTMAX-12
53) SIGRTMAX-11 54) SIGRTMAX-10 55) SIGRTMAX-9  56) SIGRTMAX-8  57) SIGRTMAX-7
58) SIGRTMAX-6  59) SIGRTMAX-5  60) SIGRTMAX-4  61) SIGRTMAX-3  62)
```

kill命令默认发送的信号是15，用于结束进程。使用信号9可以强制终止进程。

```

[root@localhost ~]# netstat -lntup
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1353/sshd           
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      1473/master         
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      945/rpcbind 

[root@localhost ~]# kill -9  1473

[root@localhost ~]# netstat -lntup   #再次查看没有进程号为1473的进程。

Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1353/sshd           
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      945/rpcbind         
[root@localhost ~]#
```


### top命令——动态显示各个进程的资源占用情况

top用于实时地对系统资源进行监控，输出各个进程的资源占用情况。同时top命令也是一个交互式命令。

交互式选项

```

交互式命令	含义
h或？       显示帮助信息，给出交互式命令的一些说明总结
m          以内存排序显示
z	       打开/关闭颜色显示
n或#       设置显示做大进程行数
q         退出top
```

### nohup命令——后台运行进程

**nohup命令可以将程序以后台方式运行，被运行程序的输出信息将不会显示到终端。**

无论是否将nohup命令的输出重定向到终端，输出都将写入到当前目录的nohup.out文件中。如果当前目录的nohup.out文件不可写，则输出重定向到$HOME/nohup.out文件中。

正常情况下，如果用户退出登录或会话终止，则用户正在执行并可持续一段时间的命令（非守护进程）将自动终止

实际工作中，我们一般会和&一起使用，让程序直接在后台运行。

```

[root@localhost ~]# nohup ping 172.16.1.20 &
[2] 109057
[root@localhost ~]# nohup: 忽略输入并把输出追加到"nohup.out"


[root@localhost ~]# ps -ef |grep ping
root     109057  93226  0 10:45 pts/0    00:00:00 ping 172.16.1.20
root     109329  93226  0 10:45 pts/0    00:00:00 grep --color=auto ping

注意：以上示例是为了测试，测试完后要kill掉，不然一直生成日志,占用磁盘空间。

[root@localhost ~]# kill -9 109057
```

### runlevel命令——输出当前运行级别

```
[root@localhost ~]# runlevel
N 3
# 显示当前运行界别为3，即为命令行多用户模式
```

级别


- 0	停机
- 1   单用户模式
- 2   无网络的多用户模式
- 3   多用户模式
- 4   未使用
- 5   图形界面多用户模式
- 6   重启

### init命令——进程初始化工具


init命令是Linux下的进程初始化工具，init进程是所有Linux进程的父进程，它的进程号为1

切换运行界别

```
# 关机
[root@localhost ~]# init 0   

# 重启 
[root@localhost ~]# init 6
```

### service命令——管理系统服务

service命令用于centos6以及前面版本。centos7后使用systemd管理系统服务。

service命令用于对系统服务进程管理，可以对服务进行启动，停止，重启，重新加载配置，查看状态等操作。

```

# 启动服务
service chronyd start

# 停止服务
service chronyd stop

# 重启服务
service chronyd restart

# 平滑重启服务
service chronyd reload

# 查看服务状态
service chronyd status

```

#### systemctl命令——管理系统服务

```

# 启动服务
systemctl start chronyd

# 停止服务
systemctl stop chronyd

# 重启服务
systemctl restart chronyd

# 平滑重启服务
systemctl restart chronyd

 # 查看服务状态
systemctl restart chronyd

# 列出已安装的unit
systemctl list-unit-files
 
 #  列出类型为service的项目
systemctl list-units --type=service

 # 输出主机当前的运行模式
systemctl get-default  

# 设置主机的运行模式，关闭图形界面，使用命令行模式
systemctl isolate multi-user.target 

#将目前的操作环境改为图形界面
systemctl isolate graphical.target  

 #系统关机
systemctl poweroff  

#重新开机 
systemctl reboot   

#进入暂停模式 
systemctl suspend   

#强制进入救援模式 
systemctl rescue   

#禁用某个服务
systemctl mask etcd.service   

#解除禁用某个服务
systemctl unmask etcd.service   
```

## Linux常用命令之磁盘管理

### fdisk命令——磁盘分区工具


fdisk是Linux系统下常用的分区工具。受mbr分区表的限制，fdisk工具只能给小于2TB的磁盘划分分区。如果大于2TB，无法识别超过2TB那部分容量。如果磁盘大于2TB最好使用parted分区工具。

* -l  显示所有磁盘分区的信息

```

[root@localhost ~]# fdisk -l
磁盘 /dev/vda：107.4 GB, 107374182400 字节，209715200 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0x000a4dca

   设备 Boot      Start         End      Blocks   Id  System
/dev/vda1            2048        6143        2048   83  Linux
/dev/vda2   *        6144     4200447     2097152   83  Linux
/dev/vda3         4200448   209715199   102757376   83  Linux


磁盘 /dev/vdb：214.7 GB, 214748364800 字节，419430400 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节


磁盘 /dev/vdc：107.4 GB, 107374182400 字节，209715200 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节

[root@localhost ~]# fdisk /dev/vdc
欢迎使用 fdisk (util-linux 2.23.2)。

更改将停留在内存中，直到您决定将更改写入磁盘。
使用写入命令前请三思。

Device does not contain a recognized partition table
使用磁盘标识符 0x5eb87e6d 创建新的 DOS 磁盘标签。

命令(输入 m 获取帮助)：m
命令操作
   a   toggle a bootable flag
   b   edit bsd disklabel
   c   toggle the dos compatibility flag
   d   delete a partition
   g   create a new empty GPT partition table
   G   create an IRIX (SGI) partition table
   l   list known partition types
   m   print this menu
   n   add a new partition
   o   create a new empty DOS partition table
   p   print the partition table
   q   quit without saving changes
   s   create a new empty Sun disklabel
   t   change a partition's system id
   u   change display/entry units
   v   verify the partition table
   w   write table to disk and exit
   x   extra functionality (experts only)


命令(输入 m 获取帮助)：n    # 新增加一个分区
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p   # 新增一个主分区
分区号 (1-4，默认 1)：1
起始 扇区 (2048-209715199，默认为 2048)：
将使用默认值 2048
Last 扇区, +扇区 or +size{K,M,G} (2048-209715199，默认为 209715199)：+10G
分区 1 已设置为 Linux 类型，大小设为 10 GiB


命令(输入 m 获取帮助)：p   # 打印分区表


磁盘 /dev/vdc：107.4 GB, 107374182400 字节，209715200 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0x5eb87e6d

   设备 Boot      Start         End      Blocks   Id  System
/dev/vdc1            2048    20973567    10485760   83  Linux


命令(输入 m 获取帮助)：t   # 更换分区格式
已选择分区 1
Hex 代码(输入 L 列出所有代码)：L

Hex 代码(输入 L 列出所有代码)：8e
已将分区“Linux”的类型更改为“Linux LVM”

命令(输入 m 获取帮助)：p   # 查看

磁盘 /dev/vdc：107.4 GB, 107374182400 字节，209715200 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0x5eb87e6d

   设备 Boot      Start         End      Blocks   Id  System
/dev/vdc1            2048    20973567    10485760   8e  Linux LVM

命令(输入 m 获取帮助)：w
The partition table has been altered!

Calling ioctl() to re-read partition table.
正在同步磁盘。

[root@localhost ~]# lsblk    # 查看分区情况
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sr0     11:0    1  9.5G  0 rom
vda    253:0    0  100G  0 disk
├─vda1 253:1    0    2M  0 part
├─vda2 253:2    0    2G  0 part /boot
└─vda3 253:3    0   98G  0 part /
vdb    253:16   0  200G  0 disk /data
vdc    253:32   0  100G  0 disk
└─vdc1 253:33   0   10G  0 part
```

### mkfs命令——创建文件系统

mkfs命令用于在指定的设备（或磁盘分区）上创建格式化并创建文件系统。创建分区后并不能使用分区，需要对分区进行格式化为特定文件系统后才能用来存取数据。

mkfs只是一个前端命令，它通过-t参数指定文件系统类型后会调用相应的命令mkfs.fstype。因此可以直接使用mkfs.ext4这个命令创建ext4文件系统

```

# 将/dev/vdc1格式化为xfs文件系统
[root@localhost ~]# mkfs.xfs /dev/vdc1

# 将/dev/vdc2格式化为ext4系统
[root@localhost ~]# mkfs -t ext4 /dev/vdc2
```

### fsck命令——检查并修复Linux文件系统

fsck命令用于检查并修复文件系统中的错误。需要注意的是：文件系统必须是卸载状态，否则可能会出现故障；**不要对正常的分区使用fsck，fsck会根据/etc/fstab进行文件系统检查**。

只有当系统开机显示磁盘错误时，才需要执行。

```
[root@localhost ~]# fsck -A
```

### monut命令——挂载文件系统


一个分区被格式化后并不可以直接使用，还需要将其挂载到指定的挂载点上才可以被访问。

```
-l   显示已经挂载的设备相关信息
-a   挂载/etc/fstab文件中的配置信息
-t   指定挂载的文件系统类型。如nfs，iso9660（光盘）
-o   后接一些挂载选项。
```

```

# 显示系统的挂载信息

[root@localhost ~]# mount
/dev/vda3 on / type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
selinuxfs on /sys/fs/selinux type selinuxfs (rw,relatime)
nfsd on /proc/fs/nfsd type nfsd (rw,relatime)
/dev/vdb on /data type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/vda2 on /boot type xfs (rw,relatime,seclabel,attr2,inode64,noquota)


# 挂载光盘光驱，前提是有光驱镜像

[root@localhost ~]# mount  /dev/cdrom /media

[root@localhost ~]# ll   /media

# 挂载nfs文件系统
[root@localhost ~]# mount -t nfs   -o nodev,noatime  172.16.1.27:/data   /data


永久挂载要写入到/etc/fstab中
[root@localhost ~]# cat  >> /etc/fstab  <<EOF
172.16.1.27:/data   /data   nfs    nodev,noatime   0 0
EOF

# 文件系统只读时，需要重新挂载根目录为读写模式。
[root@localhost ~]# mount  -o remount,rw  /

# 确认fstab文件中配置正确，避免重启失败
[root@localhost ~]# mount -a
```

永久性挂载的磁盘都需要写入/etc/fstab文件中，若这个文件配置有错误会导致系统重启无法正常进入系统。可以使用mount -a进行挂载测试，如果能挂载成功，重启一般会正常。

### umount命令——卸载已挂载的文件系统

umount卸载可以接挂载点目录，也可以接设备文件。

选项

- -f	强制卸载
- -l 将文件系统从文件系统层次结构中分离出来，并清除我对文件系统的所有引用


```

# 卸载挂载点/media
[root@localhost ~]# umount  /media
# 强制删除

[root@localhost ~]# cd /media/

[root@localhost media]# umount /media    # 如果处于挂载点中，则无法卸载，可以退出挂载点再卸载或强制卸载
umount: /media：目标忙。
        (有些情况下通过 lsof(8) 或 fuser(1) 可以
         找到有关使用该设备的进程的有用信息)
[root@localhost media]# umount -lf /media
```

### sync命令——刷新文件系统缓存区

sync命令会将内存缓冲区的数据强制刷新到磁盘。



```
操作：
# 手动将数据从缓冲区刷到磁盘中并重启系统
sync
sync
reboot
```

##Linux常用命令之网络管理


### ifconfig命令——配置或显示网络接口信息

ifconfig命令用于配置网卡IP地址等网络参数或显示当前网络的接口状态，需要以root用户的身份来执行。

如果命令不存在则安装

```
yum install -y net-tools

```

使用ifconfig命令配置网卡信息仅会临时生效，重启网络或服务器就会失效。

选项：

- Up    激活指定的网络接口
- down  关闭指定的网络接口
- Hw    设置网卡的MAC地址

```

# 查看所有已启动的网卡信息
 ifconfig

# 查看eth0网卡信息
ifconfig eth0

# 查看所有网卡信息（包括未开启的）
ifconfig -a

# 启动eth1网卡
ifconfig eth1 up

# 关闭eth1网卡
ifconfig eth1 down

# 为网卡配置信息
ifconfig eth0 172.16.1.30


# 为网卡配置多个IP
ifconfig eth0:0 172.16.1.28 netmask 255.255.255.0  up

ifconfig

eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.16.1.27  netmask 255.255.255.0  broadcast 172.16.1.255
        inet6 fe80::ffaa:cae4:9cc0:3250  prefixlen 64  scopeid 0x20<link>
        inet6 fe80::eb49:7220:1bb9:df4f  prefixlen 64  scopeid 0x20<link>
        inet6 fe80::21b6:b00:310a:19d3  prefixlen 64  scopeid 0x20<link>
        ether 28:6e:d4:89:b3:85  txqueuelen 1000  (Ethernet)
        RX packets 5035132  bytes 609739285 (581.4 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2070630  bytes 384120838 (366.3 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth0:0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.16.1.28  netmask 255.255.255.0  broadcast 172.16.1.255
        ether 28:6e:d4:89:b3:85  txqueuelen 1000  (Ethernet)
```

网卡的配置文件在`/etc/sysconfig/network-scriprts/`目录下。eth0对应的是ifcfg-eth0文件。

### ifup命令——激活网络接口


ifup命令可读取配置文件`/etc/sysconfig/network`和`/etc/sysconfig/network-scripts/ifcfg-<configuration>`对网络接口进行相应的操作。

```

# 激活eth0网络接口，等同于ifconfig eth0 up
ifup eth0   

# 查看eth0网络接口状态
ifconfig eth0
```

### ifdown命令——用于禁用指定的网络接口。

```
# 关闭eth0网卡接口，该操作导致断开SSH连接，谨慎操作
ifdown eth0

# 重启网卡
ifdown eth0 && ifup eth0
```

### route命令——管理路由表

**route命令可以为服务器设置静态路由。**

```
-n  查看路由信息
-ee  显示更详细的路由信息
add  添加路由信息
del  删除路由信息
-net  到一个网络的路由，后面接网络号地址
-host  到一个主机的路由，后面接一个主机地址
netmask  添加子网掩码
gw  指定网关
dev If  指定由哪个网卡出去，如eth0
```

```

# 查看系统路由信息
route -n 

# 删除路由信息
route del default   //删除默认网关
route del default gw 172.16.1.254 
route del default  gw 172.16.1.254 dev eth0

# 添加路由信息
route add default gw 172.16.1.254
route add default gw 172.16.1.254 dev eth0

# 配置去往某一网络或网段的路由

172网段主机访问192网段的主机。
route add -net 192.168.4.0/24 gw 172.16.1.254
route add -net 192.168.4.0/24 netmask 255.255.255.0 dev eth1

删除路由
route del -net 192.168.4.0/24 dev eth1

# 配置去往某个主机的路由
route add -host 172.16.2.250 dev eth0

删除路由：
route del -host 172.16.2.250 dev eth0
```


以上配置在重启网络时都会失效需要写到配置中。

**需要写到`/etc/sysconfig/network-scripts/route-eth*或/etc/rc.local`文件中，重启会重新加载。**

```
192.168.4.0/24 via 172.16.1.254
```

### arp命令——管理系统的arp缓存


**arp是地址解析协议，主要是根据IP地址获取物理地址**。arp可以显示缓存区中的所有条目、删除指定的条目或者添加静态的IP地址与MAC地址的对应关系。


```
-n  显示IP地址对应的MAC地址信息


-s <主机><MAC>   指定主机的IP地址与MAC地址的静态映射

-d <主机>  删除制定主机的arp条目


# 显示arp缓存区的所有条目
[root@localhost ~]# arp -n
Address                  HWtype  HWaddress           Flags Mask            Iface
172.16.1.20              ether   28:6e:d4:8a:02:c0   C                     eth0
172.16.1.25              ether   28:6e:d4:8a:15:61   C                     eth0
172.18.0.9               ether   02:42:ac:12:00:09   C                     br-7f15bb750845
172.16.1.254                     (incomplete)                              eth0
172.16.1.22              ether   28:6e:d4:89:e0:09   C                     eth0
172.16.1.24              ether   28:6e:d4:88:eb:3d   C                     eth0


# 查看指定主机的arp条目
arp -n 172.16.1.27  

# 绑定IP地址和MAC地址（临时，）
[root@localhost ~]# arp -s 172.16.1.20 28:6e:d4:8a:02:c1
[root@localhost ~]# arp -n
Address                  HWtype  HWaddress           Flags Mask            Iface
172.16.1.20              ether   28:6e:d4:8a:02:c1   CM                    eth0
# 删除静态ARP绑定
[root@localhost ~]# arp -d 172.16.1.20

```

### ip命令——网络配置工具

ip命令用于显示或管理linux系统的路由、网络设备、策略路由和隧道。

语法格式：

```
ip  [选项]  [网络对象]  [操作命令]



# 查看网卡信息
[root@localhost ~]# ip a

# 关闭网卡
[root@localhost ~]# ip link set eth0 down

# 开启网卡
[root@localhost ~]# ip link set eth0 up

# 添加IP地址
[root@localhost ~]# ip a add 172.16.1.31/24 dev eth0

# 删除IP地址
[root@localhost ~]# ip a del 172.16.1.31/24 dev eth0

# 查看路由
[root@localhost ~]# ip route
default via 172.16.1.254 dev eth0 proto static metric 100
172.16.1.0/24 dev eth0 proto kernel scope link src 172.16.1.27 metric 100
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1
172.18.0.0/16 dev br-7f15bb750845 proto kernel scope link src 172.18.0.1
```

注意：

1、删除网卡的主IP地址，同时会删除该网卡的所有IP地址。

2、删除网卡的辅助IP地址，不会影响该网卡的其他IP地址。

3、ip命令集成了ifconfig和route的功能，逐渐替代这两个IP

### netstat命令——查看网络端口信息

用于显示本机网络的连接状态、运行端口和路由表等信息。

```
-n      显示数字形式的地址而不是去解析主机
-a     显示处于监听状态和非监听状态的socket信息
-c <秒数>  每隔几秒刷新一次
-l  仅显示处于监听状态的网络状态
-t  显示所有的TCP连接情况
-u	显示所有的UDP连接情况
-p   显示socket所属进程的PID和名称
```

常用组合：

```
# 列出所有处于监听状态的端口信息
[root@localhost ~]# netstat -lntup


# 列出所有处于监听和非监听状态的端口信息
[root@localhost ~]# netstat -antup
```

重要的两个状态:

1、**ESTABLISHED：表示处于连接的状态，认为有一个EASTABLISHED是一个服务的并发连接**。

2、**LISTEN：socket正在监听连接请求**。

### ss命令——查看网络端口信息


**ss命令和netstat功能类似，但它能显示更多更详细的网络连接信息，比netstat更快更高效**

安装ss命令：

```
yum -y install iproute



# 列出所有处于监听状态的端口信息
[root@localhost ~]# ss -lntup

# 列出所有处于监听和非监听状态的端口信息
[root@localhost ~]# ss -antup
```

### ping命令——测试主机之间网络的连通性


ping命令发出请求后，远端主机网络联通的话，就会收到回应消息。可判断主机是否正常或两者的网络是否可以互通。


```

-c <次数>	指定发送ICMP报文的次数，默认一直发送报文
-i <时间>  发送报文的间隔时间，默认是1s
-t <生存期>  设置发送的数据包其生存期TTL值

# 直接ping域名
[root@localhost ~]# ping harbor.liyb.com

# 测试IP地址
[root@localhost ~]# ping 172.16.1.27

# 每次ping间隔2秒，一共5次。
[root@localhost ~]# ping -t 2 -c 5 172.16.1.27
PING 172.16.1.27 (172.16.1.27) 56(84) bytes of data.
64 bytes from 172.16.1.27: icmp_seq=1 ttl=64 time=0.029 ms
64 bytes from 172.16.1.27: icmp_seq=2 ttl=64 time=0.032 ms
64 bytes from 172.16.1.27: icmp_seq=3 ttl=64 time=0.032 ms
64 bytes from 172.16.1.27: icmp_seq=4 ttl=64 time=0.028 ms
64 bytes from 172.16.1.27: icmp_seq=5 ttl=64 time=0.026 ms


--- 172.16.1.27 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 3999ms
rtt min/avg/max/mdev = 0.026/0.029/0.032/0.005 ms
```

**注意：如果不加-c参数会一直ping下去，这时需要使用Ctrl + c来终止。**

### traceroute命令——追踪数据传输路由状态

traceroute命令和window下的tracert命令类似，用于显示网络数据包传输到指定主机的路径信息

安装traceroute：

```
yum install -y traceroute


traceroute www.baidu.com
```

注意：

1、有时会看到一些星号。可能是因为网络设备封堵或丢弃了返回的信息，所以得不到返回信息。

**2、traceroute默认使用UDP协议（受网络影响性能不太好），可以使用-I参数来调用icmp协议。**

### telnet命令——远程登录主机或检测远程端口

telnet命令使用23端口进行远程，但是使用明文传输，安全性不好。**目前远程连接一般使用安全性更好的SSH服务。现在telnet的主要应用场景是判断远端服务器的端口是否开放**。

操作：

```
# 端口放通的现象
[root@localhost ~]# telnet 172.16.1.20 22
Trying 172.16.1.20...
Connected to 172.16.1.20.
Escape character is '^]'.
SSH-2.0-OpenSSH_7.4   # 看到这种情况，代表22端口是放通的。
# 端口无法连接的现象
[root@localhost ~]# telnet 172.16.1.20 3306
Trying 172.16.1.20...
telnet: connect to address 172.16.1.20: Connection refused
```

### ssh命令——远程登录主机


ssh命令可以使用ssh加密协议实现安全的远程登录服务器，实现对服务器的远程管理。

语法格式：

```
ssh  [选项]  [用户]@[主机名或IP地址]  [远程执行的命令]


# 首次连接会提示，再次连接就不会提示，输入正确密码就可以远程
[root@k8s-master03 ~]# ssh -p 22 root@172.16.1.20
The authenticity of host '172.16.1.20 (172.16.1.20)' can't be established.
ECDSA key fingerprint is SHA256:ANt+WLWZpyB8YH14ROYVMTS68fEcEqoIrdVAi2FtwvU.
ECDSA key fingerprint is MD5:d0:f1:19:71:df:cb:39:b3:b2:cb:9a:83:39:f2:05:cb.
Are you sure you want to continue connecting (yes/no)?  yes
Warning: Permanently added '172.16.1.20' (ECDSA) to the list of known hosts.
root@172.16.1.20's password:



# 远程执行命令
[root@localhost ~]# ssh root@172.16.1.20 "free -h"
root@172.16.1.20's password:    # 输入正常密码
              total        used        free      shared  buff/cache   available
Mem:            15G        1.4G        2.4G        743M         11G         13G
Swap:            0B          0B          0B

# 调试
[root@localhost ~]# ssh -v root@172.16.1.20
```

### wget命令——命令行下载工具

wget命令用于从网络上下载某些资料，可以直接从网络上下载自己的所需要的文件

```

-o  将文件的执行结果写入文件中
-O	指定保存的文件名后下载文件
-c	断点续传
--limit-rate	  限速下载


操作：

# 下载单个文件，直接后面加链接
wget https://dlcdn.apache.org/tomcat/tomcat-8/v8.5.98/src/apache-tomcat-8.5.98-src.tar.gz

# 指定保存的文件后下载
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo

# 限速下载
wget  --limit-rate=5k -O  /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
```


### nslookup命令——域名解析查询工具

```
nslookup [选项]  [域名/IP]  [DNS服务器]


# 查看DNS配置文件
[root@localhost ~]# cat /etc/resolv.conf     
# Generated by NetworkManager
search liyb.com
nameserver 172.16.1.27


# 交互模式
[root@localhost ~]# nslookup
> server    172.16.1.27    # 指定DNS服务器
Default server: 172.16.1.27
Address: 172.16.1.27#53
> harbor.liyb.com    # 解析域名
Server:         172.16.1.27
Address:        172.16.1.27#53

Name:   harbor.liyb.com
Address: 172.16.1.26


# 非交互模式
[root@localhost ~]# nslookup harbor.liyb.com
Server:         172.16.1.27
Address:        172.16.1.27#53

Name:   harbor.liyb.com
Address: 172.16.1.26
```
### dig命令——域名查询工具

**dig命令用于测试域名系统的工作是否正常**

```
@<DNS的IP地址＞	指定DNS服务器来进行解析
-t	指定要查询的DNS数据类型
＋trace	从根域名开始跟踪查询结果
+short  仅输出最精简的CNAME和A记录
```

```

# 查询指定域名的IP地址
[root@localhost ~]# dig harbor.liyb.com


; <<>> DiG 9.11.4-P2-RedHat-9.11.4-26.P2.el7 <<>> harbor.liyb.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 6359
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 2


;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;harbor.liyb.com.               IN      A


;; ANSWER SECTION:
harbor.liyb.com.        86400   IN      A       172.16.1.26


;; AUTHORITY SECTION:
liyb.com.               86400   IN      NS      www.liyb.com.


;; ADDITIONAL SECTION:
www.liyb.com.           86400   IN      A       172.16.1.27


;; Query time: 0 msec
;; SERVER: 172.16.1.27#53(172.16.1.27)
;; WHEN: 五 1月 26 12:04:32 CST 2024
;; MSG SIZE  rcvd: 94
# 指定DNS服务器
[root@localhost ~]# dig @172.16.1.27 harbor.liyb.com

# 精简输出

[root@localhost ~]# dig +short harbor.liyb.com
172.16.1.26
```

### tcpdump命令——监听网络流量

tcpdump命令是一个截获网络数据包的包分析工具。tcpdump可以将网络中传送的数据包的“头”完全截获下来以提供分析。

```
-c <数量>  接收到指定的数据包数目后退出
-i <网络接口>  指定要监听的网络接口
-n 不进行DNS解析，加快显示速度
-nn 不将协议和端口数字等转换成名字
-q  以更快速输出的方式运行。
-v  显示命令执行的详细信息
```

```
# 不加参数将启动监视第一个网络接口所流过的数据包
[root@localhost ~]# tcpdump

# 指定网络接口进行监听
[root@localhost ~]# tcpdump -i eth0

# 指定监听主机的数据包
[root@localhost ~]# tcpdump -n host 172.16.1.26

# 监听指定端口的数据包
[root@localhost ~]# tcpdump -nn port 22
常见的协议关键字有ip、arp、icmp、tcp、udp等类型。

# 使用tcpdump对tcp数据进行抓包
[root@localhost ~]# tcpdump tcp dst port 80 or src 172.16.1.26 -i eth0 -n
```