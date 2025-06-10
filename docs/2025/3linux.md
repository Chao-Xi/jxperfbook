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

**Q8: How do you monitor disk I/O in real-time?**

`iostat -x 2`

- -x: Shows extended statistics.
- 2: Refreshes every 2 seconds.

**Q9: How do you kill a process by its name?**

```
pkill <process_name>


pgrep <process_name>

```

**Q11: How do you set up a basic firewall rule to allow SSH traffic?**

```
sudo ufw allow ssh

sudo ufw status
```

### How do you check for unauthorized access attempts on your server?

```
sudo cat /var/log/auth.log | grep "Failed password"
```

### Write a script to check if a file exists and display its permissions.

```
#!/bin/
FILE=$1
if [ -e "$FILE" ]; then
 echo "File exists."
 ls -l "$FILE" | awk '{print $1}'
else
 echo "File does not exist."
fi
```

 ./script.sh filename


### How would you write a script to archive logs older than 7 days?

```
#!/bin/
find /var/log -type f -mtime +7 -exec tar -rvf old_logs.tar {} \; -exec rm {} \;
```

- `-mtime +7`: Finds files older than 7 days.
- `-exec`: Executes tar to archive and rm to delete

### How do you add a new user with a specific home directory?

```
sudo useradd -m -d /custom/home/username username
```

- `-m`: Creates the home directory.
- `-d`: Specifies the custom directory.

**How do you change the default shell for a user?**

```
sudo usermod -s /bin/ username

cat /etc/passwd | grep username
```

**Inspect running processes:**

```
ps aux --sort=-%cpu | head

# Check I/O operations:
iostat -x 2

# Verify memory usage:
free -m
```

#### How do you extend a mounted LVM partition?

```
sudo lvextend -L +10G /dev/mapper/vol_group-lv_name

sudo resize2fs /dev/mapper/vol_group-lv_name
```

#### How do you find the top 10 largest files in a directory?

```
find /path/to/dir -type f -exec du -h {} + | sort -rh | head -n 10
```

#### How do you back up a directory using rsync?

```
rsync -av /source/directory /destination/director
```

- -a: Archive mode.
- -v: Verbose output

#### How do you restore files from a tar backup?

Use logrotate:

```
sudo logrotate /etc/logrotate.conf --force
```

### How do you view the last 50 lines of a log file?

```
tail -n 50 /var/log/syslog
```

### How do you check the groups a user belongs to?

```
groups username
```

