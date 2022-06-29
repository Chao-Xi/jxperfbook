# **2 简洁的 Bash Programming 技巧续篇**


## **1. bash中alias的使用**

alias其实是给常用的命令定一个别名，比如很多人会定义一下的一个别名：

```
alias ll='ls -l'
```

以后就可以使用ll，实际展开后执行的是`ls -l`。现在很多发行版都会带几个默认的别名，比如：

```
alias grep='grep --color=auto'  # 带颜色显示

alias ls='ls --color=auto' # 同上

alias rm='rm -i'  # 删除文件需要确认
```

那么如何不要展开alias，而是用本来的意思呢？答案是使用转义：

```
\ls
\grep
```

在命令前面加一个反斜杠后就可以了。

这里要插一段故事，前两天我在shell脚本中定义了下面的一个alias，假设位于文件`util.sh`：

```
#!/bin/bash
...
alias ssh='ssh -o StrictHostKeyChecking=no -o LogLevel=quiet -o BatchMode=yes'
...
```

后面这串ssh选项是为了去掉一些warning的信息，不提示输入密码等等。

具体可以看ssh的文档说明。我自己测试的时候好好的，当时我同事跑得时候却依然有报Warning。我对比了下我们两个人的用法：

```
sh util.sh  # 我的
./util.sh   # 他的
```

修改下util.sh，打开这个选项就Ok了：

```
#!/bin/bash
...
# Expand aliases in script
shopt -s expand_aliases
alias ssh='ssh -o StrictHostKeyChecking=no -o LogLevel=quiet -o BatchMode=yes'
...
```

## **2. awk打印除第一列之外的其他列**

awk用来截取输入行中的某几列很有用，当时如果要排除某几列呢？

例如有如下的一个文件：

```
$ cat /tmp/test.txt
1 2 3 4 5

10 20 30 40 50
```

可以用下面的代码解决（来源）：

```
$ awk '{$1="";print $0}' /tmp/test.txt

 2 3 4 5
 20 30 40 50
```

但是前面多了一个空格，可以用cut命令稍微调整下：

```
$ awk '{$1="";print $0}' /tmp/test.txt | cut -c2-
2 3 4 5
20 30 40 50
```

## **3. 巧用bash的命令展开功能备份文件**

假设要备份文件`/your/path/to/file.list` 为 `/your/path/to/file.list.20121106`，常规的方法是：

```
cp /your/path/to/file.list /your/path/to/file.list.20121106
```

这样重复写上一长串的路径，是不是很麻烦，这里利用bash的展开特性可以这样做：

```
cp /your/path/to/file.list{,.20121106}
```

`/your/path/to/file.list{,.20121106}` 这一部分会展开为 `/your/path/to/file.list`  `/your/path/to/file.list.20121106` , 再将此传给cp命令，就达到了与前面同样的效果。（思路同ls *）。具体可以man bash中的Brace Expansion这一段。


## **4. 你知道sed的这个特性吗？**

假设一个文件的每一行为一个路径：

```
$ cat /tmp/test.txt
/home/kodango/hello
/home/kodango/hello/world
/home/kodango/good
/home/kodango/good/bye
```

现在要把`/home/kodango/good`替换成/`home/kodango/bad`，普通的作法是：

```
$ sed -n 's/\/home\/kodango\/good/\/home\/kodango\/bye/p' /tmp/test.txt 
/home/kodango/bye
/home/kodango/bye/bye
```

因为路径中的分隔符与sed的替换命令的分隔符都是'/'，所以需要转义，非常麻烦。幸运的是，sed可以更改分隔符，例如使用`#`：

```
$ sed -n 's#/home/kodango/good#/home/kodango/bad#p' /tmp/test.txt 
/home/kodango/bad
/home/kodango/bad/bye
```

**补充，如果是在地址对中使用，首个分隔符前面要加反斜杠：**

```
$ sed -n '\#/home/kodango/#p' /tmp/test.txt 
/home/kodango/hello
/home/kodango/hello/world
/home/kodango/good
/home/kodango/good/bye
```

## **5 合并连续重复的字符（即squeeze操作）**

例如要合并一个字符串中连续的多个空格，假设字符串为'print hello, world'。

第一种方法，使用sed命令，扫描整个字符串，替换2个以上的空格为1格：

```
$ echo 'print  hello,   world  ' | sed -r 's/ {2,}/ /g'
print hello, world 
```

第二种方法，使用tr命令的-s选项，专门就是为了合并连续重复的字符：

```
$ echo 'print  hello,   world  ' | tr -s ' '
print hello, world 
```

第三种方法，使用awk的域赋值来完成该目的：

```
$ echo 'print  hello,   world  ' | awk '$1=$1'
print hello, world
```

对已经存在的域例如`$1,$2.`.进行赋值，会导致awk重新使用OFS输出分隔符重组`$0`，


## **6. 将文本中某列相同的行输出到不同的文件中**

标题有点绕口，我们以实际例子来讲解，假设我们有以下的一个文件：

```
$ cat /tmp/test.txt
a char
1 int
2 int
b char
abc string
```

我们的目标是将该文本中的行按第二列的值归类，并且输出到相应的文件中，文件名为第二列的名称。例如第2行、第3行会输出到int.txt文件中，而第1行、第4行则输出到char.txt，以此类推。

我没有找到其它简单的方法，只找到一种用awk来处理的方法：

```
$ awk '{print $1 > $2 ".txt"}' /tmp/test.txt
```

我们来检查结果：

```
$ grep -nH . *
char.txt:1:a
char.txt:2:b
int.txt:1:1
int.txt:2:2
string.txt:1:abc
```

## **7. 用exec命令来完成重定向**

以一个简单的例子开始，现在需要一个脚本，它可以接受一个文件名作为参数，然后按行读取该文件的内容并打印到标准输出。如果不指定文件名，则默认从标准输入读。首先按上面的功能需求写出一个可以完成功能的脚本：

```
$ cat test.sh 

filename=$1

if [ -z "$filename" ]; then
    while read line; do
        echo $line
    done
else
    while read line; do
        echo $line
    done < $filename
fi
```

如果换exec来实现重定向，可以把脚本写得更优雅：

```
$ cat test1.sh 

filename=$1

if [ -n "$filename" ]; then
    exec 0< $filename
fi

while read line; do
    echo $line
done
```

这里的关键在第5行代码，exec命令不仅可以用于执行命令，**还可以用于打开、关闭或者复制文件描述符，这里就是利用exec将指定的文件名打开重定向到标准输入**。类似地可以用`exec >$filename`将文件重定向到标准输出。我们可以在命令行上做一个试验：

```
$ exec 3>&1                   # 首先将fd 3重定向到标准输出，作为标准输出的一个备份

$ ls /proc/629/fd/{1,3} -l    # 现在fd 3和fd 1指向同一个设备文件
lrwx------ 1 kodango kodango 64 Nov 10 00:26 /proc/629/fd/1 -> /dev/pts/1
lrwx------ 1 kodango kodango 64 Nov 10 00:26 /proc/629/fd/3 -> /dev/pts/1

$ exec >stdout               # 现在把标准输出重定向到stdout这个文件中

$ ls /proc/629/fd/1 -l        # 如果你此刻在同一个终端下执行本命令是没有返回的

$ ls /proc/629/fd/1 -l        # 现在重新打开一个终端看看，确实已经重定向到stdout这个文件
l-wx------ 1 kodango kodango 64 Nov 10 00:26 /proc/629/fd/1 -> /home/kodango/stdout

$ exec 1>&3                   # 现在重新把标准输出重定向到之前备份的fd 3上
$ ls /proc/629/fd/{1,3} -l  # 现在屏幕可以看到输出了，但是fd 3这个描述符还打开，需要关闭
lrwx------ 1 kodango kodango 64 Nov 10 00:26 /proc/629/fd/1 -> /dev/pts/1
lrwx------ 1 kodango kodango 64 Nov 10 00:26 /proc/629/fd/3 -> /dev/pts/1

$ exec 3>&-                   # 关闭fd 3
$ ls /proc/629/fd/3 -l
ls: cannot access /proc/629/fd/3: No such file or directory

$ cat stdout                  # 检查stdout文件，确实有之前被吃掉的输出
l-wx------ 1 kodango kodango 64 Nov 10 00:26 /proc/629/fd/1 -> /home/kodango/stdout
```

关于I/O重定向的更详细的说明，可以看[I/O Redirection](http://tldp.org/LDP/abs/html/io-redirection.html)，这里有很多例子讲解了各种I/O重定向的用法，包括exec来改变重定向。

这一点在`while read; do xxx; done < file`内部仍需要从标准输入读取内容时非常有用，此时必须要将循环外部的重定向和内部的剥离开来。

## **8 引号之间的区别**

Shell中比较让人抓狂的是各种引号的处理，其中，反引号(`cmd`)是最容易掌握的，它其实和`$(cmd)`是差不多的。

引号的作用有几点，一个是为了将多个因为空格或者回车等分隔符隔开的字符串合在一起，避免被命令行解析分开，例如`"one two three"`就是一整个字符串，而不是像one two three会被解析成三个单独的字符串；

另外一方面，引号可以让一些特殊符号保持原义。

其中，单引号的处理是比较简单的，被单引号包括的所有字符都保留原有的意思，例如`'$a'`不会被展开, '`cmd`'也不会执行命令；而双引号，则相对比较松，在双引号中，以下几个字符`$`, `, \`依然有其特殊的含义，比如`$`可以用于变量展开, 反引号```可以执行命令，反斜杠`\`可以用于转义。


但是，在双引号包围的字符串里，反斜杠的转义也是有限的，它只能转义`$`, `, ", \或者newline（回车）这几个字符，后面如果跟着的不是这几个字符，只不会被黑底，反斜杠会被保留，例如：

```
$ echo "\$,\",\`,\',\t"
$,",`,\',\t
```

双引号内可以直接包含单引号，而且单引号也没有如上据说的特殊含义，所以像

```
"var='$var'"
```

中`$var`还是会被展开的，而不要以为简单地认为在单引号内部就不会展开了。如果双引号内部包含感叹号！就比较头痛了，感叹号是用于命令行历史展开，例如!!展开为上一次执行的命令。你可以试试双引号中包含`!`：

```
$ echo "!"
-bash: !: event not found
$ echo "\!"
\!
```

可见，即使你用反斜杠也没办法转义，除非你把历史展开功能关闭（在脚本里面是没有问题的，默认是关闭的）。

```
$ set +o histexpand 

$ echo "!"
!
```

当然，感叹号可以用在单引号里面。

```
$ set -o histexpand


$ echo '!'
!
```

到此为止，其实双引号和单引号的区别已经说得差不多了。不过还可以再说几个特殊的用法，前面说过可以在双引号内部使用单引号，你有想过在单引号里面使用单引号吗？

```
$ echo '\''
> 
```

是不是发现不能用，因为单引号中反斜杠是没有转义的效果的，任何字符都没有特殊的含义。那就没有办法了吗？方法总是有的，可以在第一个单引号前面加个`$`符号：

```
$ echo $'\''
'
```

## **9 特殊用法`$'string'`**


前面一点中已经介绍了 $'string'这种用法，比如 $'\''，之所以可以这样用，通俗地讲，就是在这种语法里一些转义字符串是被认可的，事实上有效地的转义底字符串列表可以看这里，例如\b，\',\n,\f,\nnn,\xhh等等，是不是很熟悉。

$'string'的这个特性，其实为我们提供了一种很有用的技巧：

```
$ echo $'\x41'
A
```

## **10. 用双引号比不用更加安全**

双引号除了前面讲到的去除特殊涵义的作用外，还可以避免字符串被分隔解析，例如：

```
$ echo `ls -l`
total 4.0K -rw-r--r-- 1 kodango kodango 4 Nov 10 20:09 1 -rw-r--r-- 1 kodango kodango 0 Nov 10 20:09 2
$ echo "`ls -l`"
total 4.0K
-rw-r--r-- 1 kodango kodango 4 Nov 10 20:09 1
-rw-r--r-- 1 kodango kodango 0 Nov 10 20:09 2
```

前者没有加双引号，ls -l输出行之间的回车就被吃掉了。原因是，当ls -l返回的结果传递给echo之前，会先被shell进行参数解析，而shell是用IFS定义的分隔符来分隔字符串的，一般包括\n，所以它把解析后的结果再传递给echo，就成为echo "line 1...." "line 2..."这种形式了，结果就像上面一样。

而用双引号包括起来可以避开字符串被拆开解析，因为shell认为它是一个单独的字符串。所以一般情况下，多用引号包括变量是好的，`"$var"`比`$var`更安全。
