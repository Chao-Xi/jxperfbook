## 1. Configmap reloader

Configmap或Secret使用有两种方式，

* **一种是`env`系统变量赋值，一种是`volume`挂载赋值**
* **`env`写入系统的`configmap`是不会热更新的，而`volume`写入的方式支持热更新**！

* **对于env环境的，必须要滚动更新pod才能生效，也就是删除老的pod，重新使用镜像拉起新pod加载环境变量才能生效。**
* 对于volume的方式，虽然内容变了，但是需要我们的应用直接监控configmap的变动，或者一直去更新环境变量才能在这种情况下达到热更新的目的。

**应用不支持热更新，可以在业务容器中启动一个sidercar容器，监控configmap的变动，更新配置文件，或者也滚动更新pod达到更新配置的效果。**

### 解决方案

ConfigMap 和 Secret 是 Kubernetes 常用的保存配置数据的对象，你可以根据需要选择合适的对象存储数据。通过 Volume 方式挂载到 Pod 内的，kubelet 都会定期进行更新。但是通过环境变量注入到容器中，这样无法感知到 ConfigMap 或 Secret 的内容更新。

目前如何让 Pod 内的业务感知到 ConfigMap 或 Secret 的变化，还是一个待解决的问题。但是我们还是有一些 Workaround 的。

**如果业务自身支持 reload 配置的话，比如nginx -s reload，可以通过 inotify 感知到文件更新，或者直接定期进行 reload（这里可以配合我们的 readinessProbe 一起使用）。**

Configmap或Secret使用有两种方式，一种是`env`系统变量赋值，一种是`volume`挂载赋值，`env`写入系统的`configmap`是不会热更新的，而`volume`写入的方式支持热更新！

### **自动更新**

`reloader.stakater.com/search` 和 `reloader.stakater.com/auto `并不在一起工作。

如果你在你的部署上有一个 `reloader.stakater.com/auto : "true"`的注释，该资源对象引用的所有configmap或这secret的改变都会重启该资源，**不管他们是否有 `reloader.stakater.com/match : "true"`的注释**。

```
kind: Deployment
metadata:
  annotations:
    reloader.stakater.com/auto: "true"
spec:
  template: metadata:
```

### **制定更新**

指定一个特定的configmap或者secret，只有在我们指定的配置图或秘密被改变时才会触发滚动升级，这样，它不会触发滚动升级所有配置图或秘密在部署，后台登录或状态设置中使用。

一个制定`deployment`资源对象，在引用的`configmap`或者`secret`种，**只有`reloader.stakater.com/match: "true"`为`true`才会出发更新，为`false`或者不进行标记，该资源对象都不会监视配置的变化而重启。**

```
kind: Deployment
metadata:
  annotations:
    reloader.stakater.com/search: "true"
spec:
  template:
```

### **指定cm**

**例如：一个deploy有挂载`nginx-cm1`和`nginx-cm2`两个`configmap`，只想`nginx-cm1`更新的时候deploy才发生滚动更新，此时无需在两个cm中配置注解，只需要在deploy中写入`configmap.reloader.stakater.com/reload:nginx-cm1`，**其中`nginx-cm1`如果发生更新，`deploy`就会触发滚动更新。

如果多个cm直接用逗号隔开

```
# configmap对象
kind: Deployment
metadata:
  annotations:
    configmap.reloader.stakater.com/reload: "nginx-cm1"
spec:
  template: metadata:
```

```
# secret对象
kind: Deployment
metadata:
  annotations:
    secret.reloader.stakater.com/reload: "foo-secret"
spec:
  template: metadata:
```

> 为了降低 `kube-apiserver` 的负担，Kubernetes 从 v1.19 开始自动开启了 **`ImmutableEphemeralVolumes` 特性，开启后禁止 Secret 和 ConfigMap 的自动更新**

这些配置通常可以分为两类：一**类是诸如运行环境和外部依赖等非敏感配置，另一类是诸如密钥和 SSH 证书等敏感配置。**

**对 Kubernetes 应用来说，敏感配置推荐放到 Secret 中，而非敏感配置推荐放到 ConfigMap中** 。

Secret 相对于 ConfigMap 来说，提供了更多的数据安全保证机制从而更适合敏感数据配置：

Pod 可以通过 Volume 和环境变量两种方式引用 ConfigMap 和 Secret，并且以 Volume 形式挂载后还支持热更新。这种热更新机制看起来非常好，但在实际 DevOps 流程中实际上也有不少的问题，需要使用的时候特别注意。

### **Kubernetes热更新**

使用 Secret 和 ConfigMap 最简单的方法是以 Volume 形式挂载到 Pod 中，这种方式也支持自动更新。

**不过需要注意的是，在挂载时不要使用 `subPath`，因为使用 `subPath` 之后就不支持自动更新了。**

为了降低 kube-apiserver 的负担，Kubernetes 从 v1.19 开始自动开启了 ImmutableEphemeralVolumes 特性，开启后禁止 Secret 和 ConfigMap 的自动更新：

因为是文件的更新，容器应用可以用 inotify 机制监控文件的变化情况，进而再加载新配置。比如，对于 Go 应用来说，可以使用 fsnotify 库。

当然，**这种方法有很多限制**：

* 大量 ConfigMap 和 Secret 的 watch 请求会**加重 kube-apiserver 的负载**，影响 kube-apiserver 的性能。
* * 不支持滚动更新，只要 Secret 和 ConfigMap 更新了，**所有使用他们的容器都会全部更新**。这会导致配置错误一下子更新到所有容器，而不是滚动更新，无法实现第一个 Pod 发现错误时停止后续的更新。

```
kind: Deployment
metadata:
  annotations:
    configmap.reloader.stakater.com/reload: "foo-configmap"
     secret.reloader.stakater.com/reload: "foo-secret"
spec:
  template: metadata:
```


## 2. Subpath vs Path

Path refers to the absolute location within the container's filesystem where a volume or file will be mounted.

It defines the destination directory or file path inside the container.

```
volumeMounts:
  - name: my-volume
    mountPath: /app/data
```

Here, the contents of the volume my-volume will be mounted at /app/data in the container.

### Subpath

**Subpath is used to mount a specific file or subdirectory** from a volume into a container, **rather than mounting the entire volume**.

It allows you to selectively mount a portion of the volume into the container.

```
volumeMounts:
  - name: my-volume
    mountPath: /app/config/file.txt
    subPath: config/file.txt
```

**Here, only the file `config/file.txt` from the volume my-volume is mounted at `/app/config/file.txt` in the container.**

Key Differences

|  Feature  | Path  | Subpath |
|:------------- |:---------------:| -------------:|
| Purpose      | Defines the mount location in the container. |         Selectively mounts a specific file or subdirectory from the volume |
| Scope      | Mounts the entire volume.        |   Mounts only a part of the volume. |
| Use Case | When you want to mount the entire volume.  | When you need to mount a specific file or directory from the volume.     |

Suppose you have a volume with the following structure:

```
my-volume/
  ├── config/
  │   └── file.txt
  └── data/
      └── datafile.txt
```

Using path:

```
volumeMounts:
  - name: my-volume
    mountPath: /app
```

This will mount the entire my-volume (including `config/` and `data/`) to `/app` in the container.


Using **subpath:**

```
volumeMounts:
  - name: my-volume
    mountPath: /app/config/file.txt
    subPath: config/file.txt
```

This will mount only `config/file.txt` from my-volume to `/app/config/file.txt` in the container.

### 3. Configmap change at the same time

**Can Multiple People Change a ConfigMap?**

Yes, multiple users or processes with the appropriate permissions can modify a ConfigMap. Kubernetes does not inherently restrict the number of users who can edit a ConfigMap, as long as they have the necessary RBAC (Role-Based Access Control) permissions.

**How Changes to a ConfigMap Affect Pods**

The behavior of a Pod when its associated ConfigMap is updated depends on how the ConfigMap is used:

**1 ConfigMap Mounted as a Volume:**

* **If the ConfigMap is mounted as a volume into a Pod, changes to the ConfigMap are eventually propagated to the Pod**.

* The update delay depends on the kubelet sync interval (default is ~1 minute).

* The Pod will see the updated ConfigMap data, **but existing processes inside the container** will not automatically restart unless explicitly configured to do so (e.g., using a sidecar or custom logic).


**2.ConfigMap Used as Environment Variables**

* If the ConfigMap is used to populate environment variables in a Pod, **changes to the ConfigMap will not affect the Pod**.
* Environment variables are injected into the Pod at creation time and are not updated dynamically.

**3.ConfigMap Used in subPath Mounts**

If a ConfigMap is mounted using subPath, 

* changes to the ConfigMap will not be propagated to the Pod. 
* This is because subPath mounts are treated as static files at the time of Pod creation.


### 1 Concurrent Updates:

Kubernetes uses an optimistic concurrency control mechanism for resources like ConfigMaps.

**When multiple users try to update the same ConfigMap at the same time, Kubernetes will process the updates one at a time.**

**The first update will succeed, and subsequent updates will need to rebase their changes on the latest version of the ConfigMap.**

### 2 Conflict Resolution:

If two users try to update the same ConfigMap simultaneously, **the second user's update will fail unless they explicitly handle conflicts**.

**Kubernetes uses a resource version field to track changes**. If the resource version in the update request does not match the current version in the cluster, **the update will be rejected with a conflict error**.

### 3 Example of a Conflict:

* User A fetches ConfigMap app-config with resource version 123.
* User B fetches ConfigMap app-config with resource version 123. 
* User A updates the ConfigMap, and the resource version becomes 124.
* User B tries to update the ConfigMap but provides the old resource version (123). Kubernetes rejects the update with a conflict error.

### **4 How to Handle Concurrent Updates**

**Retry Mechanism:**

If a user's update fails due to a conflict, they can:

* Fetch the latest version of the ConfigMap.
* Reapply their changes.
* Retry the update.

**Patch Instead of Replace:**

Instead of replacing the entire ConfigMap, use a patch operation to update only specific fields. This reduces the likelihood of conflicts.

```
kubectl patch configmap app-config --patch '{"data":{"key":"new-value"}}'
```

### Impact on Pods

If multiple users successfully update the ConfigMap, the changes will propagate to the Pods depending on how the ConfigMap is used:

* **Volume Mounts: Changes will eventually propagate to the Pod (after the kubelet sync interval)**.

* Environment Variables: **Changes will not affect the Pod unless it is restarted.**

* SubPath Mounts: Changes will not affect the Pod.


Yes, multiple users can attempt to change a ConfigMap at the same time, but Kubernetes will process the updates sequentially to avoid conflicts. If conflicts occur, users need to handle them by fetching the latest version and retrying their updates. Proper coordination and conflict resolution mechanisms are essential when multiple users are working with the same ConfigMap. Let me know if you need further clarification!