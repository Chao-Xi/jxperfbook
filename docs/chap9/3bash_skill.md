# **3 Bash Pitfalls: 编程易犯的错误（一）**

## 1. **`for i in $(ls *.mp3)`**

Bash写循环代码的时候，确实比较容易犯下面的错误：

```
for i in $(ls *.mp3); do    # 错误!
    some command $i         # 错误!
done

for i in $(ls)              # 错误!
for i in `ls`               # 错误!

for i in $(find . -type f)  # 错误!
for i in `find . -type f`   # 错误!

files=($(find . -type f))   # 错误!
for i in ${files[@]}        # 错误!
```

这里主要两个问题：

* 使用命令展开时不带引号，其执行结果会使用IFS作为分隔符，拆分成参数传递给for循环处理；
* 不应该让脚本去解析ls命令的结果；

```
$ for i in $(ls *.mp3); do echo $i; done
01
-
Don't
Eat
the
Yellow
Snow.mp3
```

比这更差的情况是，上面命令展开的结果可能被Shell进一步处理，比如文件名展开。比如，ls执行的结果中包含`*`号，按照通配符的规则, `*`号会被展开成当前目录下的所有文件:

```
$ touch "1*.mp3" "1.mp3" "11.mp3" "12.mp3"

$ for i in $(ls *.mp3); do echo $i; done
1*.mp3 1.mp3 11.mp3 12.mp3
1.mp3
11.mp3
12.mp3
1.mp3
11.mp3
12.mp3
```