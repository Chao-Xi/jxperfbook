# Linux File Operation (df/du/rsync)

## Linux中df和du命令的10个高级用法

`df -i`- 显示文件系统的inode使用情况，而不是磁盘空间使用情况。

```
$ df -i
```

输出示例：

```
Filesystem      Inodes  IUsed  IFree IUse% Mounted on
/dev/sda1      2621440 200000 2421440    8% /
tmpfs           102400      1 102399    1% /dev/shm
/dev/sdb1      6553600  50000 6503600    1% /data
```

**`df -T` - 显示文件系统类型。**


```
$ df -T
```

输出示例：

```
Filesystem     Type     1K-blocks    Used Available Use% Mounted on
/dev/sda1      ext4       20511360 9783776   9757632  51% /
tmpfs          tmpfs       3983616       0   3983616   0% /dev/shm
/dev/sdb1      ext4      102401280 50108800  52392304  50% /data
```

`df -h` --total - 显示总磁盘空间使用情况，包括所有文件系统。

```
$ df -h --total
```

输出示例：

```
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        20G   10G   10G  50% /
tmpfs           3.8G     0  3.8G   0% /dev/shm
/dev/sdb1       100G   48G   52G  48% /data
total           123G   58G   62G  48%
```

`df -m` - 以MB为单位显示磁盘空间使用情况。

```
$ df -m
```

```
Filesystem     1M-blocks  Used Available Use% Mounted on
/dev/sda1           20079  9553      9489  51% /
tmpfs                3894     0      3894   0% /dev/shm
/dev/sdb1           99968 48977     50815  50% /data
```

**`df -x` - 显示指定文件系统类型之外的文件系统。**

```
$ df -x tmpfs -x devtmpfs
```

输出示例：

```
Filesystem     1K-blocks    Used Available Use% Mounted on
/dev/sda1       20511360 9783776   9757632  51% /
/dev/sdb1      102401280 50108800  52392304  50% /data
```

`du -a` - 显示目录下所有文件和子目录的磁盘使用情况。

```
$ du -a /path/to/directory
```

输出示例：

```
4       /path/to/directory/file1.txt
8       /path/to/directory/file2.txt
12      /path/to/directory/subdirectory
24      /path/to/directory
```

`du -h --max-depth=1` - 以人类可读的格式显示目录下一级子目录的磁盘使用情况。

```
$ du -h --max-depth=1 /path/to/directory
```

输出示例：


```
4.0K    /path/to/directory/file1.txt
8.0K    /path/to/directory/file2.txt
12K     /path/to/directory/subdirectory
24K     /path/to/directory
```

**`du -shx` - 显示指定目录的磁盘使用情况，排除挂载的文件系统。**

```
$ du -shx /path/to/directory
```

输出示例：

```
24K     /path/to/directory
```

**`du -c` - 显示多个目录的总磁盘使用情况。**

```
$ du -c /path/to/directory1 /path/to/directory2
```

输出示例：

```
4       /path/to/directory1
12      /path/to/directory2
16      total
```

**`du -L` - 跟踪符号链接指向的文件或目录的磁盘使用情况。**

```
$ du -L /path/to/symlink
```

输出示例：

```
8       /path/to/symlink/file.txt
```


这些高级用法可以帮助您更全面地了解磁盘空间的使用情况，从而更好地管理和优化您的系统。无论是查看文件系统类型、检查inode使用情况还是定制化显示格式，df和du命令提供了丰富的选项来满足您的需求。

## Linux中比cp好用10倍的rsync

### 一、rsync命令概述

rsync（remote sync）是一个功能强大的开源工具，用于在本地和远程系统之间进行文件同步和备份。其最大的特点是能够只传输发生变化的部分文件，而不是整个文件，从而大大提高了传输效率。

**此外，rsync还支持压缩、加密等功能，使得文件同步更加灵活和安全**。

### rsync安装

在开始使用rsync之前，我们需要先确保系统中已经安装了rsync。对于大多数Linux发行版，rsync通常已经预装或者可以通过包管理器进行安装。如果您使用的是Mac系统，可以通过Homebrew来进行安装。以下是在Ubuntu系统上安装rsync的命令示例：

```
$ sudo apt-get update
$ sudo apt-get install rsync
```

### 三、基本用法示例

**3.1 本地文件同步**

首先，我们将介绍如何在本地机器之间进行文件同步。假设我们有两个目录：**源目录（source）和目标目录（destination）**。要将源目录中的文件同步到目标目录，可以使用以下命令：

```
$ rsync -avh source/ destination/
```

在上述命令中，

* `-a`参数表示以归档模式进行同步，保持文件的属性和权限不变；
* `-v`参数表示输出详细的同步过程信息；
* `-h`参数表示以人类可读的方式显示文件大小。

您可以根据实际需求调整参数。

**3.2 远程文件同步**

rsync也支持在本地机器和远程机器之间进行文件同步。在进行远程同步之前，需要确保远程机器上已经安装了rsync，并且可以通过SSH进行访问。

要将本地机器上的文件同步到远程机器，可以使用以下命令：

```
$ rsync -avh source/ user@remote:/path/to/destination/
```

在上述命令中，user是远程机器上的用户名，remote是远程机器的地址，`/path/to/destination/`是目标目录的路径。

通过SSH连接到远程机器时，rsync会要求输入密码或者使用SSH密钥进行身份验证。

**3.3 过滤文件和目录**

有时候，我们希望在文件同步过程中排除某些文件或目录，以避免不必要的复制。**rsync提供了`--exclude`和`--exclude-from`参数来实现文件和目录的过滤**。

以下是一个示例，演示如何使用--exclude参数来过滤文件：

```
$ rsync -avh --exclude='*.txt' source/ destination/
```

上述命令中，`--exclude='*.txt'`表示排除所有.txt文件。您可以根据实际需求修改过滤规则。

**3.4 同步删除**

如果源目录中的文件在目标目录中不存在，rsync默认不会删除目标目录中的文件。

如果希望在同步过程中删除目标目录中的文件，可以使用`--delete`参数：

```
$ rsync -avh --delete source/ destination/
```

上述命令中，`--delete`参数表示在同步过程中删除目标目录中不存在的文件。

### 四、高级功能

除了基本的文件同步功能外，rsync还提供了一些高级功能，使得文件同步更加灵活和可定制。

**4.1 压缩传输**

默认情况下，rsync会对传输的数据进行压缩，以减少网络带宽的占用。**如果希望禁用压缩，可以使用`-z`参数**

```
$ rsync -avh -z source/ destination/
```

**4.2 加密传输**

为了保护数据的安全性，rsync支持通过SSH进行加密传输。在远程同步中，只需使用SSH连接进行传输即可

**4.3 部分文件传输**

rsync的一个重要特点是只传输发生变化的部分文件，而不是整个文件。这种增量传输的方式大大提高了传输效率。rsync会根据文件的时间戳和内容来判断文件是否发生变化，只传输发生变化的部分。

**4.4 带宽限制**

如果您希望在进行文件同步时限制rsync使用的带宽，可以使用`--bwlimit`参数。以下是一个示例：

```
$ rsync -avh --bwlimit=1000 source/ destination/
```

上述命令中，`--bwlimit=1000`表示限制带宽为1000 KB/s。

