# 22 使用Shell逻辑表达式，满足各种条件检测

在Shell脚本中，逻辑运算符用于控制程序的流程和条件判断。

可以使用test命令测试逻辑表达式。例如：

```
if test logical_expression; then
  echo true
else
  echo false
fi
```

为方便在命令行进行测试，上述语句可以合并为一行，语句后面添加半角分号即可：

```
if test logical_expression; then echo true; else echo false; fi
```

也可以不使用test命令，将逻辑表达式放在中括号[]中，达到测试逻辑表达式的效果。例如：

```
if [ logical_expression ]; then echo true; else echo false; fi
```

**注意：逻辑表达式与[]之间要有空格。**

以下是Shell中常用的逻辑运算符及其说明：

* **文件和目录检测**

**-f：检测文件是否存在。**

**-d：检测目录是否存在。**

-b：检测是否为块设备文件。

-c：检测是否为字符设备文件。

-S：检测是否为套接字文件。

-L：检测是否为符号链接文件。

-e：检测某个东西（文件、目录等）是否存在。

* **程序所有权检测**

-G：检测是否由GID（组ID）所执行的程序所拥有。

-O：检测是否由UID（用户ID）所执行的程序所拥有。

-p：检测是否为程序间传送信息的命名管道（FIFO）或命名管道（FIFO）。


* **文件属性检测**

-r：检测文件存在且授予读取权限。

-w：检测文件存在且授予定稿权限。

-x：检测文件存在且授予执行权限。

-s：检测文件存在且大小大于零。

-u：检测文件存在且其SUID位已设置。

SUID是Set User ID的缩写，它会出现在文件拥有者权限的执行位上，有设置该位的文件会在其执行时，使调用者暂时获得该文件拥有者的权限。

-g：检测文件存在且其SGID位已设置。

**SGID即Set Group ID的缩写，它出现在文件所属组权限的执行位上面，它对普通二进制文件和目录都有效，当它作用于普通文件时，和SUID类似，在执行该文件时，用户将获得该文件所属组的权限。**

-k：检测文件存在且具有粘滞位属性。

粘滞位（Stickybit），或粘着位，是Unix/Linux文件系统权限的一个旗标。最常见的用法在目录上设置粘滞位，如此以来，只有目录内文件的所有者或者root才可以删除或移动该文件。如果不为目录设置粘滞位，任何具有该目录写和执行权限的用户都可以删除和移动其中的文件。实际应用中，粘滞位一般用于/tmp目录，以防止普通用户删除或移动其他用户的文件。

* **文件比较**

-nt：第一个文件比第二个文件新。

-ot：第一个文件比第二个文件旧。

-ef：第一个文件与第二个文件为同一个文件（例如，硬链接）。


* **逻辑运算符**

-a：逻辑与。

-o：逻辑或。

!：逻辑非。

在Bash中，可以在双中括号`[[]]`中使用`&&`表示逻辑与，`||`表示逻辑或。

* **数值比较**

-eq：等于。

-ne：不等于。

-lt：小于。

-le：小于等于。

-gt：大于。

-ge：大于等于。


* **字符串判断**

=：等于。

!=：不等于。

-z：空字符串。

-n：非空字符串。 