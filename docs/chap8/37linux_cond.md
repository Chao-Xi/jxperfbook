# **Shell脚本的流程控制知识点汇总(2024)**

* 条件判断：if语句和case语句
* 循环控制：for，while和until循环
* 跳出循环：break和continue
* 错误处理与调试

## 1. if语句

###  基本格式

```
if [ 条件1 ]; then
    # 命令1，如果符合条件1则执行命令1
elif [ 条件2 ]; then
    # 命令2，如果符合条件2则执行命令2
else
    # 命令3，如果以上条件都不符合则执行命令3
fi
```

示例：检查服务状态并重启

```

#!/bin/bash
SERVICE="nginx"  # 定义要检查的服务

if systemctl is-active --quiet $SERVICE; then
    echo "$SERVICE is running"
else
    echo "$SERVICE is not running"
    systemctl start $SERVICE
fi
```

### 1.2 case语句

case 语句是多分支条件判断的简洁形式，特别适合处理多个分支情况

```
case "变量" in
    值1）
        指令1
        ;;
    值2）
        指令2
        ;;
    *）
        指令3 # 前面都不符合就执行指令3
esac
```

**示例：菜单选择**

```
#!/bin/bash
while true; do
    echo "1. 启动服务"
    echo "2. 停止服务"
    echo "3. 检查服务"
    echo "4. 退出"
    read -p "Choose an option: " choice

    case $choice in
        1)
            echo "启动服务..."
            systemctl start nginx
            ;;
        2)
            echo "停止服务..."
            systemctl stop nginx
            ;;
        3)
            echo "服务状态:"
            systemctl status nginx
            ;;
        4)
            echo "退出..."
            break
            ;;
        *)
            echo "无效值. 请再次输入."
            ;;
    esac
done
```

### 1.3 条件判断语法扩展

详细的可以参考：Shell脚本基础知识点汇总

常用条件：

* `-e file`：文件存在
* `-f file`：普通文件
* `-d file`：目录
* `-L file`：符号链接
* `-s file`：文件非空

权限相关：

* `-r file`：可读
* `-w file`：可写
* `-x file`：可执行

字符串比较：

* `=`：字符串相等
* `!=`：字符串不等
* `-z str`：字符串为空
* `-n str`：字符串非空

整数比较：

* `-eq`：等于
* `-ne`：不等
* `-lt`：小于
* `-le`：小于等于
* `-gt`：大于
* `-ge`：大于等于

复合判断：

* `&&`：逻辑与
* `||`：逻辑或

```
if [ $num1 -gt 10 ] && [ $num2 -lt 20 ]; then
    echo "Both conditions are true."
fi
```

## **2. 循环控制**

### 2.1 for 循环

**遍历列表**

```
for item in item1 item2 item3; do
    echo "Processing $item"
done
```

**遍历文件**

```
for file in /var/log/*.log; do
    echo "Log file: $file"
done
```

**示例：批量压缩日志文件**

```
#!/bin/bash
LOG_DIR="/var/log/myapp"  # 定义应用所在的目录
BACKUP_DIR="/backup/logs"  # 备份的目录

for log in $LOG_DIR/*.log; do
    gzip -c "$log" > "$BACKUP_DIR/$(basename $log).gz"
    echo "Compressed $log"
done
```

### 2.2 while 循环

**基本格式**

```
while [ condition ]; do
    # commands
done
```

示例：监控磁盘空间

```
#!/bin/bash
THRESHOLD=80

while true; do
    USAGE=$(df / | grep '/' | awk '{print $5}' | sed 's/%//')
    if [ $USAGE -gt $THRESHOLD ]; then
        echo "Disk usage is above $THRESHOLD%. Clearing logs."
        rm -f /var/log/myapp/*.log
    fi
    sleep 60
done
```

### 2.3 until 循环

```
until [ condition ]; do
    # commands
done
```

**生产示例：等待服务启动**

```
#!/bin/bash
SERVICE="nginx"

until systemctl is-active --quiet $SERVICE; do
    echo "Waiting for $SERVICE to start..."
    sleep 5
done
echo "$SERVICE is now running."
```

## 3. 跳出循环

### 3.1 break

**立即跳出当前循环。**

```
for i in {1..10}; do
    if [ $i -eq 5 ]; then
        break
    fi
    echo "Number: $i"
done
```

### 3.2 continue

**跳过当前迭代，开始下一次循环。**

```
for i in {1..10}; do
    if [ $i -eq 5 ]; then
        continue
    fi
    echo "Number: $i"
done
```

**示例：批量文件操作，跳过特定文件**

```
#!/bin/bash
for file in /data/*; do
    if [[ "$file" == *".tmp" ]]; then
        echo "Skipping temporary file: $file"
        continue
    fi
    echo "Processing $file"
    mv "$file" /backup/
done
```

## 4. 函数

### 4.1 基本格式

```
my_function() {
    # commands
}
```

### 4.2 参数处理

* `1, 2,` ...：函数参数
* `$@`：所有参数
* `$#`：参数个数

**示例：检查目录是否存在**

```
#!/bin/bash

check_directory() {
    if [ ! -d "$1" ]; then
        echo "Directory $1 does not exist. Creating..."
        mkdir -p "$1"
    else
        echo "Directory $1 exists."
    fi
}

check_directory "/var/log/myapp"
check_directory "/backup/logs"
```

* **`set -e`：脚本遇到错误即退出**
* **`set -u`：使用未定义变量时报错**
* **`set -o pipefai`l：管道命令中任何命令失败即退出**

示例：确保脚本安全执行

```
#!/bin/bash
set -euo pipefail

if [ -z "$1" ]; then
    echo "Usage: $0 <filename>"
    exit 1
fi

echo "Processing file $1..."
```

### 5.2 调试模式

`set -x`：显示命令执行过程 

`set +x`：关闭调试模式

示例：调试脚本

## 6. 生产案例

### 6.1 定期清理过期文件

清理某个目录7天前的文件

```
#!/bin/bash
TARGET_DIR="/tmp"  # 需要清理的目录
DAYS=7    # 时间

find "$TARGET_DIR" -type f -mtime +$DAYS -exec rm -f {} \;
echo "Cleaned files older than $DAYS days in $TARGET_DIR."
```

### 6.2 自动备份数据库

```
#!/bin/bash
DB_NAME="mydb"  # 数据库名称
BACKUP_DIR="/backup/db"   # 备份目录
TIMESTAMP=$(date +%Y%m%d%H%M)

mysqldump -u root -p"$DB_PASS" "$DB_NAME" > "$BACKUP_DIR/$DB_NAME-$TIMESTAMP.sql"
echo "Backup of $DB_NAME completed at $BACKUP_DIR/$DB_NAME-$TIMESTAMP.sql"
```

### 6.3 批量处理文件

```
#!/bin/bash
INPUT_DIR="/data/input"
OUTPUT_DIR="/data/output"

for file in $INPUT_DIR/*; do
    echo "Processing $(basename $file)..."
    cp "$file" "$OUTPUT_DIR/$(basename $file)"
done
```