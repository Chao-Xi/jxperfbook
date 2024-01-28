# 快速提升Shell脚本能力

## Shell 脚本

Shell 脚本是运行在 shell 环境中的脚本语言，用于自动执行重复性任务、管理系统配置、以及通过编写脚本和运行脚本来执行一系列命令。

shell 脚本可以帮助我们完成系统管理、软件安装、文件操作等需求。


Shell 脚本文件以 .sh 作为扩展名，基本语法如下：

```
#!/bin/bash

# This is a note

echo "The first line of the script is 'shebang'"
```

### Shebang

shell 脚本的第一行是 shebang，`#!/bin/bash`，它指定了执行脚本的解释器，通常是 bash。执行脚本时，内核会读取 shebang，并使用该解释器执行脚本。

可以使用以下命令查看系统使用、支持的 shell：

* `echo $SHELL`：显示当前使用的 shell 类型
* `cat /etc/shells`：显示操作系统中可用的 shell 类型

### 执行 Shell 脚本

可以通过以下方式使用 shebang 指定的 shell 执行脚本：

**第一种方式，使用 sh 命令：**

```
sh script_file.sh
```

**第二种方式，通过相对路径或绝对路径：**

* 首先为脚本文件添加可执行权限

```
chmod +x script_file.sh
```

* 通过相对或绝对路径，从终端执行脚本：

```
./script_file.sh
```

接下来讨论 Shell 脚本中的概念。

## 读取用户输入

read 命令可以从标准输入（stdin）读取用户输入的内容。比如输入用户名 weiwendi 为 shell 脚本中 jacob 变量赋值：

```

#!/bin/bash

# displays the prompt message
# -p stand for prompt
# reads user input and assigns it to the newusername variable
read -p "Enter the username: " username
echo $username
```

输入的内容将以明文显示在屏幕上，如果不希望显示输入的内容，**比如密码之类需要保密的内容，可以使用 `read -sp`：**

```

# reads input from the user & hides the text from echoing in the terminal
# -s stand for silent
read -sp "Enter Password: " password
echo ""
echo $password
```

如果不想指定变量名，可以通过 `$REPLY` 显示输入的内容：

```

#!/bin/bash

echo "Enter the username: "
read
echo "Read without variable name assignment: "$REPLY
```

## 命令替换

通过命令替换的形式可以将命令的输出赋值给变量。命令替换有以下两种方式：

* 使用一对反撇号 ``
* 使用 `$()`

例如，将 pwd 的输出赋值给 `working_dir` 变量：

```

#!/bin/bash

echo "Method 1 - Using backticks"
working_dir1=`pwd`
echo $working_dir1

echo "Method 2 - Using $()"
working_dir2=$(pwd)
echo $working_dir2
```

## 参数传递

可以通过传递参数的形式，为脚本提供运行时所需的参数。**脚本可以使用特殊变量如 1、2、`$3` 等访问这些参数**。

* `$0`：返回执行脚本的文件名
* `$@`：返回从 CLI 传递的所有参数
* `$#`：返回从 CLI 传递的参数数量

假设有一个名为`argument_passing.sh` 的脚本文件，我们向它传递两个参数。

```
sh argument_passing.sh "Jacob" "DevOps Engineer"
```

```

#!/bin/bash

# get script file name
echo "FileName: " $0 # argument_passing.sh

# provide the first argument
echo "First Argument: "$1 # weiwendi

# provide the second argument
echo "Second Argument: "$2 # DevOps Engineer

# displays all arguments provided
echo "All Arguments: "$@ # Jacob DevOps Engineer

# displays number of arguments provided
echo "Arguments Number: "$# # 2
```

## 算术运算

使用双括号 `(())` 可以在 shell 脚本中执行算术运算。`(())` 也称为一元运算符。

```

#!/bin/bash

n1=10
n2=5

echo "Sum of two numbers: "$(($n1+$n2)) # Addition
echo "Sub of two numbers: "$(($n1-$n2)) # Substraction
echo "Mul of two numbers: "$(($n1*$n2)) # Mulitplication
echo "Div of two numbers: "$(($n1/$n2)) # Division
echo "Modulus of two numbers: "$(($n1%$n2)) # Modulus
```

## 条件表达式

在 shell 脚本中，`[[ ]]` 或 `test` 命令可用于评估条件表达式。以下是一些用于测试条件的一元运算符。


* `[[ -z String ]]`：判断字符串是否为空。字符串为空，结果为 true。
* `[[ -n String ]]`：判断字符串是否不为空。字符串不为空，结果为 true。
* `[[ String1 == String2 ]]`：判断两个字符串是否相同。
* `[[ String1 != String2 ]]`：判断两个字符串是否不相同。
* `[[ num1 -eq num2 ]]`：判断两个数字是否相等。
* `[[ num1 -ne num2 ]]`：判断两个数字是否不相等。
* `[[ num1 -lt num2 ]]`：num1 是否小于 num2。
* `[[ num1 -le num2 ]]`：num1 小于或等于 num2。
* `[[ num1 -gt num2 ]]`：num1 大于num2。
* `[[ num1 -ge num2 ]]`：num1 大于或等于 num2。

在 Linux 中，大多数对象以文件的形式存在，因此，Linux 也提供了对文件的条件判断：

* `[[ -e fileName ]]`：判断文件是否存在
* `[[ -r fileName ]]`：对文件是否有读权限
* `[[ -h fileName ]]`：文件是否为符号链接
* `[[ -d fileName ]]`：是否为目录
* `[[ -w fileName ]]`：对文件是否有写权限
* `[[ -s fileName ]]`：文件是否大于 0 字节
* `[[ -x fileName ]]`：对文件是否有执行权限

## if else

if else 是条件语句，可以根据条件的 true 或 false 执行不同的命令。

创建一个名为 `ifelse.sh` 的文件，代码内容如下：

```
#!/bin/bash

# -e stands for exists
# use brackets to determine whether the file exists
if [[ -e ./ifelse.sh ]]
then
	echo "File exist"
else
	echo "File does not exist"
fi

# run the test command to check whether the file exists
if test -e "./ifelse.sh"
then
	echo "File exist"
else
	echo "File does not exist"
fi
```

## elif

elif 是 else 和 if 的组合，用于创建多个条件语句，必须与 if else 语句结合使用

```
#!/bin/bash

read -p "Enter the number between 1 to 3: " number

if [[ $number -eq 1 ]]
then
	echo "The number entered is 1"
elif [[ $number -eq 2 ]]
then
	echo "The number entered is 2"
elif [[ $number -eq 3 ]]
then
	echo "The number entered is 3"
else
	echo "Invalid Number"
fi
```

## 循环

在 shell 编程中，除了条件判断外，对一些特殊情况需要进行循环操作。

### **for**

for 循环用于遍历列表，在进入 shell 循环前知道迭代次数时，通常使用 for 循环。语法如下：

```

#!/bin/bash

for i in {1..10}
do
	echo "Var: $i"
done
```

### while

while 循环用于在特定条件为真时重复执行一组命令，循环一直持续到条件为假时终止。

```
#!/bin/bash

count=0

while [ $count -lt 5 ]
do
	echo $count
	count=$(($count+1))
done
```

### until

```
#!/bin/bash

count=1

until [ $count -gt 5 ]
do
	echo $count
	let count++
done
```

### Break 语句

break 关键字是一个控制语句，用于在满足特定条件时退出循环（for、while 或 until）。break 仅退出循环，脚本将继续向下执行。

```

#!/bin/bash

count=1

while true
do
	echo "Count is $count"
	count=$(($count+1))
	if test $count -gt 5; then
		echo "Break statement reached"
		break
	fi
done

echo "Jump out of the sequence and continue to execute the following code."
```

### Continue 语句

continue 是循环（如 for、while 和 until）中使用的关键字，用于跳过循环的当前迭代，进入下一次迭代。

```

#!/bin/bash

for i in {1..10}
do
	if [ $i -eq 5 ]
	then
		continue
	fi
	echo $i
done
```


### 数组

数组中可以存储多个值，Bash shell 支持一维数组。在脚本中，通常把数组作为变量的值。


* `${arrayVarName[@]}`：显示数组变量中的所有值
* `${#arrayVarName[@]}`：显示数组的长度
* `${arrayVarName[0]}`：显示数组的第一个元素
* `${arrayVarName[-1]}`：显示数组的最后一个元素
* `unset arrayVarName[2]`：删除第三个元素，索引从 0 开始。


## 函数


函数是一个代码块，可以重复使用来完成特定任务，从而提供了代码的可重用性。

以下是一个函数示例：

```

#!/bin/bash

sum() {
	echo "The numbers are: ${n1} ${n2}"
	sum_val=$((${n1}+${n2}))
	echo "Sum: $sum_val"
}

n1=$1
n2=$2
sum
```

### 带有返回值的函数

函数是可以有返回值的。要访问函数的返回值，需要使用` $?`

```

#!/bin/bash

sum(){
        echo "The numbers are: $n1 $n2"
        sum_val=$(($n1+$n2))
        return $sum_val
}

n1=$1
n2=$2
sum

echo "Retuned value from function is $?"
```

## 变量

变量是一个占位符，用于保存一个值，以后可以使用该名称访问该值。变量有两种类型：

* 全局变量：在函数外部定义的变量，可在整个脚本中访问
* 局部变量：定义在函数内部的变量，只能在函数内部访问

```

#!/bin/bash

# x & y are global variables
x=10
y=20

sum() {
	sum=$(($x+$y))
	echo "Global Variable Addition: $sum"
}

sum

sub() {
	# a & b are local variables
	local a=20
	local b=10
	local sub=$(($a-$b))
	echo "Local Variable Substraction: $sub"
}

sub
```

## 字典

在 shell 脚本中，字典是使用关联数组实现的。关联数组是使用字符串而不是整数作为索引的数组。`declare -A` 命令用来定义字典：

```
!/bin/bash

# 定义一个字典，并在定义后赋值
declare -A dic1
dic1[name]=Curry
dic1[no]=30

# 根据 key 打印 value
echo "the name's: ${dic1[name]}"
echo "the jersey number's: ${dic1[no]}"

# 定义字典并同时赋值
declare -A dic2=([name]=Wiggins [no]=22)

# 打印字典中的所有 key
echo "print all keys in the dic2: ${!dic2[@]}"
# 打印字典中的所有 value
echo "print all values in the dic2: ${dic2[@]}"

# 打印键值对
for i in ${!dic2[@]}
do
	echo $i: ${dic2[$i]}
done
```

## Set 选项

**<mark>set 命令可以修改或显示 shell 选项的值。如果不带任何参数，将列出所有 shell 变量及值</mark>**。

`set -x `类似于调试模式，先打印正在执行的命令，然后显示命令的输出结果。

`set -e` 当出现非零退出代码时，立即退出脚本。


在使用管道命令时，例如 `sdfdsf | echo 'vish'`。由于该行执行的最后一条命令是 echo，而 echo 返回的退出代码为零，因此整行命令被认为是成功的，但之前的命令 sdsds 将返回非零代码，这是错误的。要解决这个问题，我们可以使用下面的设置选项。

`set -o pipefail` 为了克服上述管道命令错误，可以使用 `set -o pipefail` 选项，它会捕获并立即停止脚本。因此，每条命令都应返回零退出代码。否则，脚本将失败。

