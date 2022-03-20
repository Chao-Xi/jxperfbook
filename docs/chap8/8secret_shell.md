# **8 Secret shell 脚本**

### **1 根据 md5 校验码,检测文件是否被修改**

```
#!/bin/bash

# 根据 md5 校验码,检测文件是否被修改 
# 本示例脚本检测的是/etc 目录下所有的 conf 结尾的文件,根据实际情况,您可以修改为其他目录或文件
# 本脚本在目标数据没有被修改时执行一次,当怀疑数据被人篡改,再执行一次
# 将两次执行的结果做对比,MD5 码发生改变的文件,就是被人篡改的文件
for i in $(ls /etc/*.conf)
do
  md5sum "$i" >> /var/log/conf_file.log
done
```

### **2 非交互自动生成 SSH 密钥文件**

```
#!/bin/bash

# 非交互自动生成 SSH 密钥文件 
 
# ‐t 指定 SSH 密钥的算法为 RSA 算法;‐N 设置密钥的密码为空;‐f 指定生成的密钥文件>存放在哪里
rm  -rf  ~/.ssh/{known_hosts,id_rsa*}
ssh‐keygen -t RSA -N '' -f ~/.ssh/id_rsa
```

### **3 生成随机密码(urandom 版本)**

```
#!/bin/bash

# 生成随机密码(urandom 版本) 
 
# /dev/urandom 文件是 Linux 内置的随机设备文件
# cat /dev/urandom 可以看看里面的内容,ctrl+c 退出查看
# 查看该文件内容后,发现内容有些太随机,包括很多特殊符号,我们需要的密码不希望使用这些符号
# tr ‐dc '_A‐Za‐z0‐9' < /dev/urandom
# 该命令可以将随机文件中其他的字符删除,仅保留大小写字母,数字,下划线,但是内容还是太多
# 我们可以继续将优化好的内容通过管道传递给 head 命令,在大量数据中仅显示头 10 个字节
# 注意 A 前面有个下划线
tr -dc '_A‐Za‐z0‐9' </dev/urandom | head -c 10
```

### **4 生成随机密码(字串截取版本)**

```

#!/bin/bash

# 生成随机密码(字串截取版本) 
 
# 设置变量 key,存储密码的所有可能性(密码库),如果还需要其他字符请自行添加其他密码字符
# 使用$#统计密码库的长度
key="0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"
num=${#key}
# 设置初始密码为空
pass=''
# 循环 8 次,生成随机密码
# 每次都是随机数对密码库的长度取余,确保提取的密码字符不超过密码库的长度
# 每次循环提取一位随机密码,并将该随机密码追加到 pass 变量的最后
for i in {1..8}
do  
  index=$[RANDOM%num]
  pass=$pass${key:$index:1}
done
echo $pass
```

### **5 生成随机密码(UUID 版本,16 进制密码)**

```
#!/bin/bash

# 生成随机密码(UUID 版本,16 进制密码) 
uuidgen
```


### **6、生成随机密码(进程 ID 版本,数字密码)**

```
#!/bin/bash

# 生成随机密码(进程 ID 版本,数字密码)
echo $$
```

### **7、测试用户名与密码是否正确**  

```
#!/bin/bash

# 测试用户名与密码是否正确
 
#用户名为 tom 并且密码为 123456,则提示登录成功,否则提示登录失败
read -p "请输入用户名:"  user
read -p "请输入密码:"    pass
if [ "$user" == 'tom' -a "$pass" == '123456' ];then
  echo "Login successful"
else
  echo "Login Failed"
fi
```

### **8、循环测试用户名与密码是否正确**

```
#!/bin/bash

# 循环测试用户名与密码是否正确 
 
# 循环测试用户的账户名和密码,最大测试 3 次,输入正确提示登录成功,否则提示登录失败
# 用户名为 tom 并且密码为 123456  
for i in {1..3}
do
  read -p "请输入用户名:" user
  read -p "请输入密码:"   pass
if [ "$user" == 'tom' -a "$pass" == '123456' ];then
    echo "Login successful"
     exit
fi
done
echo "Login Failed"
```

### **9、找出`/etc/passwd` 中能登录的用户,并将对应在`/etc/shadow` 中第二列密码提出处理**

```
#!/bin/bash

# 找出/etc/passwd 中能登录的用户,并将对应在/etc/shadow 中第二列密码提出处理
 
user=$(awk -F: '/bash$/{print $1}' /etc/passwd)
for i in $user
do
  awk -F: -v x=$i '$1==x{print $1,$2}' /etc/shadow
done
```

### **10、统计`/etc/passwd` 中 root 出现的次数**

```
!/bin/bash

# 统计/etc/passwd 中 root 出现的次数 
 
#每读取一行文件内容,即从第 1 列循环到最后 1 列,依次判断是否包含 root 关键词,如果包含则 x++
awk -F: '{i=1;while(i<=NF){if($i~/root/){x++};i++}} END{print "root 出现次数为"x}' /etc/passwd
```

### **11 生成签名私钥和证书**

```
#!/bin/bash

# 生成签名私钥和证书 
 
read -p "请输入存放证书的目录:" dir
if [ ! -d $dir ];then
  echo "该目录不存在"
  exit
fi
read -p "请输入密钥名称:" name
# 使用 openssl 生成私钥
openssl genrsa -out ${dir}/${name}.key
# 使用 openssl 生成证书 #subj 选项可以在生成证书时,非交互自动填写 Common Name 信息
openssl req -new -x509 -key ${dir}/${name}.key -subj "/CN=common" -out ${dir}/${name}.crt
```