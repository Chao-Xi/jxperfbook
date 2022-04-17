# **L14 Linux iptables命令详解**

iptables 是 Linux 防火墙系统的重要组成部分，iptables 的主要功能是实现对网络数据包进出设备及转发的控制。

**当数据包需要进入设备、从设备中流出或者由该设备转发、路由时，都可以使用 iptables 进行控制。**

## **iptables简介**

iptables 是集成在 Linux 内核中的包过滤防火墙系统。**使用 iptables 可以添加、删除具体的过滤规则，iptables 默认维护着 4 个表和 5 个链，所有的防火墙策略规则都被分别写入这些表与链中。**

“四表”是指 iptables 的功能，默认的 iptables 规则表有 **filter 表（过滤规则表）、nat 表（地址转换规则表）、mangle（修改数据标记位规则表）、raw（跟踪数据表规则表）**：

* **filter 表：控制数据包是否允许进出及转发**，可以控制的链路有 INPUT、FORWARD 和 OUTPUT。
* **nat 表：控制数据包中地址转换**，可以控制的链路有 PREROUTING、INPUT、OUTPUT 和 POSTROUTING。
* **mangle：修改数据包中的原数据**，可以控制的链路有 PREROUTING、INPUT、OUTPUT、FORWARD 和 POSTROUTING。
* **raw：控制 nat 表中连接追踪机制的启用状况，可以控制的链路有 PREROUTING、OUTPUT**。

“五链”是指内核中控制网络的 NetFilter 定义的 5 个规则链。每个规则表中包含多个数据链：INPUT（入站数据过滤）、OUTPUT（出站数据过滤）、FORWARD（转发数据过滤）、PREROUTING（路由前过滤）和POSTROUTING（路由后过滤），防火墙规则需要写入到这些具体的数据链中。

Linux 防火墙的过滤框架，如图 1 所示。


![Alt Image Text](../images/chap6_14_1.png "Body image")

可以看出，

* **如果是外部主机发送数据包给防火墙本机**，数据将会经过 **PREROUTING** 链与 INPUT 链；
* **如果是防火墙本机发送数据包到外部主机**，数据将会经过 **OUTPUT 链与 POSTROUTING 链**；
* **如果防火墙作为路由负责转发数据**，则数据将经过 **PREROUTING 链、FORWARD 链以及 POSTROUTING 链**。

## **iptables语法格式**

```
[root@bogon ~]# iptables [-t table] COMMAND [chain] CRETIRIA -j ACTION
```

各参数的含义为：

* `-t`：**指定需要维护的防火墙规则表 filter、nat、mangle或raw**。**在不使用 `-t` 时则默认使用 filter 表**。
* `COMMAND`：子命令，定义对规则的管理。
* `chain`：指明链表。
* `CRETIRIA`：匹配参数。
* `ACTION`：触发动作。

iptables 命令常用的选项及各自的功能如表 2 所示。


![Alt Image Text](../images/chap6_14_2.png "Body image")

iptables 命令常用匹配参数及各自的功能如表 3 所示。


内核会按照顺序依次检查 iptables 防火墙规则，如果发现有匹配的规则目录，则立刻执行相关动作，停止继续向下查找规则目录；

如果所有的防火墙规则都未能匹配成功，则按照默认策略处理。

使用 `-A` 选项添加防火墙规则会将该规则追加到整个链的最后，而使用 `-I` 选项添加的防火墙规则则会默认插入到链中作为第一条规则。

> 注意，在 Linux CentOS 系统中，iptables 是默认安装的，如果系统中没有 iptables 工具，可以先进行安装。

## **规则的查看与清除**

使用 iptables 命令可以对具体的规则进行查看、添加、修改和删除

### **1) 查看规则**

对规则的查看需要使用如下命令：

```
[root@bogon ~]# iptables -nvL
```

各参数的含义为：

* **`-L` 表示查看当前表的所有规则，默认查看的是 filter 表，如果要查看 nat 表，可以加上 `-t nat`参数**。
* **`-n` 表示不对 IP 地址进行反查，加上这个参数显示速度将会加快。**
* **`-v` 表示输出详细信息，包含通过该规则的数据包数量、总字节数以及相应的网络接口**。

**【例 1】查看规则。**

首先需要使用 su 命令，切换当前用户到 root 用户。然后在终端页面输入命令如下：

```
[root@bogon ~]# iptables -L
Chain INPUT (policy ACCEPT)
target     prot opt source               destination        
ACCEPT     all  --  anywhere             anywhere            state RELATED,ESTABLISHED
ACCEPT     icmp --  anywhere             anywhere           
ACCEPT     all  --  anywhere             anywhere           
ACCEPT     tcp  --  anywhere             anywhere            state NEW tcp dpt:ssh
REJECT     all  --  anywhere             anywhere            reject-with icmp-host-prohibited
......
```

### **2) 添加规则**


**【例 2】查看当前规则。**

首先需要使用 su 命令，切换当前用户到 root 用户，然后在终端页面输入命令如下：

```
[root@bogon ~]# iptables -nL --line-number
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination        
1    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           state RELATED,ESTABLISHED
2    ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0          
3    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0          
4    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:22
5    REJECT     all  --  0.0.0.0/0            0.0.0.0/0           reject-with icmp-host-prohibited
......
```

**【例 3】添加一条规则到尾部。**

首先需要使用 su 命令，切换当前用户到 root 用户，然后在终端页面输入如下命令：

```
[root@bogon ~]# iptables -A INPUT -s 192.168.1.5 -j DROP


[root@bogon ~]# iptables -nL --line-number
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination        
1    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           state RELATED,ESTABLISHED
2    ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0          
3    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0          
4    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:22
5    REJECT     all  --  0.0.0.0/0            0.0.0.0/0           reject-with icmp-host-prohibited
6    DROP       all  --  192.168.1.5          0.0.0.0/0    
```

**3) 修改规则**

在修改规则时需要使用`-R` 参数。

**【例 4】把添加在第 6 行规则的 DROP 修改为 ACCEPT。**

首先需要使用 su 命令，切换当前用户到 root 用户，然后在终端页面输入如下命令：

```
[root@bogon ~]# iptables -R INPUT 6 -s 194.168.1.5 -j ACCEPT
[root@bogon ~]# iptables -nL --line-number
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination        
1    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           state RELATED,ESTABLISHED
2    ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0          
3    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0          
4    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:22
5    REJECT     all  --  0.0.0.0/0            0.0.0.0/0           reject-with icmp-host-prohibited
6    ACCEPT     all  --  194.168.1.5          0.0.0.0/0   
```

对比发现，第 6 行规则的 target 已修改为 ACCEPT。

### **4) 删除规则**

删除规则有两种方法，但都必须使用 -D 参数。

**【例 5】删除添加的第 6 行的规则。**

首先需要使用su命令，切换当前用户到 root 用户，然后在终端页面输入如下命令：

```
[root@bogon ~]# iptables -D INPUT 6 -s 194.168.1.5 -j ACCEPT
```
或

```
# iptables -D INPUT 6
```

> 注意，有时需要删除的规则较长，删除时需要写一大串的代码，这样比较容易写错，这时可以先使用 -line-number 找出该条规则的行号，再通过行号删除规则。

## **防火墙的备份与还原**

默认的 iptables 防火墙规则会立刻生效，但如果不保存，当计算机重启后所有的规则都会丢失，所以对防火墙规则进行及时保存的操作是非常必要的。

iptables 软件包提供了两个非常有用的工具，我们可以使用这两个工具处理大量的防火墙规则。**这两个工具分别是 `iptables-save` 和 `iptables-restore`，使用该工具可以实现防火墙规则的保存与还原。这两个工具的最大优势是处理庞大的规则集时速度非常快**。

CentOS 7 系统中防火墙规则默认保存在 `/etc/sysconfig/iptables` 文件中，使用 `iptables-save` 将规则保存至该文件中可以实现保存防火墙规则的作用，计算机重启后会自动加载该文件中的规则。

如果使用 `iptables-save` 将规则保存至其他位置，可以实现备份防火墙规则的作用。当防火墙规则需要做还原操作时，可以使用 `iptables-restore` 将备份文件直接导入当前防火墙规则。


### **1、iptables-save命令**

`iptables-save` 命令用来批量导出 Linux 防火墙规则，语法介绍如下：

保存在默认文件夹中（保存防火墙规则）：

```
[root@bogon ~]# iptables-save > /etc/sysconfig/iptables
```

保存在其他位置（备份防火墙规则）：

```
[root@bogon ~]# iptables-save > 文件名称
```

1) 直接执行 iptables-save 命令：显示出当前启用的所有规则，按照 raw、mangle、nat、filter 表的顺序依次列出，如下所示：

```
[root@bogon ~]# iptables-save
# Generated by iptables-save v1.4.7 on Thu Aug 27 07:06:36 2020
*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [602:39026]
.......
COMMIT
# Completed on Thu Aug 27 07:06:36 2020
```

其中：

* `“#”`号开头的表示注释；
* `“*filter”`表示所在的表；
* “`：`链名默认策略”表示相应的链及默认策略，具体的规则部分省略了命令名“iptables”；
* 在末尾处“COMMIT”表示提交前面的规则设置。

**2) 备份到其他文件中。例如文件：text，如下所示：**

```
[root@bogon ~]# iptables-save > test
[root@bogon ~]# ls
test
[root@bogon ~]# cat test
# Generated by iptables-save v1.4.7 on Thu Aug 27 07:09:47 2020
*filter
......
```

**3) 列出nat表的规则内容，命令如下**：

```
[root@bogon ~]# iptables-save -t nat
```

“-t表名”：表示列出某一个表。

### **2、iptables-restore命令**

iptables-restore 命令可以批量导入Linux防火墙规则，同时也需要结合重定向输入来指定备份文件的位置。命令如下：

```
[root@bogon ~]# iptables-restore < 文件名称
```

> 注意，导入的文件必须是使用 iptables-save工具导出来的才可以。

先使用`iptables-restore` 命令还原 text 文件，然后使用 iptables -t nat -nvL 命令查看清空的规则是否已经还原，如下所示：

```
[root@bogon ~]# iptables-restore < test
[root@bogon ~]# iptables -t nat -nvL
Chain PREROUTING (policy ACCEPT 0 packets, 0 bytes)
pkts bytes target     prot opt in     out     source               destination        

Chain POSTROUTING (policy ACCEPT 0 packets, 0 bytes)
pkts bytes target     prot opt in     out     source               destination        

Chain OUTPUT (policy ACCEPT 0 packets, 0 bytes)
pkts bytes target     prot opt in     out     source               destination        
```

