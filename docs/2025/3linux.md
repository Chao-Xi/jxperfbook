# 250 Linux Scenario Based Interview

**Q1: How would you find all files larger than 100MB in a directory and its subdirectories?**


`find /path/to/directory -type f -size +100M`


- `-type f`: Finds only files.
- `-size +100M`: Specifies files larger than 100MB.


**Q3: How do you recursively delete all .log files in a directory?**

```
find /path/to/directory -name "*.log" -type f -delete
```

- `-name "*.log”` : Matches files with .log extension.
- `-type f`: Targets only files.
- `-delete`: Deletes the files found.

**Q4: How do you create a tarball of /var/log and compress it with gzip?**

```
tar -czvf logs.tar.gz /var/log
```

- -c: Creates a tarball.
- -z: Compresses using gzip.
- -v: Verbose output.
- -f logs.tar.gz: Specifies the tarball file name.

**Q5: Test specific port availability:**

`nc -zv <server_ip> <port>`


**Q6: How do you permanently assign an IP address in Linux?**

Edit the network configuration file:

`vi /etc/sysconfig/network-scripts/ifcfg-eth0`

```
IPADDR=192.168.1.100
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
```

`sudo systemctl restart network`


**Q7: How do you identify a process consuming high CPU?**

```
ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%cpu | head
```

