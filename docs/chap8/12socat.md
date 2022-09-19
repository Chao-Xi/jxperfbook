# **12 Linux 命令 socat - netcat**

Socat或SOcket CAT是一个基于 Linux 命令行/终端的实用程序，用于在两个双向字节流之间建立和传输数据。

socat命令可以在多种场景下实现，主要有两个原因：


* **数据接收器和数据源；不同类型并存在于一个大集合中，可用于构造流**。
* 这些构造的流可以与许多地址选项相关联。



socat命令可以比作与TCP和UDP协议绑定的netcat 实用程序。

但是，socat比netcat具有安全优势（chrooting），并且还支持设备、管道、文件、SSL、SOCKS4 客户端、TCP 套接字、代理 CONNECT、UNIX 套接字等。

## **Socat 命令**


为了更熟悉这个 Linux 命令行实用程序，我们需要列出它的一些实际应用。以下要点总结了一些流行的 socat 实用程序应用程序：

* 安全测试和研究。
* 面向 TCP 的程序进行串行线路重定向。
* 作为 UNIX 套接字 shell 接口。
* 建立 su 和 chroot 安全环境以在共享网络连接上执行服务器/客户端 Shell 脚本。
* 不同计算机上串行线路的逻辑连接。
* IP6 relay。
* 通过攻击弱防火墙进行安全测试。
* TCP 端口转发。

## **在 Linux 中安装 Socat 实用程序**

如果您的 Linux 操作系统发行版上尚未安装基于socat Linux 命令行的实用程序，请参考您正在使用的 Linux 操作系统发行版参考以下安装命令之一：

```
$ sudo apt install socat         [在 Debian, Ubuntu 和 Mint 上]
$ sudo yum install socat         [在 RHEL/CentOS/Fedora 和 Rocky Linux/AlmaLinux 上]
$ sudo emerge -a net-misc/socat  [在 Gentoo Linux 上]
$ sudo pacman -S socat           [在 Arch Linux 上]
$ sudo zypper install socat      [在  OpenSUSE 上]
```

正如已经讨论过的，socat是netcat实用程序的出色替代品，因为它具有强大和高级的功能。我们现在应该能够通过 Linux 命令行环境看到一些使用socat实用程序的实际示例。

**其使用语法如下：**

```
# socat [options] <address> <address>
```

确保您在 Linux 机器上拥有 sudoer/root 用户权限。

**1、监听特定端口**


我们可以指示socat通过TCP协议监听特定端口，例如80 ，并通过STDOUT打印出任何相关的发现，如下所示。


```
$ sudo socat TCP4-LISTEN:80 STDOUT
```

TCP可以切换到其他不同的值，例如TCP6、TCP6-LISTEN和TCP4。


**2. 连接到远程服务器的端口**

要连接到与端口关联的服务器，我们将运行：

```
$ sudo socat – TCP4:linuxmi.com:80
```

**3. TCP 端口转发器**

它也是一个有效的TCP端口转发器。例如，端口81连接可以转发到端口80，如下所示：

对于单个连接。

```
$ sudo socat TCP4-LISTEN:81 TCP4:192.168.122.1:80
```

对于多个连接。

```
$ sudo socat TCP4-LISTEN:81,fork,reuseaddr TCP4:TCP4:192.168.122.1:80
```

您可以使用键盘组合取消端口转发[Ctrl]+c。

**4.监听本地端口**

监听本地端口www。

```
$ sudo socat TCP4-LISTEN:www TCP4:linuxmi.com:www
```

**5. 监听远程套接字上的特定端口**

如果我们想监听一个特定的端口，接受它的连接并将它转发到一个远程的 Unix 套接字，例如 mysql.sock，我们会以如下方式实现 socat 命令：

```
$ sudo socat TCP-LISTEN:3309,reuseaddr,fork UNIX-CONNECT:/var/lib/mysql/mysql.sock
```

**6. 基于网络的消息收集器**


这个简单的例子演示了基于网络的消息收集器的实现。客户端连接到端口 3354 成功后，文件/tmp/testing.log通过新生成的子进程附加客户端发送的数据。当发现此文件不存在时， socat会自动创建此文件。

```
$ sudo socat -u TCP4-LISTEN:3354,reuseaddr,fork OPEN:/tmp/testing.log,creat,append
```

```
$man socat
```