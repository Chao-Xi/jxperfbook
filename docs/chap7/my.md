
## 网络 / Network

### State the difference between GET and POST.

GET and POST are two different HTTP request methods.

**GET**

* This method is used to **request data from a certain
resource (via some API URL)**.
* If you use the GET method to send data, the data is added to the URL, and a URL can be up to 2048 characters in length. Therefore, it has restrictions on data length.
* In comparison to POST, **GET is less secure since data issent as part of the URL**. Passwords and other sensitive information should never be sent using GET.
* Everyone can see the data in the URL

**POST**

* This method is used to send or write data to be processed to a specific resource (via some API URL).
* It does not impose such limitations
* It is a little safer to use POST than GET because the parameters are not saved in the browser history or the web server logs.
* There is no data displayed in the URL




* 从功能上讲，GET一般用来从服务器上获取资源，POST一般用来更新服务器上的资源；
* 从REST服务角度上说，GET是幂等的，即读取同一个资源，总是得到相同的数据，而POST不是幂等的，因为每次请求对资源的改变并不是相同的；进一步地，GET不会改变服务器上的资源，而POST会对服务器资源进行改变；
* 从请求参数形式上看，GET请求的数据会附在URL之后，即将请求数据放置在HTTP报文的 **请求头**中，以?分割URL和传输数据，参数之间以&相连。特别地，如果数据是英文字母/数字，原样发送；否则，会将其编码为 `application/x-www-form-urlencoded` MIME 宇符串(如果是空格，转换为+，如果是中文/其他宇符，则直接把宇符串用BASE64加密，得出如：`%E4%BD%A0%E5%A5%BD`，其中%××中的XX为该符号以16进制表示的ASCI)；而POST请求会把提交的数据则放置在是HTTP请求报文的 请就安全性而言，POST的安全性要比GET的安全性高，因为GET请求提交的数据将明文出现在URL上，而POST请求参数则被包装到**请求体**中，相对更安全
* 从请求的大小看，GET请求的长度受限于浏览器或服务器对URL长度的限制，允许发送的数据量比较小， 而POST请求则是没有大小限制的


### **三次握手与四次挥手**

* 第一次握手：客户端发送syn包(syn=j)到服务器，并进入`SYN_SEND`状态，等待服务器确认；
* 第二次握手：服务器收到syn包，必须确认客户的SYN（ack=j+1），同时自己也发送一个SYN包（syn=k），即SYN+ACK包，此时服务器进入SYN_RECV状态；
* 第三次握手：客户端收到服务器的SYN＋ACK包，向服务器发送确认包ACK(ack=k+1)，**此包发送完毕，客户端和服务器进入ESTABLISHED状态，完成三次握手**。


### **HTTPS和HTTP的区别**


* https协议需要到ca申请证书或自制证书。
* http的信息是明文传输，https则是具有安全性的ssl加密。
* http是直接与TCP进行数据传输，而https是经过一层SSL（OSI表示层），用的端口也不一样，前者是80（需要国内备案），后者是443。
* http的连接很简单，是无状态的；HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，比http协议安全。

**注意https加密是在传输层**

https报文在被包装成tcp报文的时候完成加密的过程，无论是https的header域也好，body域也罢都是会被加密的。

###  Explain the Restful API and write its usage.


APIs (Application Programming Interfaces) are sets of rules and protocols that define how software programs or devices can communicate with each other. APIs that conform to the design principles of REST, or representational state transfer, are known as REST APIs. REST APIs may also be referred to as RESTful APIs. Using RESTful APIs, developers can create requests and receive responses via an HTTP request. REST API can also be used for mapping data from a cloud platform to a data warehouse or vice versa.



## Linux 

### **Difference hardlink and symlink**

系统是通过link的数量来控制文件删除的，只有当一个文件不存在任何link的时候，这个文件才会被删除。

一般来说每个文件两个link计数器来控制：`i_count`和`i_nlink`。

* 当一个文件被一个程序占用的时候`i_count`就加1。
* 当文件的硬链接多一个的时候`i_nlink`也加1。
* 删除一个文件，就是让这个文件，没有进程占用，同时`i_link`数量为0。
* 默认不带参数的情况下，ln创建的是硬链接，带-s参数的ln命令创建的是软链接。
* 硬链接文件与源文件的inode节点号相同，而软链接文件的inode节点号，与源文件不同
* ln命令不能对目录创建硬链接，但可以创建软链接。对目录的软链接会经常使用到。
* 删除文件
	* 删除软链接文件，对源文件和硬链接文件无任何影响。
	* 删除文件的硬链接文件，对源文件及软链接文件无任何影响。
	* 删除链接文件的源文件，对硬链接文件无影响，会导致其软链接失效（红底白字闪烁状）
	* 同时删除源文件及其硬链接文件，整个文件才会被真正的删除。
* 很多硬件设备的快照功能，使用的就是类似硬链接的原理

### linux系统里，buffer和cache如何区分？

buffer和cache都是内存中的一块区域，

* 当CPU需要写数据到磁盘时，由于磁盘速度比较慢，所以CPU先把数据存进buffer，
* 当CPU需要从磁盘读入数据时，由于磁盘速度比较慢，可以把即将用到的数据提前存入cache，CPU直接从Cache中拿数据要快的多。


### **Linux 文件系统**

文件系统包含元数据（索引数据）以及数据内容（数据块）

**inode即为元数据，inode记录数据块的位置及索引信息（文件属性，ctime，atime，mtime等）**

* 文件的字节数，块数
* 文件拥有者的User ID
* 文件的Group ID
* 文件的读、写、执行权限
* 文件的时间戳，共有三个：ctime指inode上一次变动的时间，mtime指文件内容上一次变动的时间，atime指文件上一次打开的时间。
* 链接数，即有多少文件名指向这个inode
* 文件数据block的位置
* inode编号

### **监听端口**

侦听端口是应用程序或进程在其上侦听的网络端口，充当通信端点。

每个监听端口都可以使用防火墙打开或关闭（过滤）。一般而言，开放端口是一个网络端口，它接受来自远程位置的传入数据包。

```
sudo netstat -tunlp
```

**简述 HTTPS 的加密与认证过程**


### Git flow

git cherry pick

**Git flow   /   github flow / giltab flow**


## Others

### **In the past, what was the best implementation or debugging you did?**


By asking this question, the hiring manager will get an idea of the type and complexity of past projects you have completed. You should clearly state the problems you encountered and the steps you took to overcome them. In addition, you can talk about the lessons you learned from the issue.






### DataBase


* 请描述MySQL 中 几种join的方式？ join 与 left join 的区别是什么


	* left join: 
	* join(inner join):



**如何显示前50行？**

在Mysql中，使用以下代码查询显示前50行：

`SELECT * FROM LIMIT 0,50;`



**实践中如何优化MySQL**

最好是按照以下顺序优化：

* 1.SQL语句及索引的优化
* 2. 数据库表结构的优化
* 3.系统配置的优化
* 4.硬件的优化


* 聚簇(cu)索引和非聚簇(cu 4)索引有什么区别？

主键索引叫做“聚集索引”，其他索引叫做“非聚集索引”。后者通过索引记录值找到主键，随后再通过聚集索引找到对应的记录。因此“聚集索引（主键）是通往真实数据所在的唯一路径”

**聚簇索引与非聚簇索引的区别**

聚簇索引是将索引和整条记录存放在一起，找到索引就找到了记录；
非聚簇索引只存储索引字段和记录所在的位置，通过索引找到记录所在的位置，然后再根据记录所在位置去获取记录。

一般来讲一堆数据记录最多只能有一个聚簇索引，但可以有很多非聚簇索引；


两者的优缺点对比

* 聚簇索引的查找记录要比非聚簇索引块，因为聚簇索引查找到索引就查找到了数据位置，而非聚簇索引查找到索引之后，根据记录的数据地址，再去查找数据；
* 一个数据表只能有一个聚簇索引，但可以有多个非聚簇索引；
* 聚簇索引和非聚簇索引都可以加快查询速度，但同时也都对写入速度会有影响；聚簇索引对写入的速度影响更大一些；


两者使用场景

InnoDB的主键使用的都是聚簇索引，而MyASM无论是主键索引还是二级索引，使用的都是非聚簇索引。



### **数据库中的事务是什么?**

事务（transaction）是作为一个单元的一组有序的数据库操作。如果组中的所有操作都成功，则认为事务成功，即使只有一个操作失败，事务也不成功。如果所有操作完成，事务则提交，其修改将作用于所有其他数据库进程。如果一个操作失败，则事务将回滚，该事务所有操作的影响都将取消。

事务特性：

* （1）原子性：即不可分割性，事务要么全部被执行，要么就全部不被执行。
* （2）一致性或可串性。事务的执行使得数据库从一种正确状态转换成另一种正确状态
* （3）隔离性。在事务正确提交之前，不允许把该事务对数据的任何改变提供给任何其他事务，
* （4） 持久性。事务正确提交后，其结果将永久保存在数据库中，即使在事务提交后有了其他故障，事务的处理结果也会得到保存。


SQL注入漏洞产生的原因？如何防止？

SQL注入产生的原因：程序开发过程中不注意规范书写sql语句和对特殊字符进行过滤，导致客户端可以通过全局变量POST和GET提交一些sql语句正常执行。

**防止SQL注入的方式：**

* 	开启配置文件中的`magic_quotes_gpc` 和  `magic_quotes_runtime`设置
* 执行sql语句时使用addslashes进行sql语句转换
* Sql语句书写尽量不要省略双引号和单引号。
* 过滤掉sql语句中的一些关键词：update、insert、delete、select



提高数据库表和字段的命名技巧，对一些重要的字段根据程序的特点命名，取不易被猜到的。


能不能简述一下什么是行级锁和表级锁，各自有什么特点？

* 表级锁：开销小，加锁快；不会出现死锁；锁定粒度大，发生锁冲突的概率最高，并发度最低；
* 行级锁：开销大，加锁慢；会出现死锁；锁定粒度最小，发生锁冲突的概率最低，并发度也最高；    


**查看二进制日志工作模式**

* ROW: 基于行的复制
* Statement: 基于sql语句的复制
* mixed模式 ：row 与 statement 结合



### **什么情况下会造成数据库死锁？** 

当多个事务试图以不同的顺序锁定资源时，就可能会产生死锁。


* 图一中，有两个事务A与B，事务A与B同时执行sql1，则事务A会锁住记录B；
* 事务B会锁住记录A，两条记录同时被锁住。接下来事务A执行sql2，发现记录A被事务B锁住，事务B执行sql2发现记录B被事务A锁住，陷入死循环，产生死锁。


**[PostgreSQL Interview Questions 2022](https://chao-xi.github.io/jxdatabasebook/pg1/1pg_int/)**


