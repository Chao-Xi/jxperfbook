# **2021 Apple Interview**

## **1 How does the PXE boot process work?**

**Dynamic Host Configuration Protocol**

Using your DHCP server to store and serve this information looks like this:

* **The device sends out a DHCP broadcast and states that it needs to PXE boot**
* The DHCP server picks up this broadcast and replies with a suggested IP address to use. 
	* If the server has the information on how to PXE boot, that information is included in its reply
* The device then replies to the server and uses the provided address
* Then the device contacts the PXE boot server (traditional a WDS server or SCCM server) and requests the boot file (also known as the Network Boot Program (NBP)) that it was told to look for from the DHCP server
* The file is then loaded and launched on the client

## 2 Check Linux memory

`/proc/meminfo`


* **Free memory is the amount of memory which is currently not used for anything.** This number should be small, because memory which is not used is simply wasted.

* **Available memory is the amount of memory** which is available for allocation to a new process or to existing processes.



## **3 How do I add a DNS server via resolv.conf?**

`resolv.conf` is the name of a computer file used in various operating systems to configure the system's Domain Name System resolver. 

The `/etc/resolv.conf` file defines how the system uses DNS to resolve host names and IP addresses. 

This file usually contains a line specifying the search domains and up to three lines that specify the IP addresses of DNS server. The following entries from `/etc/resolv.conf` configure two search domains and three DNS servers:

```
search us.mydomain.com mydomain.com
nameserver 192.168.154.3
nameserver 192.168.154.4
nameserver 10.216.106.3
```

If your system obtains its IP address from a DHCP server, it is usual for the system to configure the contents of this file with information also obtained using DHCP.



