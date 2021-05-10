# **L4 运维必须掌握的Linux面试题**

## **1、解释下什么是GPL,GNU,自由软件？**

* **GPL：（通用公共许可证**）：一种授权，任何人有权取得、修改、重新发布自由软件的权力
* **GNU:(革奴计划)**：目标是创建一套完全自由、开放的的操作系统。
* **自由软件**：是一种可以不受限制地自由使用、复制、研究、修改和分发的软件。主要许可证有GPL和BSD许可证两种


## **2、linux系统里，buffer和cache如何区分？**

**buffer和cache都是内存中的一块区域，**


* **当CPU需要写数据到磁盘时，由于磁盘速度比较慢，所以CPU先把数据存进buffer**，
	* 然后CPU去执行其他任务，buffer中的数据会定期写入磁盘；

* **当CPU需要从磁盘读入数据时，由于磁盘速度比较慢**，
	* **可以把即将用到的数据提前存入cache，CPU直接从Cache中拿数据要快的多**。


## **3、描述Linux运行级别0-6的各自含义**

* 0：关机模式
* 1：单用户模式<==破解root密码
* 2：无网络支持的多用户模式
* 3：有网络支持的多用户模式（文本模式，工作中最常用的模式）
* 4：保留，未使用
* 5：有网络支持的X-windows支持多用户模式（桌面）
* 6: 重新引导系统，即重启


## **4、描述Linux系统从开机到登陆界面的启动过程**

* ⑴开机BIOS自检，加载硬盘。
* ⑵读取MBR,MBR引导。
* ⑶grub引导菜单(Boot Loader)。
* ⑷加载内核kernel。
* ⑸启动init进程，依据inittab文件设定运行级别
* ⑹init进程，执行rc.sysinit文件。
* ⑺启动内核模块，执行不同级别的脚本程序。
* ⑻执行/etc/rc.d/rc.local
* ⑼启动mingetty，进入系统登陆界面。

## **5、描述Linux下软链接和硬链接的区别**

在Linux系统中，链接分为两种，一种是硬链接（Hard link），另一种称为符号链接或软链接（Symbolic Link）。


* ①**默认不带参数的情况下，ln创建的是硬链接，带-s参数的ln命令创建的是软链接**。
* ②**硬链接文件与源文件的inode节点号相同，而软链接文件的inode节点号，与源文件不同**，
* ③**ln命令不能对目录创建硬链接，但可以创建软链接。对目录的软链接会经常使用到**。
* ④删除软链接文件，对源文件和硬链接文件无任何影响。
* ⑤删除文件的硬链接文件，对源文件及软链接文件无任何影响。
* ⑥删除链接文件的源文件，对硬链接文件无影响，会导致其软链接失效（红底白字闪烁状）。
* ⑦**同时删除源文件及其硬链接文件，整个文件才会被真正的删除**。
* ⑧**很多硬件设备的快照功能，使用的就是类似硬链接的原理**。
* ⑨软链接可以跨文件系统，硬链接不可以跨文件系统。

## **6、shell脚本中`“$?”`标记的用途是什么?**

在写一个shell脚本时，如果你想要检查前一命令是否执行成功，**在if条件中使用`“$?”`可以来检查前一命令的结束状态**。简单的例子如下：

```
root@localhost:~# ls /usr/bin/
root@localhost:~# echo $?
0

如果结束状态是0，说明前一个命令执行成功。
root@localhost:~# ls /usr/bin/share
ls: cannot access /usr/bin/share: No such file or directory

root@localhost:~# echo $?
2
如果结束状态不是0，说明命令执行失败。
```

## **7、如何让history命令显示具体时间？**

```
$ HISTTIMEFORMAT="%Y-%m-%d %H:%M:%S"

$ export HISTTIMEFORMAT

重新开机后会还原，可以写／etc／profile
```

**8、用shell统计ip访问情况，要求分析nginx访问日志，找出访问页面数量在前10位的IP数。以下是nginx的访问日志节选**


```
202.101.129.218- - [26/Mar/2006:23:59:55 +0800] "GET /online/stat_inst.php?pid=d065HTTP/1.1" 302 20-"-" "-" "Mozilla/4.0(compatible; MSIE 6.0; Windows NT 5.1)"

# 请写shell实现输出top10的IP列表。

$ awk '{print $1}' access.log |sort|uniq -c |head -n 10

31 202.101.129.218
21 123.93.29.11
11 13.92.19.31
```

## **9、将本地的80端口的请求转发到8080端口，本机地址10.0.0.254，写出命令**

```
$ iptables -t nat -A PREROUTING -d 10.0.0.254 -p tcp --dprot 80 -j DNAT --to-destination 10.0.0.254:8080
```

## **10、Load过高的可能性有哪些？**

### **10-1 排查思路**

* 1. 首先排查哪些进程cpu占用率高。**通过命令 ps aux**
* 2、假如通过第一步看到一个JAVA进程占有资源比较高，查看对应java进程的每个线程的CPU占用率。**通过命令：`ps -Lp 15047`**
* 3、**追踪线程内部，查看load过高原因。通过命令：jstack 15047**


排查思路：
1. 首先排查哪些进程cpu占用率高。通过命令 ps ux
2、假如通过第一步看到一个JAVA进程占有资源比较高，查看对应java进程的每个线程的CPU占用率。通过命令：ps -Lp 15047
3、追踪线程内部，查看load过高原因。通过命令：jstack 15047

### **10-2 其他经验**：


cpu load的飙升，一方面可能和full gc的次数增大有关，一方面可能和死循环有关


## **11、描述`/etc/fstab` 文件中每个字段的含义？**

* (1）第一列：将被加载的文件系统名；
* （2）第二列：该文件系统的安装点；
* （3）第三列：文件系统的类型；
* （4）第四列：设置参数；
* （5）第五列：供备份程序确定上次备份距现在的天数；
* （6）第六列：在系统引导时检测文件系统的顺序。


## **12、 如何在打包时排除指定目录?**

```
$ tar --exclude=/home/dmtsai --exclude=*.tar -zcvf myfile.tar.gz /home/* /etc
```

## **13. 怎么生成随机密码?**

```
mkpasswd -l 8  -C 2 -c 2 -d 4 -s 0
```

## **14、如何统计tcp状态？**

```
netstat -an | grep '^tcp' | awk '{++s[$NF]} END{for(i in s) print i "\t"s[i]}'
```

## **15、mysql 忘记 root 密码怎么办**

```
$ mysqld_safe --user=mysql --skip-grant-tables --skip-networking & use mysql;

mysql> update user set password=password('123123') where user='root';
```
