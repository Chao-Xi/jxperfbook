# 使用find命令轻松查找文件

## 1、基本用法

find命令的基本格式如下：

```
find [-H] [-L] [-P] [路径] [表达式]
```

其中，**[路径]是希望开始搜索的目录路径，如未指定，则从当前目录开始搜索，[表达式]则是搜索条件**。

另有以下不常用的选项：

* `-P`：不跟随符号链接，这是默认行为。
* `-L`：跟随符号链接。
* `-H`：除了处理命令行参数外，不跟随符号链接。如：`-newer`选项的参数是一个符号链接，此时会跟随符号链接。

当find检查或打印文件信息时，所使用的信息应来自符号链接本身的属性。这种行为的唯一例外是当命令行上指定的文件是符号链接，并且可以解析链接时。

对于这种情况，所使用的信息来自链接指向的任何内容（即，链接被跟随）。如果符号链接指向的文件无法检查，则使用链接本身的信息作为后备。如果`-H`有效，并且命令行上指定的路径之一是目录的符号链接，则将检查该目录的内容（当然，`-maxdepth 0` 将阻止这种情况）。

## 2、搜索条件

有些搜索选项后面可以指定数字参数，格式为：`[+|-]`n。

**+n表示大于n，-n表示小于n，无符号表示等于n**


### 1. 按文件名称查找

如果知道要查找的文件名称，可以直接使用该名称进行搜索。例如，要查找名为nginx的文件，可以使用以下命令：

```
find /path/to/search -name nginx
```

为了提高查找效率，应尽量缩小搜索范围，并指定尽可能明确的路径。


### 2. 按文件类型查找

上述查找也会找到名称为nginx的目录，可以使用-type选项只查找文件，使用以下命令

```
find /path/to/search -type f -name nginx
```

`-type`选项有以下文件类型参数值：

* b：块设备特殊文件
* c：字符设备特殊文件
* d：目录
* p：命名管道（FIFO）
* f：普通文件
* l：符号链接（字母L的小写）

如果-L选项有效，除非符号链接断开，否则这种情况永远不会发生。

如果要搜索对于`-L`有效时的符号链接，请使用`-xtype`。

* s：套接字

### 3. 按文件日期查找

使用`-mtime`选项，可以查找在特定天数之前修改过的文件。例如，要查找刚好在30天前修改过的文件，可以使用以下命令：

```
find /path/to/search -mtime 30
```

注意，-mtime后面的参数，30表示刚好在30天前，+30表示超过30天，-30表示30天之内。

当find计算出文件最后一次被修改的n天前的时间段时，任何小数部分都会被忽略，因此为了匹配`-mtime +1`，文件必须至少在两天前被修改过。

其它按时间查找的选项：

* `-atime`：查找在特定天数之前访问过的文件。
* `-ctime`：查找在特定天数之前更改过状态（如：修改权限）的文件。
* `-amin`、-cmin、-mmin：与-atime、-ctime、-mtime选项类似，区别是时间单位为分钟。
* `-newer reference_file`：查找修改时间比参考文件最后修改时间晚的文件。如果参考文件是一个符号链接，并且`-H`选项或`-L`选项有效，则始终使用它所指向的文件的最后一次数据修改时间。

### 4. 按文件大小查找

使用-size选项，可以查找大于或等于特定大小的文件。例如，要查找大于10M的文件，可以使用以下命令：

```
find /path/to/search -size +10M
```

+表示大于，-表示小于，参数值可以使用以下后缀表示文件大小单位：

* b：表示512字节块（如果不使用后缀，则为默认值）
* c：表示字节
* w：表示两个字节的单词
* k：表示千字节
* M：表示兆字节
* G：表示吉字节

### 5. 按其它条件查找

```
man find
```

## 三、日常应用场景

### 1. 查找并清理过时文件

在许多系统中，临时文件通常保存在`/tmp`目录下，我们自己也可以有专用于临时工作的目录`~/tmp`。

可以使用find命令来查找这些临时文件，并使用`-delete`选项来删除它们。例如，要查找30天之前的临时文件并删除它们，可以使用以下命令

```
find ~/tmp -type f -mtime +30 -delete
```

可以根据大概日期查找文件。例如，要查找过去20~30天访问过的文件，可以使用以下命令：

```
find ~/tmp -type f -mtime +20 -mtime -30
```


### 2. 查找大文件并清理

有时候，系统中可能会有一些大而无用的文件。可以使用find命令来查找这些大文件，并删除它们以释放空间。例如，要查找下载目录中大于500MB的文件并删除它们，可以使用以下命令：

```
find ~/Downloads -size +500M -delete
```

