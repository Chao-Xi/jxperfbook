# 未安装telnet 和 nc 等工具，如何测试端口是否开放？

在Unix/Linux环境下，你可以使用echo命令结合特殊的设备文件/dev/tcp/hostname/port 来间接测试一个远程主机上的指定端口是否开放。

这种方式利用了Bash shell的一些特性，尝试通过TCP连接来检测端口状态。命令格式如下：

```
Bash
echo > /dev/tcp/目标主机地址/端口号
```

例如，想要测试服务器example.com的80端口是否开放，你可以运行

```
Bash
echo > /dev/tcp/example.com/80
```

**当命令执行后，若端口是开放的，命令不会有任何输出并且会立即返回命令提示符；**

若端口未开放或连接失败，则可能由于网络问题、防火墙限制等原因导致命令执行失败，通常会显示错误信息，比如“Permission denied”、“Connection refused”或“Network is unreachable”。

不过需要注意的是，这种方法只适用于那些支持此特性的Bash环境，不具有普遍性，而且它并不会像专门的网络工具（如telnet、nc或netcat）那样提供详细的连接状态信息。在Windows系统下，你需要使用其他工具或命令来测试端口。

## Script

```
for port in {1..65535}; do
  echo >/dev/tcp/google.com/$port &&
    echo "port $port is open" ||
    echo "port $port is closed"
done
```

This code loops over ports 1-65535 and tries to open google.com:$port. H**owever, this doesn't work that well because if the port is closed, it takes bash like 2 minutes to realize that**.

To solve this I needed something like `alarm(2)` system call to interrupt bash. 

Bash doesn't have a built-in alarm function, so I had to write a helper program in Perl to handle SIGALRM.

```
alarm() {
  perl -e '
    eval {
      $SIG{ALRM} = sub { die };
      alarm shift;
      system(@ARGV);
    };
    if ($@) { exit 1 }
  ' "$@";
}
```

This alarm function takes two args – seconds for the alarm call and the code to execute. If the code doesn't execute in the given time, the function fails.

Once I had this, I could take my earlier code and just call it through alarm:

```
for port in {1..65535}; do
  alarm 1 "echo >/dev/tcp/google.com/$port" &&
    echo "port $port is open" ||
    echo "port $port is closed"
done
```

This is working! Now if bash freezes because of a closed port, alarm 1 will kill the probe in 1 second and the script will move to the next port.

I went ahead and turned this into a proper scan function:

```
scan() {
  if [[ -z $1 || -z $2 ]]; then
    echo "Usage: $0 <host> <port, ports, or port-range>"
    return
  fi

  local host=$1
  local ports=()
  case $2 in
    *-*)
      IFS=- read start end <<< "$2"
      for ((port=start; port <= end; port++)); do
        ports+=($port)
      done
      ;;
    *,*)
      IFS=, read -ra ports <<< "$2"
      ;;
    *)
      ports+=($2)
      ;;
  esac

  for port in "${ports[@]}"; do
    alarm 1 "echo >/dev/tcp/$host/$port" &&
      echo "port $port is open" ||
      echo "port $port is closed"
  done
}
```

ou can run the scan function from your shell. It takes two arguments – the host to scan and a list of ports to scan (such as 22,80,443), or a range of ports to scan (such as 1-1024), or an individual port to scan (such as 80).

Here is what happens when I run scan google.com 78-82.

```
$ scan google.com 78-82 
port 78 is closed
port 79 is closed
port 80 is open
port 81 is closed
port 82 is closed
```

Similarly you can write an udp port scanner. Just replace /dev/tcp/ with /dev/udp/.

### timeout

```
$ timeout 1 bash -c "echo >/dev/tcp/$host/$port" &&
    echo "port $port is open" ||
    echo "port $port is closed"
```

