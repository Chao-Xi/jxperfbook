# Linux 中 `.bashrc`、`.bash-profile` 和 `.profile` 之间的区别


## 2. 交互式 和 非交互式 Shell

### 2.1 交互式 Shell（Interactive Shell）

指通过终端输入命令并获得执行结果的 shell，其提供两种模式：交互式登录和交互式非登录 shell。

**Bash 调用时，会从一组启动文件中读取并执行命令,读取哪些文件取决于 shell 是作为交互式登录还是非登录 shell 调用。**

**交互式登录 Shell：**

指需要用户名、密码登录后才能进入的 shell，例如：通过 ssh 或本地远程登录到终端，还有就是使用 `--login` 选项启动 `Bash`；


当 Bash 作为交互式登录 shell 调用时，它将查找 `/etc/profile` 文件，如果该文件存在，它将运行文件中列出的命令，**然后 Bash 将按以下的顺序搜索 `~/.bash_profile`、`~/.bash_login` 和 `~/.profile` 文件，并在第一个找到的文件中执行命令**。

**Bash 启动文件搜索执行顺序：**

* **`/etc/profile`：设置系统级的环境变量和启动程序，对全局用户生效；**
* `/etc/profile.d`：存放应用程序所需的启动脚本，**其中包括了颜色、语言、less、vim 及 which 等命令的一些附加设置，这些脚本文件之所以能够被自动执行，是因为在 `/etc/profile` 中使用一个 for 循环语句来调用这些脚本**;

```
if [ -d /etc/profile.d ]; then
for i in /etc/profile.d/*.sh; do
if [ -r $i ]; then
  . $i
fi
done
unset i
fi
```

* `~/.bash_profile`： **当前用户个人配置脚本，如果该脚本存在，则执行完就不再往下搜索执行**；
* `~/.bash_login`：如果 `~/.bash_profile` 没找到，**该脚本存在则尝试执行这个脚本，执行完就不再往下搜索执行；**
* （Ubuntu）`~/.profile`（或者 `~/.bash_profile `）：如果 `~/.bash_profile` 和 `~/.bash_login` 都没找到，则尝试读取这个脚本；

**注意：**

> Linux 发行版更新的时候，会更新 `/etc` 里面的文件，比如 `/etc/profile`，因此建议不要直接修改这个文件。如果想修改所有用户的环境参数，建议在 `/etc/profile.d` 目录里面新建 `.sh` 脚本，如果想修改个人登录环境，一般是写在 `~/.bash_profile` 里面。


### **交互式非登录 shell：**

指不需要用户名和密码即可打开的 shell，例如：直接命令 “bash” 就是打开一个新的非登录 shell，或者在 Linux Gnome 桌面环境打开一个“终端（terminal）”窗口也是一个非登录 shell。

**Bash 启动文件搜索执行顺序：**

* （Ubuntu）`/etc/bash.bashrc`（或者/etc/bashrc）： 对全局用户生效；
* `~/.bashrc`： 仅对当前用户有效；

### 非交互式 Shell (Non-Interactive Shell)

指不与终端关联，一般是执行一个命令、一个脚本的 shell ，通常不需要人工干预，例如系统维护脚本、后台脚本等。

该类型 shell 不执行任何启动文件，它从创建它的 shell 继承环境变量。

## 3. Bash 启动文件

### 3.1 /etc/profile

用于设置系统级的环境变量和启动程序，这个文件中的配置会对所有用户生效。一般不建议在 `/etc/profile` 文件中添加环境变量，因为在这个文件中添加的设置会对所有用户起作用。当必须添加时，可以按以下方式添加：


示例：添加一个 `“TEST_BASH”` 值为 “linux” 的环境变量：

**vi /etc/profile**

```
...
TEST_BASH=linux
```

添加环境变量后，需要重新登录才能生效，也可以使用 source 命令强制立即生效：

```
echo $TEST_BASH
linux
```

或者 `/etc/profile.d` 目录里面新建 `.sh` 脚本：

**vi /etc/profile.d/test.sh**

```
TEST_BASH=linux
```

保存，验证可达到同样效果；

### 3.2. `.bash_profile`

`.bash_profile` 用户级的设置，只对单一用户有效，文件存储于 `~/.bash_profile`，和 profile 文件类似，`bash_profile` 也会在用户登录（login）时生效，但与 profile 不同的是 `bash_profile` 只会对当前用户生效。

示例：设置和导出PATH变量

`vi ~/.bash_profile`

```
echo "Bash_profile execution starts.."  
PATH=$PATH:$HOME/bin; 
export PATH; 
echo "Bash_profile execution stops.."
```

以交互式登录 shell 打开：可以看到命令提示符前自定义的输出：

```
Last login: Mon Apr  1 17:46:37 2024 from 10.40.2.83
Bash_profile execution starts..
Bash_profile execution stops..
```

### 3.3. `.bashrc`

bashrc 文件用于配置函数或别名，有两种级别：

**系统级:系统级的位于 `/etc/bashrc`，对所有用户生效;**

**用户级:用户级的位于` ~/.bashrc`，仅对当前用户生效;**

bashrc 文件只会对指定的 shell 类型起作用，bashrc 只会被 bash shell 调用。

示例 .bashrc 文件：

`vi ~/.bashrc`

```
echo "Bashrc execution starts.." 
alias elui='top -c -u $USER' 
alias ll='ls -lrt' 
echo "Bashrc execution stops.."
```
交互式非登录 shell 执行，在命令提示符之前看到以下输出：

```
$ bash
Bashrc execution starts..
Bashrc execution stops..
```

### 3.4 `.bash_logout`

~/.bash_logout 每次退出 shell 时执行，通常用来做一些清理工作和记录工作，比如删除临时文件,如果没有退出时要执行的命令，这个文件也可以不存在。

## 4. .bashrc 和 .bash_profile 之间的区别

`.bash_profile`：交互式登录 shell 调用时读取和执行，用于只运行一次的命令，例如自定义环境变量。

.bashrc：交互式非登录 shell 调用时读取和执行，每次启动新 shell 时应运行的命令放入该文件中，包括别名和函数、自定义提示、历史记录等。

通常，环境变量放在 `.bash_profile `中，由于交互式登录 shell 是第一个shell，因此环境设置所需的所有默认设置都放在`.bash_profile`中，它们只被设置一次，所有子 shell 将被继承。

同样，别名和函数放在 `.bashrc` 中，确保每次从现有环境中启动 shell 时都加载它们，同时为了避免登录和非登录交互 shell 设置的差异，将下面的代码片段插入到 `.bash_profile` 中，实现 `.bash_profile` 调用 .bashrc，这样在每个交互式登录 shell中，`.bashrc` 也会在同一个 shell 中执行:

```
if [ -f ~/.bashrc ];
then 
    .  ~/.bashrc; 
fi 
PATH=$PATH:$HOME/bin export PATH
```










