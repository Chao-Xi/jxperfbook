# Linux AWK 文本处理

## 8个经典案例

### 案例一：分析Nginx访问日志

```
统计访问IP次数：
awk '{a[$1]++}END{for(v in a)print v,a[v]}' access.log

统计访问IP次数：
awk '{a[$1]++}END{for(v in a)print v,a[v]}' access.log

统计访问访问大于100次的IP：
awk '{a[$1]++}END{for(v in a){if(a[v]>100)print v,a[v]}}' access.log

统计访问IP次数并排序取前10：
awk '{a[$1]++}END{for(v in a)print v,a[v]|"sort -k2 -nr |head -10"}' access.log

统计时间段访问最多的IP：
awk '$4>="[02/Jan/2017:00:02:00" && $4<="[02/Jan/2017:00:03:00"{a[$1]++}END{for(v in a)print v,a[v]}' access.log

统计上一分钟访问量：
date=$(date -d '-1 minute' +%d/%d/%Y:%H:%M)
awk -vdate=$date '$4~date{c++}END{print c}' access.log

统计访问最多的10个页面：
awk '{a[$7]++}END{for(v in a)print v,a[v]|"sort -k1 -nr|head -n10"}' access.log

统计每个URL数量和返回内容总大小:
awk '{a[$7]++;size[$7]+=$10}END{for(v in a)print a[v],v,size[v]}' access.log

统计每个IP访问状态码数量：
awk '{a[$1" "$9]++}END{for(v in a)print v,a[v]}' access.log

统计访问IP是404状态次数：
awk '{if($9~/404/)a[$1" "$9]++}END{for(i in a)print v,a[v]}' access.log
```

### 案例二：两个文件差异对比

生成a和b两个文件，用于测试：

```
$ seq 1 5 > a
$ seq 3 7 > b
```

**找出b文件在a文件相同记录：**

```
方法1：
awk 'FNR==NR{a[$0];next}{if($0 in a)print $0}' a b
3
4
5
awk 'FNR==NR{a[$0];next}{if($0 in a)print FILENAME,$0}' a b
b 3
b 4
b 5
awk 'FNR==NR{a[$0]}NR>FNR{if($0 ina)print $0}' a b
3
4
5
awk 'FNR==NR{a[$0]=1;next}(a[$0]==1)' a b  # a[$0]是通过b文件每行获取值，如果是1说明有
awk 'FNR==NR{a[$0]=1;next}{if(a[$0]==1)print}' a b
3
4
5

方法2：
awk 'FILENAME=="a"{a[$0]}FILENAME=="b"{if($0 in a)print $0}' a b
3
4
5
4
5

方法3：
awk 'ARGIND==1{a[$0]=1}ARGIND==2 && a[$0]==1' a b
3
4
5
```

**找出b文件在a文件不同记录：**

```
方法1：
awk 'FNR==NR{a[$0];next}!($0 in a)' a b
6
7
awk 'FNR==NR{a[$0]=1;next}(a[$0]!=1)' a b
或
awk 'FNR==NR{a[$0]=1;next}{if(a[$0]!=1)print}' a b
6
7

方法2：
awk 'FILENAME=="a"{a[$0]=1}FILENAME=="b" && a[$0]!=1' a b

方法3：
awk 'ARGIND==1{a[$0]=1}ARGIND==2 && a[$0]!=1' a b
```

### 案例三：合并两个文件

生成a和b两个文件，用于测试：

```
$ cat a
zhangsan 20
lisi 23
wangwu 29
$ cat b
zhangsan man
lisi woman
wangwu ma
```

**将a文件合并到b文件**：

```
方法1：
awk 'FNR==NR{a[$1]=$0;next}{print a[$1],$2}' a b
zhangsan 20 man
lisi 23 woman
wangwu 29 man
方法2：

awk 'FNR==NR{a[$1]=$0}NR>FNR{print a[$1],$2}' a b
zhangsan 20 man
lisi 23 woman
wangwu 29 man
```

**将a文件相同IP的服务名合并：**

```
$ cat a
192.168.1.1: httpd
192.168.1.1: tomcat
192.168.1.2: httpd
192.168.1.2: postfix
192.168.1.3: mysqld
192.168.1.4: httpd

awk 'BEGIN{FS=":";OFS=":"}{a[$1]=a[$1] $2}END{for(v in a)print v,a[v]}' a
192.168.1.4: httpd
192.168.1.1: httpd tomcat
192.168.1.2: httpd postfix
192.168.1.3: mysql
```

> 解读：

> 数组a存储是$1=a[$1] $2，第一个a[$1]是以第一个字段为下标，值是a[$1] $2，也就是$1=a[$1] $2，值的a[$1]是用第一个字段为下标获取对应的值，但第一次数组a还没有元素，那么a[$1]是空值，此时数组存储是192.168.1.1=httpd，再遇到192.168.1.1时，a[$1]通过第一字段下标获得上次数组的httpd，把当前处理的行第二个字段放到上一次同下标的值后面，作为下标192.168.1.1的新值。此时数组存储是192.168.1.1=httpd tomcat。每次遇到相同的下标（第一个字段）就会获取上次这个下标对应的值与当前字段并作为此下标的新值。


### 案例四：将第一列合并到一行


```
$ cat file
1 2 3
4 5 6
7 8 9

awk '{for(i=1;i<=NF;i++)a[i]=a[i]$i" "}END{for(vin a)print a[v]}' file
1 4 7
2 5 8
3 6 
```

	解读：
	
	for循环是遍历每行的字段，NF等于3，循环3次。
	
	
	
	读取第一行时：
	
	第一个字段：a[1]=a[1]1" "  值a[1]还未定义数组，下标也获取不到对应的值，所以为空，因此a[1]=1 。
	
	第二个字段：a[2]=a[2]2" "  值a[2]数组a已经定义，但没有2这个下标，也获取不到对应的值，为空，因此a[2]=2 。
	
	第三个字段：a[3]=a[3]3" "  值a[2]与上面一样，为空,a[3]=3 。
	
	读取第二行时：
	
	第一个字段：a[1]=a[1]4" "  值a[2]获取数组a的2为下标对应的值，上面已经有这个下标了，对应的值是1，因此a[1]=1 4
	
	第二个字段：a[2]=a[2]5" "  同上，a[2]=2 5
	
	第三个字段：a[3]=a[3]6" "  同上，a[2]=3 6
	
	读取第三行时处理方式同上，数组最后还是三个下标，分别是1=1 4 7，2=2 5 8，3=36 9。最后for循环输出所有下标值。
	
	
### 案例五：字符串拆分

```

方法1：
echo "hello" |awk -F '''{for(i=1;i<=NF;i++)print $i}'
h
e
l
l
o

方法2：
echo "hello" |awk '{split($0,a,"''");for(v in a)print a[v]}'
l
o
h
e
l
```

### 案例六：统计出现的次数

统计字符串中每个字母出现的次数：

```
echo "a.b.c,c.d.e" |awk -F'[.,]' '{for(i=1;i<=NF;i++)a[$i]++}END{for(v in a)print v,a[v]}'
a 1
b 1
c 2
d 1
e 1
```

### 案例七：获取某列数字最大数

```
$ cat a
a b 1
c d 2
e f 3
g h 3
i j 2

获取第三字段最大值：
awk 'BEGIN{max=0}{if($3>max)max=$3}END{print max}' a
3
打印第三字段最大行：
awk 'BEGIN{max=0}{a[$0]=$3;if($3>max)max=$3}END{for(v in a)if(a[v]==max)print v}'a
g h 3
e f 3
```

### 案例八：去除文本第一行和最后一行

```
seq 5 |awk 'NR>2{print s}{s=$0}'
2
3
4
```

> 解读：
> 
> 读取第一行，NR=1，不执行print s，s=1
> 
> 读取第二行，NR=2，不执行print s，s=2 （大于为真）
> 
> 读取第三行，NR=3，执行print s，此时s是上一次p赋值内容2，s=3
> 
> 
>最后一行，执行print s，打印倒数第二行，s=最后一行。



