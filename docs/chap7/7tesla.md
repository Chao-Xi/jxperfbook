# **7 Tesla** 

[Instrumenting Ruby on Rails with Prometheus](https://firehydrant.io/blog/instrumenting-ruby-on-rails-with-prometheus/)

[How To Use Prometheus Adapter to Autoscale Custom Metrics Deployments](https://hackernoon.com/how-to-use-prometheus-adapter-to-autoscale-custom-metrics-deployments-p1p3tl0)


### **The technical problem**

Setup **customizatized** automatically HPA(horizontal Pod AutoScaler) infrastructure for our web application server pods based on a large amount of web server quests during a short of times. 

Our web application is running with Nginx server inside Kubernetes pod, we want to set up an automatically scaling up/down policy based on customized NGINX metrics for example when a large amount of requests come, we want our web application server will automatically scaling up and scaling down when requests dropping in the session of time.

Default Kubernetes HPA policies are only based on CPU and memory. 
We already installed Prometheus operator inside our cluster as our monitor tool, so I choose nginx-prometheus-exporter to export nignx service metrics, Prometheus scrape metrics from services, and prometheus-adpater pull metrics and APIServer expose metrics to be used by Horizontal Pod Autoscaling object.



### **Why was it hard?**

Normally we only use Prometheus/Granfana as monitor solutions and only use CPU and Memory to scale the deployment. How to combine two solutions to enable HPA inside the Kubernetes cluster needs strong experience on both Prometheus and Kubernetes, and the Prometheus-adapter is pretty complex

### **How did solve it?**

![Alt Image Text](../images/chap7_7_1.png "Body image")
  
Inject `nginx-prometheus-exporter` as sidecar inside the web application pod to export niginx metrics to Prometheus, use `nginx_http_requests_total` as HPA metrics. And make sure Prometheus can collect all these nginx metrics.

Config Prometheus adapter configmap for the metrics, like seriesQuery: `nginx_http_requests_total`, and create Prometheus adapater deployment, service and APIService

Create Kubernetes HorizontalPodAutoscaler(HPA) to set up HPA policy based on the metrics name created by Prometheus adapter and we can also set up target value for the scaling up/down threshold.

Then the Kubernetes API server will route the request to the Prometheus adapter custom API server to trigger the scaling actions.


### **What alternative solutions did you consider and why didn't you choose them?**


KEDA is a Kubernetes-based Event Driven Autoscaler. KEDA can drive the scaling of any container in Kubernetes based on the number of events needing to be processed.

KEDA main component Scaler represents event sources that KEDA can scale based on a queue services like AWS Kinesis Stream, Kafka topic, RabbiqMQ queue, Redis Streams. 

So this solution is better for backend services like queue consumers to do the SQL query job and need to set up another set **metrics adapter, contoller and Scaler** not perfect for front-end web service or web server as loadbalancer.



## Difference between deployment vs StatefulSets vs Damemonset

### **Deployment**

* It will check that the desired state of ReplicaSet, so it will create a ReplicaSet,
* Deployments are usually used for stateless applications
* All the pods of deployment will be sharing the **same Volume** and  data across all of them will be the same which can lead to data inconsistency.
* **Deployments create a ReplicaSet which then creates a Pod** so whenever you update the deployment using RollingUpdate(default) strategy

* pod之间没有顺序
* 所有pod共享存储
* pod名字包含随机数字
* service都有ClusterIP,可以负载均衡

### **StatefulSets**


* StatefulSet is a Kubernetes resource used to manage stateful applications
* Provides the guarantee about the **ordering and uniqueness of these Pods**.
* **StatefulSet doesn’t create ReplicaSet rather itself but creates the Pod with a unique naming convention.** 
* **Every replica of a stateful set will have its own state, and each of the pods will be creating its own PVC(Persistent Volume Claim)**. 
* **StatefulSets don’t create ReplicaSet so you can't roll back a StatefulSet to a previous version.** You can only delete or scale up/down the Statefulset. 
* StatefulSets are useful in the case of Databases especially when we need **Highly Available Databases in production**
	* Stable, unique network identifiers.
	* Stable, persistent storage.
	* Ordered, graceful deployment and scaling.
	* Ordered, automated rolling updates.

* 部署、扩展、更新、删除都要有顺序
* 每个pod都有自己存储，所以都用volumeClaimTemplates，为每个pod都生成一个自己的存储，保存自己的状态
* pod名字始终是固定的
* service没有ClusterIP，是headlessservice，所以无法负载均衡，返回的都是pod名，所以pod名字都必须固定，
* StatefulSet在Headless Service的基础上又为StatefulSet控制的每个Pod副本创建了一个DNS域名:`$(podname).(headless server name).namespace.svc.cluster.local`

### **Damemonset**

* **pod runs on all the nodes of the cluster**. If a node is added/removed from a cluster, DaemonSet automatically adds/deletes the pod.
* **Daemonset automatically doesn’t run on nodes that have a taint** e.g. Master. You will have to specify the tolerations for it on the pod.
* **All pods are sharing the same volume**
* If you update a DaemonSet, it also performs RollingUpdate i.e. one pod will go down and the updated pod will come up
* Some typical uses of a DaemonSet are:

	* running a cluster storage daemon on every node 比如 glusterd 或 ceph。
	* running a logs collection daemon on every node 比如 flunentd 或 logstash。
	* running a node monitoring daemon on every node 比如 Prometheus Node Exporter 或 collectd。

## Difference hardlink and symlink

Linux系统是通过link的数量来控制文件删除的，**只有当一个文件不存在任何link的时候，这个文件才会被删除。**

一般来说每个文件两个link计数器来控制：**`i_count`和`i_nlink`。**

* **当一个文件被一个程序占用的时候`i_count`就加1。**
* **当文件的硬链接多一个的时候`i_nlink`也加1。**
* 删除一个文件，就是让这个文件，没有进程占用，同时`i_link`数量为0。


* 默认不带参数的情况下，**ln创建的是硬链接，带`-s`参数的ln命令创建的是软链接**。
* **硬链接文件与源文件的inode节点号相同**，**而软链接文件的`inode`节点号，与源文件不同**
* **ln命令不能对目录创建硬链接，但可以创建软链接。对目录的软链接会经常使用到**。
* **删除文件**
	*  **<mark>删除软链接文件，对源文件和硬链接文件无任何影响</mark>**。
	*  **<mark>删除文件的硬链接文件，对源文件及软链接文件无任何影响</mark>**。
	*  **<mark>删除链接文件的源文件，对硬链接文件无影响，会导致其软链接失效（红底白字闪烁状）</mark>**
	*  **<mark>同时删除源文件及其硬链接文件，整个文件才会被真正的删除。</mark>**
* 很多硬件设备的快照功能，使用的就是类似硬链接的原理

## How to block one port on server

```
$ iptables -t nat -A PREROUTING -d 10.0.0.254 -p tcp --dprot 80 -j DNAT --to-destination 10.0.0.254:8080
```

* `-t`：指定需要维护的防火墙规则表 filter、nat、mangle或raw。在不使用 `-t` 时则默认使用 filter 表。
* `-d`, `--destination`: Destination specification
* `--to-destination`: This allows you to DNAT connections in a round-robin way over a given range of destination addresses.

 
## **Whats proc folder in Linux**

It is a **Virtual File System**. 

**Contained within the procfs are information about processes and other system information.** It is mapped to **`/proc`** and **mounted** at boot time.

The files contain system information **such as memory (meminfo), CPU information (cpuinfo), and available filesystems**.
 
* /proc/cmdline – Kernel command line information.
* /proc/console – Information about current consoles including tty.
* /proc/devices – Device drivers currently configured for the running kernel.
* /proc/dma – Info about current DMA channels.
* /proc/fb – Framebuffer devices.
* /proc/filesystems – Current filesystems supported by the kernel.
* /proc/iomem – Current system memory map for devices.
* /proc/ioports – Registered port regions for input output communication with device.
* /proc/loadavg – System load average.
* /proc/locks – Files currently locked by kernel.
* /proc/meminfo – Info about system memory (see above example).
* /proc/misc – Miscellaneous drivers registered for miscellaneous major device.
* /proc/modules – Currently loaded kernel modules.
* /proc/mounts – List of all mounts in use by system.
* /proc/partitions – Detailed info about partitions available to the system.
* /proc/pci – Information about every PCI device.
* /proc/stat – Record or various statistics kept from last reboot.
* /proc/swap – Information about swap space.
* /proc/uptime – Uptime information (in seconds).
* /proc/version – Kernel version, gcc version, and Linux distribution installed.

## Find the most n repeated word in a string

**N largest values in dictionary**

```
from collections import Counter

def getNthFrequency(str, n):
    array = str.split(' ')
    res_array = []
    i = 1
    # return array
    res_dict = dict(Counter(array))
    # return result
    while i <= n:
        maximum_key = max(res_dict, key=res_dict.get)
        res_array.append(maximum_key)
        res_dict.pop(maximum_key)
        i += 1
    
    return res_array
    

if __name__ == '__main__':
    str = "In this program, to use Counter, we have to import it from the collections class in our program. Since Counter works for hashable objects they are accessed using a key. We can get the Key using the get method."
    print(getNthFrequency(str,2))
```

**`['the', 'to']`**

```
from collections import Counter
from heapq import nlargest


def getNthFrequency(str, N):
    array = str.split(' ')
    res_array = []
    i = 1
    # return array
    res_dict = dict(Counter(array))
    # N largest values in dictionary
    # Using nlargest
    res = nlargest(N, res_dict, key = res_dict.get)
    return res 

if __name__ == '__main__':
    str = "In this program, to use Counter, we have to import it from the collections class in our program. Since Counter works for hashable objects they are accessed using a key. We can get the Key using the get method."
    print(getNthFrequency(str,3))
```

```
['the', 'to', 'using']
```
