#  Ansible 从入门到放弃

## 1 Ansible 简介

### Ansible 简介

Ansible 是一个由 Red Hat 开发的 开源自动化运维工具，用来做：

- • 配置管理
- • 应用部署
- • 自动化任务执行
- • 多台服务器批量操作

它的最大特点是：**无代理（agentless）+ 基于 SSH 连接 + YAML 编写（可读性强）。**

### 为什么选择 Ansible？

```

特点     描述
✅ 无代理     被控端无需安装 agent，直接通过 SSH 操作
✅ 易于上手   使用 YAML 编写 playbook，可读性强
✅ 模块丰富   内置和社区模块支持各种平台与任务
✅ 跨平台     管理 Linux、Windows、网络设备等
✅ 可扩展     支持自定义模块和插件系统
✅ 社区活跃   拥有丰富的开源资源和文档
```

### Ansible 能做什么？

- • 批量部署软件（如安装 nginx、mysql）
- • 配置文件下发与模板替换
- • 重启服务、管理用户、开防火墙等
- • 自动化部署 Web 应用、容器、云服务（AWS、Azure）
- • 编排跨主机任务（如部署 Hadoop、K8s、Ceph）

### Ansible 的架构

![Alt Image Text](../../images/sre_ans1_1.png "Body image")

**Ansible 管理节点负责下发任务，包含以下组件：**

- • Ansible：Ansible-core，Ansible 的主程序，包含 Ansible 相关二进程程序
- • Inventory：主机清单，用来定义被管理服务器的清单
- • Ad Hoc：Ansible 的命令行，用于执行简单的任务或做调试使用
- • **Playbooks：剧本，用来定义复杂的自动化任务**
- • **Core Modules：Ansible 核心模块，包含 Ansible 最常用的模块（Ansible 2.9 以前会包含大量模块，但在 2.10 后 Ansible 只会包含少量的核心模块）**
- • Ansible Collections：核心模块只能满足基础功能，通过 Ansible Collections 可以安装新的模块来扩展 Ansible 的功能
- • Connection Plugins： 连接插件 ，连接插件定义 Ansible 连接到远程主机的方式


### Ansible 管理主机的流程：

1. Ansible 通过 Inventory 来定义主机清单
2. 通过 Playbooks 或 Ad Hoc 来定义任务（任务通过模块执行）和要执行任务的主机
3. 通过连接插件，将模块推送到被控端
4. 在被控端执行模块的任务，执行完成后删除模块

> Ansible 的大部分模块都具有幂等性，多次执行只会修改需要改的内容。但部分模块除外，如 raw、command 和 shell 模块。

**Ansible 的管理节点只能是 Linux 系统**

![Alt Image Text](../../images/sre_ans1_2.png "Body image")

Ansible 会使用被控节点有的连接方式来管理被控节点，如通过 SSH 管理 Linux，因此 Ansible 是无代理的，不需要在被控节点安装 Agent


- • `ANSIBLE_CONFIG` 的环境变量
- • 当前工作目录下的 `ansible.cfg`
- • 当前用户家目录下的 `.ansible.cfg` 隐藏文件
- • 全局默认的配置文件 ``/etc/ansible/ansible.cfg`


## Ansible 安装

Ansible 有三种安装方式，源码安装、发行版安装和 Python 安装。

使用发行版安装或 Python 安装两种方式时，Ansible 的安装包有两个，区别如下：

- • ansible-core：一种极简语言和运行时包，包含一组内置模块和插件。
- • ansible：一个更大的“包含电池”的软件包，它添加了社区精选的 Ansible 集合选择，用于自动化各种设备。

> 在用源码或者 Python 安装 Ansible 时，默认不会安装 sshpass 软件包，该软件包用来给 Ansible 提供密码验证被控端，因此如果在执行 Ansible 的命令时需要输入 ssh 的密码，则需要该软件包，该软件包通过 `dnf install -y sshpass`。

```
[root@ansible ansible]# ansible servera -m ping
servera | FAILED! => {
 "msg": "to use the 'ssh' connection type with passwords or pkcs11_provider, you must install the sshpass program"
}
```

```
[root@ansible ~]# dnf install python3.12 python3.12-pip sshpass
[root@ansible ~]# tar xf ansible-2.16.3.tar.gz
[root@ansible ~]# cd ansible-2.16.3/
[root@ansible ansible-2.16.3]# python3 -m pip install -r ./requirements.txt
[root@ansible ansible-2.16.3]# python3 setup.py install
```

**源码安装只能安装 Ansible-core。**



```
[root@ansible ~]# dnf install -y epel-release

# 安装最简洁的 Ansible
[root@ansible ~]# dnf install ansible-core


# 安装包含常用模块的 Ansible
[root@ansible ~]# dnf install ansible
```


Python 安装

```
# 安装 Python3 和 pip
[root@ansible ~]# dnf install python3.12 python3.12-pip sshpass

# 安装 Ansible-core
[root@ansible ~]# python3.12 -m pip install ansible-core==2.16.3

# 安装 Ansible
[root@ansible ~]# python3.12 -m pip install ansible
```

### 设置 Ansible 参数自动补全

```
[root@ansible ~]# python3 -m pip install argcomplete
[root@ansible ~]# activate-global-python-argcomplete --user
```

**重新登录命令行加载一下环境变量就可以看到自动补全了。**

###  快速配置并使用 Ansible

有一个被控节点，地址为 192.168.221.131，主机名为 servera。


```
[root@ansible ~]# mkdir ansible
[root@ansible ~]# cd ansible
[root@ansible ansible]# sed -i 's/;inventory=.*/inventory\ =\ .\/inventory/' ansible.cfg
[root@ansible ansible]# cat <<-EOF > ansible.cfg
[defaults]
inventory      = ./inventory
ask_pass       = false
remote_user    =  root
log_path       = /var/log/ansible.log
host_key_checking = False
[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False
[ssh_connection]
ssh_args = -C -o ControlMaster=auto -o ControlPersist=60s -o StrictHostKeyChecking=no
EOF

[root@ansible ansible]# echo servera > inventory

[root@ansible ansible]# tail -n1 /etc/hosts
192.168.221.131 servera

# 通过 ping 模块测试网络连通性
[root@ansible ansible]# ansible all -k -m ping
SSH password:
servera | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/libexec/platform-python"
    },
    "changed": false,
    "ping": "pong"
}
 
```

## 3 Ansible 配置文件

### Ansible配置文件

```
[root@ansible ansible]# ansible --version
ansible [core 2.16.3]
  config file = /root/ansible/ansible.cfg
  configured module search path = ['/root/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/lib/python3.12/site-packages/ansible_core-2.16.3-py3.12.egg/ansible
  ansible collection location = /root/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/local/bin/ansible
  python version = 3.12.8 (main, Dec 12 2024, 16:30:29) [GCC 8.5.0 20210514 (Red Hat 8.5.0-22)] (/usr/bin/python3)
  jinja version = 3.1.6
  libyaml = True
```


### **文件位置和优先级**

Ansible 的配置文件在四个位置可以出现：

- • `ANSIBLE_CONFIG` 的环境变量
- • 当前工作目录下的 `ansible.cfg`
- • 当前用户家目录下的 `.ansible.cfg` 隐藏文件
- • 全局默认的配置文件 `/etc/ansible/ansible.cfg`

优先级由高到低：**环境变量 → 当前工作目录 → 家目录 → /etc**

所以执行 Ansible 指令时，先从环境变量中找配置文件，如果环境变量没有，就到当前工作目录下找配置文件，如果当前工作目录依然没有，就到当前用户的家目录下找配置文件，如果当前用户家目录下还没有，就去找 `/etc/ansible/ansible.cfg`。


推荐的 ansible.cfg 管理方式：

优先级从高到低如下：

1. 当前目录的 `./ansible.cfg`
2. 环境变量 `ANSIBLE_CONFIG` 指定的路径
3. 用户目录下的 ``~/.ansible.cfg`
4. 系统路径 `/etc/ansible/ansible.cfg`



**生成一个默认的配置文件**


```
[root@ansible ansible]# ansible-config init --disabled > ansible.cfg

```

文件内容


```
[root@ansible ansible]# grep -E '^\[' ansible.cfg
[defaults]
[privilege_escalation]
[persistent_connection]
[connection]
[colors]
[selinux]
[diff]
[galaxy]
[inventory]
[netconf_connection]
[paramiko_connection]
[jinja2]
[tags]
```

### Ansible 配置段说明

```
[defaults]	
默认设置
主要配置 Ansible 的基础行为，比如 inventory, remote_user, timeout, roles_path 等


[privilege_escalation]	
提权控制
控制 become, become_user, become_method 等提权行为（如 sudo）

[persistent_connection]	
持久连接参数
控制连接插件如 SSH 的持久化（可减少连接开销）

[persistent_connection]	
持久连接参数
控制连接插件如 SSH 的持久化（可减少连接开销）

[connection]	
通用连接配置
包括远程连接的超时、重试、缓存控制等

[selinux]	
SELinux 设置
是否自动管理 semanage、标签恢复等（对启用 SELinux 的主机有用）

[diff]	
显示差异设置
控制是否开启 diff 模式，能在修改配置时显示差异

[galaxy]	
Ansible Galaxy 设置
设置 Galaxy 源、缓存策略、角色下载等

[diff]	
显示差异设置
控制是否开启 diff 模式，能在修改配置时显示差异

[galaxy]	
Ansible Galaxy 设置
设置 Galaxy 源、缓存策略、角色下载等

[inventory]	
动态库存参数
针对 Inventory 插件的默认设置，比如缓存、路径等

[netconf_connection]	
Netconf 连接参数
针对支持 NETCONF 的网络设备（如 Cisco、Juniper）连接设置


[paramiko_connection]	
使用 paramiko 时的设置
使用 paramiko（Python SSH 库）替代 OpenSSH 时的行为控制

[jinja2]	
模板渲染设置
控制模板变量、未定义变量行为、Jinja 环境等


[tags]	
tag 行为
控制运行 playbook 时关于 tag 的匹配方式
```

### 常用配置选项

**[defaults] 常用配置：**

```
inventory	
指定主机清单路径
inventory = ./hosts


remote_user	
默认远程登录用户
remote_user = ansible


ask_pass	
是否在执行时询问 SSH 密码
ask_pass = false

ask_become_pass	
是否询问 sudo 密码
ask_become_pass = true

private_key_file	
指定私钥路径
private_key_file = ~/.ssh/id_rsa

host_key_checking	
是否启用 SSH 主机密钥检查
host_key_checking = false

timeout	
连接超时时间（秒）
timeout = 30

forks	
并发执行主机数
forks = 10

retry_files_enabled	
是否生成 .retry 文件
retry_files_enabled = false


log_path	
日志文件路径
log_path = /var/log/ansible.log


roles_path	
指定角色目录
roles_path = ./roles


gathering	
控制 facts 的收集方式（implicit、explicit、smart）
gathering = smart


fact_caching	
是否启用 facts 缓存
fact_caching = jsonfile


fact_caching_connection	
facts 缓存路径
fact_caching_connection = ./fact_cache
```

### `[privilege_escalation] `提权配置：

```
become	
是否启用提权
become = true


become_method	
使用提权方式（sudo/su/pbrun/doas 等）
become_method = sudo

become_user	
提权后的目标用户
become_user = root

become_ask_pass	
是否询问提权密码
become_ask_pass = false
```

### [`ssh_connection`]（或 [connection]）连接优化配置：

```
pipelining	
启用 SSH pipelining，加速执行
pipelining = true

control_path	
SSH 控制连接的 socket 路径
control_path = %(directory)s/%%h-%%r

ssh_args	
传递给 SSH 的参数
ssh_args = -o ControlMaster=auto -o ControlPersist=60s

retries	
SSH 失败重试次数
retries = 3
```

### [jinja2] 模板渲染相关：

```
undefined	
控制未定义变量的处理方式（如 strict）
undefined = strict

trim_blocks	
去除 Jinja2 模板中的空行
trim_blocks = true

lstrip_blocks	
去除 Jinja2 左侧空格
lstrip_blocks = true
```

### [diff] 差异显示：

```
always	
是否始终显示变更 diff
always = true

context	
显示上下文行数
context = 5
```

### [galaxy] Ansible Galaxy 设置：

```
server_list	
指定 Galaxy 服务器
server_list = ansible_galaxy

ignore_certs	
忽略 Galaxy HTTPS 证书验证
ignore_certs = false
```

### [inventory] 主机清单设置（动态库存）：

```
enable_plugins	
启用的库存插件
enable_plugins = host_list, yaml, ini

ache	
启用主机缓存
cache = true

cache_plugin	
使用的缓存插件
cache_plugin = jsonfile

cache_timeout	
缓存有效时间
cache_timeout = 600
```

### 其他配置段（可选）：

```
[paramiko_connection]	
如果使用 paramiko 作为 SSH 连接后端，配置连接行为

[netconf_connection]	
管理 NETCONF 网络设备的连接参数

[colors]	
控制终端输出颜色样式

[selinux]	
配置如何处理 SELinux 标签修复

[tags]	
控制 tag 匹配行为，比如 skip_tags 默认值等
```

### 配置案例

```
[defaults]  
inventory      = ./inventory    # 主机清单的位置
fork           = 20                # 并发执行的主机数
ask_pass       = False            # 执行 Ansible 时是否询问密码
remote_user    =  root            # 表示使用 root 用户来访问被控节点
log_path       = /var/log/ansible.log    # 指定 Ansible 的日志文件位置
host_key_checking = False                # 是否进行 SSH 主机 Key 检查
ansible_python_interpreter = /usr/bin/python3.9        # 指定被控端上 Python 的解释器
[privilege_escalation]
become = True    # 是否提权
become_method = sudo    # 提权的方式
become_user = root        # 提权到哪个用户
become_ask_pass = False    # 执行 Ansible 时是否询问提权密码
```

## 4 Ansible 主机清单

### Ansible 主机清单

主机清单用来定义哪些主机在 Ansible 的管理范围。主机清单支持以下写法：

- • IP 地址
- • 主机名
- • 范围（`server[b:c]`）
- • 主机组（webserver）
- • 组的子组（lnmp:children）


### 主机清单

```
172.25.250.10

[webserver]
server[b:c]

[mysql]
172.25.250.1[3:4]

[lnmp:children]
webserver
mysql
```

• 直接定义主机名或地址，当前 172.25.250.10 不属于任何组。

```
172.25.250.10
```

• 定义主机组，webserver 主机组包含 serverb 到 serverc

```
[webserver]
server[b:c]
```

• 组和组之间可以嵌套

```
[lnmp:children]
webserver
mysql
```

• 使用范围来匹配主机

```
server[b:c]
172.25.250.1[3:4]
```

### 选择主机和主机组

**匹配所有主机**

```
**ansible all --list-hosts
  hosts (5):
    172.25.250.10
    serverb
    serverc
    172.25.250.13
    172.25.250.14**
```

匹配指定的主机或主机组

```
ansible serverc --list-hosts
  hosts (1):
    serverc

ansible lnmp --list-hosts
  hosts (4):
    serverb
    serverc
    172.25.250.13
    172.25.250.14

```

**匹配多个主机和主机组**

```
ansible serverb,serverc --list-hosts
  hosts (2):
    serverb
    serverc

ansible webserver,mysql --list-hosts
  hosts (4):
    serverb
    serverc
    172.25.250.13
    172.25.250.14
```

**匹配没有组的主机**

```
ansible server* --list-hosts
  hosts (2):
    serverb
    serverc

ansible *25.* --list-hosts
  hosts (3):
    172.25.250.10
    172.25.250.13
    172.25.250.14
```

**通配符逻辑组合**

- • `&``：取交集
- • `!``：去反
- • `，``：取并集

```
# 匹配所有以server开头的主机，排除serverc
ansible 'server*,!serverc' --list-hosts
  hosts (1):
    serverb

# 匹配组webserver和组lnmp两个组内相同的主机
ansible 'webserver,&lnmp' --list-hosts
  hosts (2):
    serverb
    serverc

# 在上一个例子在排除serverc
ansible 'webserver,&lnmp,!serverc' --list-hosts
  hosts (1):
    serverb
```

使用正则匹配

```
# '~'后接正则表达式，下边例子表示匹配以se开头的主机
ansible '~^se' --list-hosts
  hosts (2):
    serverb
    serverc
```

通过limit匹配主机

```
ansible all --limit serverb,172.25.250.13 --list-hosts
  hosts (2):
    serverb
    172.25.250.13

# 使用文件匹配
cat <<EOF > ip.txt
serverb
serverc
172.25.250.14
EOF

ansible all --limit @/home/student/ansible/ip.txt --list-hosts
  hosts (3):
    serverb
    serverc
    172.25.250.14
```

### 特殊的主机 localhost

最后说一个特殊的主机 localhost，这是一个特殊的主机，它不需要在主机清单里声明就可以使用。

localhost 表示本地主机，未在主机清单中声明时，Ansible 会使用 local 方式连接，相当于在本地执行命令，以下是它和 SSH 连接的区别：

```
项目  local. ssh
执行位置. 控制节点本地执行. 远程通过 ssh 执行
是否走网络. 否. 是
权限控制. 当前用户. 可指定远程用户
常见用途. 控制节点配置、本地调试. 正式环境、管理多台主机
```

可以通过在主机清单内明确指定 localhost 来设置连接方式为 ssh。

**local 和 ssh 连接有个最大的区别就是 local 会忽略 `remote_user `选项，可以看以下例子：**

```
# 配置文件
[test@awx-1 ansible]$ cat ansible.cfg
[defaults]
inventory      = ./inventory
fork          = 20
ask_pass       = False
remote_user    =  remote-manager
host_key_checking = False
ansible_python_interpreter = /usr/bin/python3.9
[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False


# 当前用户
[test@awx-1 ansible]$ id
uid=1000(test) gid=1000(test) groups=1000(test) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023

# 查看主机清单，当前 localhost 已被注释
[test@awx-1 ansible]$ cat inventory
#localhost ansible_ssh_password=redhat

# 测试
[test@awx-1 ansible]$ ansible localhost -b -m command -a 'id'
localhost | CHANGED | rc=0 >>
uid=0(root) gid=0(root) groups=0(root) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
# 可以看到可以执行成功

# 删除主机清单的注释
[test@awx-1 ansible]$ cat inventory
localhost ansible_ssh_password=redhat

# 再次测试
[test@awx-1 ansible]$ ansible localhost -b -m command -a 'id'
localhost | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: remote-manager@localhost: Permission denied (publickey,gssapi-keyex,gssapi-with-mic,password,keyboard-interactive).",
    "unreachable": true
}
# 可以看到报错了，因为 test 不能直接 ssh remote-manager 用户
```

可以看到 local 方式会忽略 remote_user 选项，而 ssh 不会忽略。


## 5 Ansible 常用命令

Ansible 有多个命令，不同命令有不同的功能，以下列出一些常用的命令和常用参数。

### 常用命令

```
# 设置 ansible 的配置文件
ansible-config

# 查看 ansible 相关模块和模块文档
ansible-doc

# 查看 ansible 的主机清单
ansible-inventory

# 执行临时任务
ansible

# 执行 ansible 的 playbook
ansible-playbook

# 设置 ansible 的角色
ansible-galaxy

# ansible 相关文件加密或解密
ansible-vault
```

### Ansible-config 常用参数

```
# 生成一个注释所有配置的 ansible 配置文件
ansible-config init --disabled -t all > ./ansible.cfg
```

### Ansible-doc 常用参数

```
# 列出所有支持的模块
ansible-doc -l

# 查看模块参数
ansible-doc -s <模块名>

# 查看模块案例
ansible-doc <模块名>
# 这个可以通过搜索 EXAMPLE 来快速查看使用方法

# 按类型列出模块
ansible-doc -t cache -l        # 列出所有缓存模块
```

### Ansible-inventory 常用参数

```
# 列出所有主机
ansible-inventory -i inventory --list
-i        # 指定主机清单文件
--list    # 列出所有主机

# 查看某一个主机
ansible-inventory -i inventory --host server1

# 以树状图显示并显示变量
ansible-inventory -i inventory --graph --vars

# 以 toml 格式输出主机清单
ansible-inventory -i inventory --list --toml

# 以 yaml 格式输出主机清单
ansible-inventory -i inventory --list --yaml

# 将内容输出到文件里
ansible-inventory -i inventory --list --output file
```

### Ansible 常用参数

```
ansible all -u root -k -m command -a 'pwd chdir=/opt removes=/root/anaconda-ks.cfg'
all        # 表示需要执行任务的主机或主机组
-m        # 后接要使用的模块
command    # 使用的模块
-u root    # 指定连接用户
-k        # 手动输入密码
-a        # 后接模块的参数
```

> 有的地方可能会提到 Ansible Ad-Hoc，Ansible Ad-Hoc 就是 ansible 命令，通过命令的方式执行简单任务（一次使用一个模块执行一个任务）。

### Ansible-playbook 常用参数

```
ansible-playbook --become --become-method sudo \
    --become-user root -i inventory \
    --ask-pass --ask-become-pass playbook.yaml
--become            # 启动提权
--become-method        # 设置提权方法
--become-user        # 设置提权到什么用户
--ask-pass            # 输入远程用户密码
--ask-become-pass    # 设置提权密码

# 检查 playbook 是否有语法错误
ansible-playbook --syntax-check playbook.yaml

# 执行 playbook 但不做更改
ansible-playbook --check playbook.yaml
```

### Ansible-galaxy 常用选项

```
# 列出所有 role
ansible-galaxy role list

# 初始化一个名为 role 的 role1
ansible-galaxy role init role1

# 下载集合
ansible-galaxy collection download
```

### Ansible-vault 常用选项

```
# 创建加密文件
ansible-vault create test1

# 对已有文件进行加密
ansible-vault encrypt test

# 对字符串 username=root 进行加密
ansible-vault encrypt_string "username=root"

# 编辑加密文件
ansible-vault edit test

# 查看加密文件
ansible-vault view test

# 修改文件密码
ansible-vault rekey test

# 解密文件
ansible-vault decrypt test2
```

## 6 Ansible 模块和集合

### Ansible 模块和集合

Ansible 通过模块执行任务，例如 `ansible.builtin.hostname` 用于设置主机名。

> `ansible.builtin`  是模块的命名空间，不同的模块属于不同的命名空间，一般情况下模块名称不会冲突，但是随着模块数量的增加，不排除模块名冲突的可能，所以将不同的模块放在不同的命名空间里用于区分，如果控制节点安装的模块没有模块名冲突的情况，调用模块时可以不写命名空间，如：hostname。

> Ansible 是 Python 写的，所以 Ansible 的大部分模块都需要被控主机有 Python才能使用，少部分模块除外，如：ansible.builtin.raw。

### 文件与目录操作模块

```
模块名	功能描述	
ansible.builtin.copy		复制文件到远程主机
ansible.builtin.template	渲染 Jinja2 模板并传输
ansible.builtin.fetch	从远程主机获取文件
ansible.builtin.file	设置文件权限、属主等
ansible.builtin.stat	获取文件状态
ansible.builtin.unarchive	解压归档文件
ansible.builtin.lineinfile	修改文件中的一行内容
ansible.builtin.blockinfile	向文件中插入多个行
```

### 系统管理模块


```
模块名. 功能描述

ansible.builtin.hostname	设置主机名
ansible.builtin.user	创建/修改/删除用户
ansible.builtin.group	创建/修改/删除用户组
ansible.builtin.service	管理系统服务
ansible.builtin.systemd	 管理 systemd 服务
ansible.builtin.package	 安装/卸载通用软件包（自动选择后端）
ansible.builtin.yum	   使用 yum 安装包
ansible.builtin.apt	  使用 apt 安装包
ansible.builtin.dnf	  使用 dnf 安装包
```

### 命令执行模块

```

模块名   功能描述
ansible.builtin.command	  运行普通命令（不支持管道、重定向）
ansible.builtin.shell	  运行 shell 命令（支持管道、重定向等）
ansible.builtin.raw	      原始命令执行，不走模块系统
ansible.builtin.script	  将本地脚本上传并执行
```

### 归档与压缩

```
模块名.  功能描述
ansible.builtin.archive	   创建归档文件（如 tar.gz）
ansible.builtin.unarchive	   解压归档文件
```

### 权限与认证

```
模块名		功能描述
ansible.builtin.seboolean	管理 SELinux 布尔值
ansible.builtin.selinux	    设置 SELinux 模式
ansible.builtin.authorized_key		添加 SSH 公钥到用户 ~/.ssh/authorized_keys
```

### 网络与远程连接

```
模块名.    功能描述
ansible.builtin.uri	 发 HTTP 请求
ansible.builtin.get_url	  下载文件
ansible.builtin.wait_for	 等待端口/文件状态变更
```

### 控制逻辑模块

```
模块名			功能描述
ansible.builtin.debug		输出调试信息
ansible.builtin.pause		暂停执行
ansible.builtin.assert		条件断言
ansible.builtin.set_fact	设置自定义变量
```

> 这些模块都可以通过 `ansible-doc <模块名> ` 来查看帮助文档，可以通过搜索 EXAMPLE 来快速了解模块的使用，也可以通过 `ansible-doc -s <模块名>` 查看模块选项。

举个例子，`ansible.builtin.systemd` 模块用于服务管理，通过 ansible-doc 查看模块文档并搜索 EXAMPLE。

```
[root@awx-1 ansible]# ansible-doc ansible.builtin.systemd
# 用 /EXAMPLE 搜索
EXAMPLES:

- name: Make sure a service unit is running
  ansible.builtin.systemd:
    state: started
    name: httpd

- name: Stop service cron on debian, if running
  ansible.builtin.systemd:
    name: cron
    state: stopped

- name: Restart service cron on centos, in all cases, also issue daemon-reload to pick up config changes
  ansible.builtin.systemd:
    state: restarted
    daemon_reload: true
    name: crond

- name: Reload service httpd, in all cases
  ansible.builtin.systemd:
    name: httpd.service
    state: reloaded

- name: Enable service httpd and ensure it is not masked
  ansible.builtin.systemd:
    name: httpd
    enabled: true
    masked: no

- name: Enable a timer unit for dnf-automatic
  ansible.builtin.systemd:
    name: dnf-automatic.timer
    state: started
    enabled: true

- name: Just force systemd to reread configs (2.4 and above)
  ansible.builtin.systemd:
    daemon_reload: true

- name: Just force systemd to re-execute itself (2.8 and above)
  ansible.builtin.systemd:
    daemon_reexec: true

- name: Run a user service when XDG_RUNTIME_DIR is not set on remote login
  ansible.builtin.systemd:
    name: myservice
    state: started
    scope: user
  environment:
    XDG_RUNTIME_DIR: "/run/user/{{ myuid }}"
```

很多选项都能做到见文知意，比方说 enabled 用于设置是否开机自启，state 用于设置服务状态。

### Ansible 集合

Ansible-core 自带一组模块，以 `ansible.builtin` 开头，这些模块是远远不够的，需要其他模块来扩展 Ansible 的功能，为了方便这些模块的管理，所以将这些扩展模块分类，一类模块就组成一个集合。


![Alt Image Text](../../images/sre_ans1_3.png "Body image")

```
ansible-galaxy collection download community.general
```

```
---
- name: init    # 定义 Playbook 的名字
  hosts: all    # 定义被控主机范围
  tasks:        # 定义要执行的任务，下边一个 - 对应一个任务（一个 - 表示一个列表）
  - name: set hostname        # 第一个任务的名称
    ansible.builtin.hostname:    # 第一个任务使用的模块
      name: "{{ inventory_hostname }}.{{ host_search_name }}"    # 模块选项
  - name: dnf install package    # 第二个任务名称
    ansible.builtin.dnf:        # 第二个任务使用的模块
      name:                        # 模块选项
      - bash-completion
      - vim
      - gcc
      - make
      - git
      - wget
      - tar
      - bzip2
      - unzip
      - python3
      - sysstat
      state: present
  - name: set hosts
    ansible.builtin.template:
      src: ./templates/hosts.j2
      dest: /etc/hosts
      owner: root
      group: root
      mode: '0644'
```

- `---`：—yaml 文件的文档分隔符，多个 yaml 文档可以放在一个文件里，用---来做分隔

- `- name`：用于定义 Playbook 和 Play 的名称，类似于注释

- `hosts`：定义 Playbook 作用的主机范围

- `tasks`：定义 Playbook 的多个自动化任务，下边包含多个任务

YAML对缩进有严格要求，比方说`- name`和`hosts`是一个等级（在一个列表），所以缩进是对齐的，tasks下的`- name`和`ansible.builtin.hostname`是它的子项（是tasks下的列表），所以会多缩进两个空格（缩进以第一个字母算，不考虑-）。

```
  - name: set hosts
    ansible.builtin.template:
      src: ./templates/hosts.j2
      dest: /etc/hosts
      owner: root
      group: root
      mode: '0644'
```


这个表示一个 play，play 的名字为set hosts，使用的模块为`ansible.builtin.template`，模块使用的参数有`src、dest、owner、group、mode`。

### Ansible Playbook 命令常用选项

1. 可以通过ansible-playbook命令来执行 Playbook，通过-v可以查看更详细的信息。
  - •-v: 提供任务和主机状态的详细信息，但不包括变量值和详细调试信息。
  - •-vv: 提供更详细的任务执行信息，包括变量值。
  - •-vvv: 显示包括变量展开和任务详细信息在内的更多调试信息。
  - •-vvvv: 显示所有调试信息，包括 HTTP 请求和响应的详细内容。
    
2. 可以通过`--syntax-check`选项来对 Playbook 的格式进行检查
3. 可以通过`--check`来执行 Playbook，但并不做实际的改变


Ansible Playbook 队列错误处理机制

Ansible Playbook 的机制是如果某个主机的某个 Play 执行错误，那么这个主机就会被移除执行队列，该主机相关的后续所有 Play 都不会执行

![Alt Image Text](../../images/sre_ans1_4.png "Body image")

有时候我们可能希望任务报错后节点继续执行后边的任务，这个时候可以使用`ignore_errors: yes`来实现。

```
  - name: dnf install package
    ansible.builtin.dnf:
      name:    sysstat
      state: present
    ignore_errors: yes
```

### Ansible Handlers


Ansible Handlers 表示一组特殊的任务，它只会在特定的情况下才会被执行，例如配置 sssd 服务，可以通过 Ansible Handlers 来实现只有 sssd 服务的配置文件发生改变后才执行 sssd 服务的重启。

**Ansible Handlers 有两个常用选项，notify、handlers和listen。**

**notify：在某个 play 下追加notify后，表示这个 play 执行成功后并且做出修改后触发handlers的某个特殊任务**

#### Ansible Handlers 例子

```
---
- name: set sssd
  hosts: all
  gather_facts: false
  vars:
    sssd_packages:
    - sssd
    - sssd-tools
    - oddjob
    - oddjob-mkhomedir
    - libsss_sudo
  tasks:
  - name: install packages
    ansible.builtin.yum:
      name: "{{ sssd_packages }}"
      state: present
  - name: set sssd.conf
    ansible.builtin.template:
      src: templates/sssd.conf.j2
      dest: /etc/sssd/sssd.conf
      mode: '0600'
      owner: root
      group: root
    notify: set_pam_and_service
  - name: set oddjob service
    ansible.builtin.systemd:
      name: oddjobd
      state: started
      enabled: true
  handlers:
  - name: set authselect
    ansible.builtin.command: "authselect select sssd with-sudo with-mkhomedir --force"
    listen: set_pam_and_service
  - name: set sssd service
    ansible.builtin.systemd:
      name: sssd
      state: restarted
    listen: set_pam_and_service
```

**在这个 Playbook 中，在名为set sssd.conf的 Play 下定义了一个notify，notify的名字为 `set_pam_and_service`，然后在handlers中定义了两个 Play，分别是`set authselect`和`set sssd service`，**

这两个 Play 都一个listen选项，listen的内容均为 `set_pam_and_service`，这个实现的结果就是当`set sssd.conf`执行成功且做出改变之后就会触发`set authselect`和`set sssd service`这两个任务，当`set sssd.con`f没有做出改变时，`set authselect`和`set sssd service`这两个任务会直接被跳过。

**上述例子里是一个 Play 触发两个handlers的 Play，如果handlers里被触发的 Play 只有一个，那么可以不用listen，可以直接将handlers里 Play 的name设置为和notify内容相同。（如果handlers里多个 Play 的名字为`set_pam_and_service`，那么只有第一个 Play 会被执行）**

```
---
- name: set sssd
  hosts: all
  gather_facts: false
  tasks:
  - name: set sssd.conf
    ansible.builtin.template:
      src: templates/sssd.conf.j2
      dest: /etc/sssd/sssd.conf
      mode: '0600'
      owner: root
      group: root
    notify: set_pam_and_service
  handlers:
  - name: set_pam_and_service
    ansible.builtin.systemd:
      name: sssd
      state: restarted
```

### 强制执行 handlers

**Ansible 的机制是如果某个主机的某个 Play 执行错误，那么这个主机就会被移除执行队列，该主机相关的后续所有 Play 都不会执行，所以为了保证handlers能够正常执行，可以添加§`force_handlers: true`选项，这样无论是否有错误任务产生handlers都会正常执行**

```
---
- name: set sssd
  hosts: all
  gather_facts: false
  force_handlers: true
  tasks:
  ...
```

## 8 Ansible 变量的定义和引用

### Ansible 变量


- • Ansible 的变量区分大小写
- • Ansible 的变量不要重名，特别是不要和内置变量冲突
- • Ansible 的变量名称可以使用数字、字母和下划线组成，但是只能以字母开头

### 连接变量

连接变量是 Ansible 的内置变量，这个变量控制 Ansible 对被控主机的连接方式。

可以在主机清单、Playbook 和ansible -e处设置变量。

- • `ansible_host`~：指定清单中主机的真实 IP 地址
- • `ansible_port`：指定清单中主机的端口
- • `ansible_user`：指定清单中主机的连接用户
- • `ansible_become`：是否进行特权升级
- • `ansible_become_user`：通过特权升级到哪个用户
- • `ansible_become_password`：提升特权时，如果需要密码的话，可以通过该变量指定
- • `ansible_sudo_exec`：如果 sudo 命令不在默认路径，需要指定 sudo 命令路径
- • `ansible_connection`：ssh 连接的类型：local, ssh, paramiko，默认是 ssh
- • `ansible_ssh_password`：ssh 连接时的密码
- • `ansible_ssh_private_key_file`：秘钥文件路径，如果不想使用 ssh-agent 管理秘钥文件时可以使用此选项
- • `ansible_ssh_executable`：如果 ssh 指令不在默认路径当中，可以使用该变量来定义其路径
- • `ansible_ssh_extra_args`：额外的 ssh 参数。
- • `ansible_python_interpreter`：指定受控主机的 python 的位置


以下是在主机清单中设置变量示例：

```
[all:vars]
ansible_ssh_user = root
ansible_ssh_password = redhat
ansible_become = true
ansible_become_user = redhat
```

### 定义变量

**主机清单定义**

```
server1 MYSQL_VERSION=5.7 MYSQL_MASTER=true
[web]
server2
server3

[web:vars]
HTTPD_VERSION=2.4

[all:vars]
SYSTEM_TYPE=RHEL
```

- • 主机变量写在主机后边用空格分隔，如`MYSQL_VERSION`和`MYSQL_MASTER`
- • 组变量通过组名接`:vars`定义，如`[web:vars]`
- • 所有主机的变量可以通过`[all:vars]`来定义

也可用 yaml 格式定义主机清单

```
ungrouped:
  hosts:
    server1:
      MYSQL_VERSION: 5.7
      MYSQL_MASTER: true

web:
  hosts:
    server2:
    server3:
  vars:
    HTTPD_VERSION: 2.4

all:
  vars:
    SYSTEM_TYPE: RHEL
```

**`sssd_packages`就是定义的变量**

通过 `set_fact` 模块设置

```
- name: set fact
  hosts: localhost
  gather_facts: false
  tasks:
  - name: set fact
    ansible.builtin.set_fact:
      var1: one
      var2:
      - two
      - three
  - name: print vars
    ansible.builtin.debug:
      msg: "{{ var1 }} and {{ var2 }} "
```

**var1 和 var2 都是通过`set_fact`模块设置的变量，通过`set_fact`设置的变量可以给后续的模块使用**。

通过文件设置变量

自定义变量文件

```
cat vars_file.yml
---
var3: three
var4: four

cat test.yml
- name: set fact
  hosts: localhost
  gather_facts: false
  vars_files:
  - ./vars_file.yml
  tasks:
  - name: print vars
    ansible.builtin.debug:
      msg: "{{ var3 }} and {{ var4 }} "
```

**将变量写入 `vars_file.yml` 文件，在 playbook 中通过 `vars_files` 加载变量文件**

**设置主机变量**

```
cat host_vars/localhost/vars.yml
---
HTTPD_VERSION: 2.4
cat test.yml
- name: set fact
  hosts: localhost
  gather_facts: false
  tasks:
  - name: print vars
    ansible.builtin.debug:
      msg: "{{ HTTPD_VERSION }}"
```

在 Ansible 配置文件所在目录创建一个 `host_vars` 目录，在 `host_vars` 目录下创建和清单中对应名称或地址的目录，如`./host_vars/localhost/`（表示设置localhost主机的变量），在`./host_vars/localhost/`下创建任意名称的文件（文件取什么名字都可以，最好以`.yml`或`.yaml`结尾，方便观看），将变量写入文件，如`./host_vars/localhost/vars.yml`，Ansible 会自动取读取同名目录下的文件，Playbook 可以直接引用变量。

**设置主机组变量**

```
cat inventory
[webserver]
server1
server2
cat group_vars/webserver/vars.yml
---
SYSTEM_TYPE: RHEL
cat test.yml
- name: set fact
  hosts: webserver
  gather_facts: false
  tasks:
  - name: print vars
    ansible.builtin.debug:
      msg: "{{ SYSTEM_TYPE }}"
```

和 `host_vars` 类似，只不过主机换成了组。

**注册变量**

```
cat test.yml
- name: set fact
  hosts: webserver
  gather_facts: false
  tasks:
  - name: register
    ansible.builtin.command: id
    register: register_var
  - name: print vars
    ansible.builtin.debug:
      msg: "{{ register_var }}"
```

在模块执行成功后可以通过register来将输出注册为变量，注册的变量输出如下

```
ok: [server1] => {
    "msg": {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/libexec/platform-python"
        },
        "changed": true,
        "cmd": [
            "id"
        ],
        "delta": "0:00:00.004153",
        "end": "2024-08-04 23:09:14.460466",
        "failed": false,
        "msg": "",
        "rc": 0,
        "start": "2024-08-04 23:09:14.456313",
        "stderr": "",
        "stderr_lines": [],
        "stdout": "uid=0(root) gid=0(root) groups=0(root) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023",
        "stdout_lines": [
            "uid=0(root) gid=0(root) groups=0(root) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023"
        ]
    }
}
```

这里有价值的几个输出为failed、rc、stderr、stderr_lines、stdout和stdout_lines，这几个输出可用于判断执行是否成功和后续模块对这次执行结果内容的引用。

### 通过 Ansible Ad-Hoc 设置变量

```
ansible localhost -e SYSTEM_TYPE=RHEL -m debug -a 'msg="{{ SYSTEM_TYPE }}"'
```

使用-e选项设置变量，优先级最高。

### Ansible Playbook 引用变量

**通过` {{}}` 引用**

```
---
- name: set sssd
  hosts: all
  gather_facts: false
  vars:
    sssd_packages:
    - sssd
    - sssd-tools
    - oddjob
    - oddjob-mkhomedir
    - libsss_sudo
  tasks:
  - name: install packages
    ansible.builtin.yum:
      name: "{{ sssd_packages }}"
```

playbook 在使用变量的时候需要注意双引号的使用，如果某个段落的开头就是调用变量，即以{{开头，那么这个段落就需要使用双引号引起来，但如果开始是字符串，那么可以不使用双引号。

```
---
- name: vars example
  hosts: all
  vars:
    var1: one
    var2: two
  tasks:
  - name: debug1
    ansible.builtin.debug:
      msg: vars is {{ var1 }} and {{ var2 }}
  - name: debug2
    ansible.builtin.debug:
      msg: "{{ var1 }} and {{ var2 }}"
```

template 模块也可以使用变量，使用方法和上边一样。

### 通过 [] 引用

`[]`引用和`{{}}`的区别就是`[]`内的值会被当成变量处理，举个例子：

```
---
- name: test
  hosts: localhost
  vars:
    netcard: ens18
  tasks:
  - name: loop list
    debug:
      msg: "{{ ansible_facts[netcard].ipv4.address }}"
```

上边的`"{{ ansible_facts[netcard].ipv4.address }}"`使用了`[]`，netcard也会被”成变量处理，实际要打印的变量为`ansible_facts.ens18.ipv4.address`。

> 有的地方在引用变量是会使用['']，这个因为带有''，所以里边的值不会当成变量处理，比如：ansible_facts['ens18']ipv4.address就是ansible_facts.ens18.ipv4.address。
>
> 注意使用[]时，左边是没有.的，举个例子：ansible_facts[netcard].ipv4['address']。

## 9 Ansible 事实变量和魔法变量

事实变量

顾名思义事实变量就是根据事实定义的变量。比方说被控主机的配置信息，如 cpu、内存、硬盘、IP 和系统版本等信息。