# 7 从容器到调度：准备好这些 CRI 面试题

### 1. 什么是 CRI（Container Runtime Interface）？

CRI（Container Runtime Interface）是 Kubernetes 定义的一个接口，目的是为了实现容器的运行时抽象，使得 Kubernetes 能够支持多种不同的容器运行时（如 Docker、containerd、CRI-O 等）。

**CRI 是 Kubernetes 的一个关键组件，它提供了一种标准化的方式来让 Kubernetes 与不同的容器运行时交互**。

* **容器运行时（Container Runtime）**： 负责创建和管理容器的组件，它直接与操作系统交互，执行容器的生命周期管理。
* **CRI 的作用**： **通过定义一个统一的接口**，允许 Kubernetes 与容器运行时之间进行通信和交互，从而支持不同类型的容器运行时。

### 2. CRI 的常见实现有哪些？

**常见的CRI实现包括：**

Docker： 虽然 Docker 在 Kubernetes 中被逐渐替代，但它曾是最常用的容器运行时之一。Docker 支持通过 dockershim 实现与 Kubernetes 的 CRI 兼容。

containerd： 这是一个由 Docker 提供的容器运行时，后成为独立项目，并成为 Kubernetes 推荐的容器运行时之一。它提供了基础的容器生命周期管理功能，如容器的创建、启动、停止等

CRI-O： 这是一个专门为 Kubernetes 设计的容器运行时，遵循 CRI 规范。CRI-O 通过提供与 Kubernetes 的直接集成，简化了容器运行时的功能，去除了 Docker 中的许多非必需功能。

Frakti： 这是一个为 Kubernetes 提供容器和虚拟机支持的容器运行时，通过 KVM 提供虚拟化支持。

### 3. CRI 与 Docker 之间的关系是什么？

在 Kubernetes 中，Docker 以前通过 dockershim 提供了对 CRI 的支持，但随着 Kubernetes 1.20 版本的发布，Docker 支持逐渐被弃用。

原因是 Docker 不完全符合 Kubernetes 对容器运行时的要求，尤其是在性能和资源管理方面的灵活性。**Kubernetes 推荐使用 containerd 或 CRI-O 来作为容器运行时**。

dockershim： Kubernetes 使用 dockershim 将 Docker 与 Kubernetes 连接在一起，使得 Kubernetes 能够通过 CRI 与 Docker 进行通信。

然而，dockershim 在 Kubernetes 1.24 中被完全移除，因此 Kubernetes 现在不再直接支持 Docker 作为容器运行时。

**containerd 和 CRI-O： 这两个容器运行时符合 CRI 规范，因此可以直接与 Kubernetes 进行集成并作为容器运行时使用**。

### 4. CRI 的工作流程是怎样的？

1. **Kubernetes 请求容器运行时**： Kubernetes 向 CRI 发出请求，通过 CRI 调用容器运行时的接口（如创建、启动、停止容器等）。
2. **CRI 调用容器运行时 API**： CRI 通过容器运行时的 API 与容器运行时（**如 containerd 或 CRI-O）进行交互，管理容器的生命周期**。
3. **容器运行时操作系统**： 容器运行时与操作系统的内核进行交互，直接管理容器的进程和资源，启动和停止容器。
4. **返回结果给 Kubernetes**： 容器运行时完成任务后，返回状态或执行结果给 Kubernetes

这个流程确保了 Kubernetes 可以在不同的容器运行时之间进行无缝切换。

### 5. Kubernetes 通过 CRI 启动容器时，哪些信息是必需的？

当 Kubernetes 通过 CRI 启动容器时，必须提供以下信息：

*  **容器镜像**： 容器运行时需要知道要拉取并使用哪个镜像。
* **容器的资源需求**： 包括 CPU、内存等资源的限制。
* **网络配置**： 如容器的 IP 地址、端口映射等。
* **存储卷**： 容器可能需要挂载一些持久化存储卷。
* **命令和参数**： 容器启动时需要执行的命令和参数。
* **容器运行时配置**： 如环境变量、日志、PID 控制等配置项。

### 6. CRI 的接口包括哪些主要的功能？

CRI 提供了多个功能接口，主要包括

**容器创建和启动：**

* CreateContainer：创建一个容器。
* StartContainer：启动一个已经创建的容器。

**容器管理：**

* StopContainer:  停止一个容器。
* RemoveContainer: 删除一个容器。


**容器状态查询：**

* ContainerStatus： 获取容器的当前状态
* ListContainers：列出当前所有运行的容器。

**容器日志：**

ContainerLogs：获取容器的日志。

**容器的健康检查**：

*  UpdateContainerResources: 更新容器的资源配置。
*  ContainerStats：获取容器的资源使用情况，如CPU、内存、磁盘等。

### 7. Kubernetes 支持哪些容器运行时？

Kubernetes 支持多种容器运行时，主要包括：

* Docker（通过 dockershim，已逐步弃用）
* containerd
* CRI-O
* Frakti（支持虚拟机容器）
* gVisor（提供额外的安全性，类似沙箱）
* Kata Containers（通过虚拟化实现容器）

从 Kubernetes 1.24 版本开始，Docker 支持逐渐被移除，Kubernetes 推荐使用 containerd 或 CRI-O 作为容器运行时。

### 8. 为什么 Kubernetes 不再推荐使用 Docker 作为容器运行时？

Kubernetes 不再推荐使用 Docker 作为容器运行时，主要原因有：

* **性能问题**： Docker 包含了很多不必要的功能，Kubernetes 只需要容器运行时的基础功能，如容器的创建、运行和停止，**而 Docker 提供了很多额外的功能（如构建镜像、网络配置等），这增加了 Kubernetes 的复杂性**。
*  **复杂性**： Docker 本身包含很多与 Kubernetes 无关的组件，如镜像构建、Docker CLI 等，Kubernetes 只需要与容器运行时交互。containerd 和 CRI-O 是更加轻量和专注的容器运行时，它们去除了与容器编排无关的部分，更适合 Kubernetes 的需求。
*  **维护问题**： Docker 是一个完整的容器平台，包含了从构建到运行的完整生命周期，而 containerd 和 CRI-O 仅专注于容器的运行时部分，避免了不必要的复杂性和依赖

### 9. CRI 是如何与 Kubernetes 中的 kubelet 交互的？

kubelet 是 Kubernetes 的一个组件，负责与容器运行时交互，确保容器的生命周期管理。kubelet 通过 CRI 与容器运行时进行通信，以下是交互过程：

1. **创建容器**： kubelet 向 CRI 发起请求，要求创建容器。
2. **启动容器**： kubelet 通过 CRI 启动容器并进行管理。
3. **监控容器**： kubelet 通过 CRI 获取容器的状态、资源使用情况等信息。
4. **容器健康检查**： kubelet 可以定期向容器运行时请求容器的健康检查信息，并在容器失败时采取恢复操作。

kubelet 作为 Kubernetes 节点的核心组件，利用 CRI 来实现容器的高效管理与调度。

### 10. CRI 是如何与 Kubernetes 中的 Pod 和容器生命周期管理交互的？

CRI（Container Runtime Interface）与 Kubernetes 中的 Pod 和容器生命周期管理紧密结合。Kubernetes 的 kubelet 通过 CRI 与容器运行时进行交互来管理容器的生命周期。主要交互步骤如下：

* **Pod 创建**： kubelet 通过 CRI 请求容器运行时创建容器。容器运行时会根据 Pod 的配置文件（如容器镜像、资源限制等）启动容器。
* **容器启动**： 一旦 Pod 被调度到节点，kubelet 会调用 CRI 中的 **CreateContainer 和 StartContainer** 接口来启动容器。
* **资源管理**： kubelet 会通过 CRI 获取容器的资源使用情况（如 CPU、内存等），并根据 Pod 的资源请求进行调度和限制。
* **容器停止和删除： kubelet 会通过 CRI 调用容器运行时的 StopContainer 和 RemoveContainer 接口**，停止并删除容器。
* **健康检查**： kubelet 使用 CRI 接口中的 **ContainerStatus 和 ContainerLogs 来获取容器的健康状态**，并进行故障恢复操作。

### 11. CRI-O 与 containerd 之间有什么区别，为什么 Kubernetes 推荐使用它们？

CRI-O 和 containerd 都是符合 CRI 规范的容器运行时，常用于 Kubernetes 集群中。它们的主要区别和 Kubernetes 推荐使用它们的原因如下

#### CRI-O

* 专为 Kubernetes 设计，遵循 CRI 标准。
* 提供一个轻量级的容器运行时，去除了 Docker 中的许多不必要的功能（如构建镜像、网络设置等）。
* 重点是与 Kubernetes 集成，保证与 Kubernetes 的兼容性。

#### containerd

* 由 Docker 提供并最初是 Docker 的一部分，后来成为独立的项目。
* 支持更多的功能，如镜像管理、容器管理、任务调度等，支持广泛的容器工作负载。
* 对 Kubernetes 也有良好的支持，并且在 Kubernetes 1.20 版本中，containerd 被推荐作为默认的容器运行时。

#### 常见的CRI实现包括：

* **轻量级**： 这两个容器运行时都专注于容器的创建、管理和销毁，不像 Docker 那样有过多的额外功能，因此适合 Kubernetes 这种高度集成的系统。
* **高效性**： 它们比 Docker 更轻量，减少了 Kubernetes 对资源的消耗，并且对容器的启动和管理更加高效。
* **稳定性与支持**： **containerd 和 CRI-O** 都有活跃的社区支持，并且在 Kubernetes 中得到了广泛的使用和验证。

### 12. 如何配置 Kubernetes 使用 CRI-O 作为容器运行时？

要将 Kubernetes 配置为使用 CRI-O 作为容器运行时，通常需要进行以下步骤：

1. 安装 CRI-O：安装 CRI-O 并确保它在 Kubernetes 节点上运行。可以使用发行版的包管理工具（如 apt、yum）或直接从 GitHub 上安装预编译的二进制文件。
2. 安装 CRI-O 之后，启动并确保它运行正常

```
systemctl start crio
systemctl enable crio
```

3.配置 Kubernetes 使用 CRI-O： **Kubernetes 的 kubelet 通过 `--container-runtime` 参数指定容器运行时。如果要使用 CRI-O，可以在 kubelet 配置中设置为 cri-o：**
4.编辑 kubelet 的启动配置文件（如 `/etc/default/kubelet`）：

```
KUBELET_EXTRA_ARGS="--container-runtime=remote --container-runtime-endpoint=/var/run/crio/crio.sock"
```

5.如果你使用的是 kubeadm 来安装 Kubernetes，可以通过 kubeadm 的配置文件进行配置：

```
apiServer:
  extraArgs:
    container-runtime: remote
    container-runtime-endpoint: /var/run/crio/crio.sock
```


6.重启 kubelet 服务： 配置完成后，重启 kubelet 服务，使其使用 CRI-O 作为容器运行时：

```
systemctl restart kubelet
```

7.验证配置： 使用以下命令验证容器运行时配置是否正确：

```
kubectl get nodes -o wide
```

8.如果配置成功，kubelet 会使用 CRI-O 来管理容器。

### 13. CRI 在容器调度中的作用是什么？

CRI 在 Kubernetes 中的容器调度过程中起到了以下作用：

* *  **容器生命周期管理**： CRI 负责容器的生命周期管理，包括容器的创建、启动、停止和删除等。当 Kubernetes 调度一个 Pod 到某个节点时，kubelet 会通过 CRI 启动容器，并监控其健康状态。
* **容器状态监控**： 通过 CRI，kubelet 能够获取容器的状态和资源使用情况（如 CPU、内存等），并将这些信息反馈给 Kubernetes，以便进行调度决策。
* **资源分配与限制**： CRI 协调容器资源（如内存、CPU、存储等）的分配和限制。kubelet 会通过 CRI 来确保容器不会超出节点的资源配额。
* **容器健康检查**： CRI 提供了容器健康检查接口，kubelet 可以定期检查容器的健康状态，并在容器失败时进行重启或迁移。

### 14. 什么是 Kubernetes 中的 kubelet 与 CRI 的交互方式？

在 Kubernetes 中，kubelet 是管理容器生命周期的关键组件，它与 CRI 容器运行时接口直接交互，确保容器的启动、管理和停止等操作。kubelet 通过以下方式与 CRI 进行交互：


1. **容器创建和启动**： kubelet 会向 CRI 请求创建和启动容器。它将 Pod 的规范（如容器镜像、资源要求等）传递给 CRI，CRI 根据这些信息启动容器。
2. **容器监控**： kubelet 会定期通过 CRI 获取容器的状态、资源使用情况和健康状况。基于这些数据，kubelet 会判断容器是否需要重启、停止或删除。
3. **容器日志： kubelet 可以通过 CRI 获取容器的日志信息，并将日志记录在 Kubernetes 的日志系统中，供用户调试和监控**。
4. **健康检查： kubelet 使用 CRI 的健康检查功能来检测容器的健康状态，并根据结果决定是否需要重新调度容器**。
5. **容器停止与删除**： kubelet 会在容器运行完成后，通过 CRI 请求停止并删除容器，释放资源。


### 15. 如何在 Kubernetes 中实现容器的资源限制和 QoS（Quality of Service）？

在 Kubernetes 中，容器的资源限制是通过 Pod 的资源请求和限制来实现的。资源请求和限制会影响容器的 QoS 级别，具体步骤如下：

1. 资源请求与限制： Kubernetes 允许用户为每个容器指定 **请求（requests） 和 限制（limits）**：

 * **requests**： 容器启动时，**Kubernetes 为容器分配的最小资源量**。如果容器使用的资源低于请求，Kubernetes 会为其保留资源
 * **limits** : **容器使用的最大资源量，超过该限制，容器会被 OOM（Out of Memory）杀死或被暂停**。

 2. QoS（质量服务）： 根据容器的请求和限制，Kubernetes 会为容器分配不同的 QoS 级别：

* ***Guaranteed*** ： 当请求和限制相等时，容器会被分配 Guaranteed QoS，意味着容器的资源始终受到保证。
* **Burstable**当请求小于限制时，容器属于 Burstable QoS，意味着它可以在需要时获得更多资源，但如果资源不足时，Kubernetes 会优先保证其他容器的资源。
* **BestEffort**： 当容器没有设置请求和限制时，**它属于 BestEffort QoS，容器的资源不受保证，可能会被优先驱逐**。

通过 CRI，kubelet 根据这些配置来管理容器的资源分配，确保容器的资源使用在合理范围内。

### 16. 如何实现 CRI 与 Kubernetes 中的调度器（Scheduler）的高效集成？

为了确保 CRI 与 Kubernetes 调度器（Scheduler）高效集成，涉及多个方面的配置和优化：

**资源请求与调度优化**

* Kubernetes 调度器基于容器的资源请求（如 CPU、内存）来进行调度。CRI 需要向 kubelet 提供容器的资源请求信息，调度器根据这些信息将容器调度到适合的节点上。
* 为了实现高效调度，容器运行时（如 CRI-O 或 containerd）需要与 kubelet 紧密协作，确保容器的资源限制与请求正确地传递给调度器。

**Pod Affinity/Anti-Affinity**

**在调度 Pod 时，使用 Pod Affinity 和 Anti-Affinity 配置来确保相关容器运行在相同节点或不同节点上**。容器运行时通过支持节点亲和性和拓扑配置，帮助调度器做出高效的决策

**调度策略与 QoS（Quality of Service）**

*  Kubernetes 中的 QoS 级别（如 Guaranteed、Burstable、BestEffort）直接影响容器的优先级。CRI 需要根据容器的 QoS 配置来优化资源的分配和管理。
* 对于关键业务服务，容器运行时应支持资源优先级的配置，确保这些容器能够优先获得资源

**动态资源调整与 Pod 重新调度**

* Kubernetes 支持基于资源使用的动态调度，容器运行时通过 CRI 接口提供容器的实时资源使用数据，帮助调度器根据集群的负载动态调整容器调度。
*  例如，**containerd 和 CRI-O 可以提供容器的实时资源使用情况，通过 CRI 接口将其反馈给 kubelet 和调度器，进而执行容器的迁移或重调度**。

### 17. 如何在 CRI 中实现容器运行时的高可用性（HA）？

* 容器运行时的多节点部署
* 容器运行时的状态同步
	* 容器运行时应支持跨节点的状态同步。例如，containerd 提供了对分布式存储的支持，确保多个节点上容器的状态信息一致，以便在节点发生故障时能够恢复容器的运行状态。
	* 配置共享存储卷（如 NFS、Ceph），确保容器的持久化数据在节点故障时能够恢复
* **故障恢复与自动重启**
	*  CRI 运行时可以通过支持容器的健康检查和自动重启机制来提高容器的可用性。kubelet 会监控容器状态，通过 CRI 接口获取容器健康状况，自动重启失败的容器。
	*  配置容器运行时的故障检测机制，当容器出现故障或不可用时，及时重启容器并将其恢复到预期状态
* **动态容器调度与节点扩展**
	* 配置 Kubernetes 的自动扩容功能，结合容器运行时的高可用性设置，当集群资源紧张时，自动扩展节点并将负载均衡到新节点上。
	* 在容器运行时支持容器的动态迁移和调度，确保容器在集群中故障恢复时能够快速迁移到其他节点。

### 18. 如何优化 CRI 中容器运行时的 I/O 性能？

优化容器运行时的 I/O 性能可以从以下几个方面着手：

**文件系统选择**

* **选择高性能的存储驱动，如 overlay2**，该驱动提供了较高的文件系统性能，特别是在容器镜像和容器数据的 I/O 操作时。
* **使用支持高性能 I/O 操作的存储插件，如 device-mapper 和 btrfs。**

**存储卷优化**

* 对于需要高性能存储的容器，使用本地存储卷（如 local 存储插件）可以减少 I/O 延迟，提供更高的吞吐量。
*  配置适当的卷类型，例如使用 NFS 协议的远程存储系统来支持共享存储需求。

**容器磁盘配额与分配**

* 配置磁盘 I/O 限制（如 `--blkio-weight`）来确保容器不会因 I/O 操作过多而影响其他容器的性能。
* **使用 磁盘配额 来分配每个容器的磁盘空间**，避免磁盘资源过度占用。
* 优化日志处理

• 对容器日志进行优化，避免过多的日志写入影响容器性能。使用外部日志处理系统（如 Fluentd、ELK）来集中管理容器日志。


**容器磁盘配额与分配**

* **配置磁盘 I/O 限制（如 --blkio-weight）**来确保容器不会因 I/O 操作过多而影响其他容器的性能。
* 使用** 磁盘配额** 来分配每个容器的磁盘空间，避免磁盘资源过度占用


**容器磁盘配额与分配**

* 配置磁盘 I/O 限制（如 `--blkio-weight`）来确保容器不会因 I/O 操作过多而影响其他容器的性能。
* **使用 磁盘配额 来分配每个容器的磁盘空间，避免磁盘资源过度占用**

**优化日志处理**

对容器日志进行优化，避免过多的日志写入影响容器性能。使用外部日志处理系统（如 Fluentd、ELK）来集中管理容器日志。

### 19 CRI 的配置文件通常位于哪里？如何修改其参数？

* 路径： `/etc/containerd/config.toml（containerd）`或 `/etc/crio/crio.conf（CRI-O）`。
* 修改后重启服务：

```
systemctl restart containerd  # 或 crio、
```

### 20. Kubernetes 1.24 移除了 dockershim，如何确保旧集群兼容 Docker？

方案：

使用 Mirantis Container Runtime（替代 dockershim）。
 迁移到 containerd，并确保 Docker 容器镜像兼容。
 
### 21 如何通过 CRI 查看容器的底层日志？

**containerd**

```
ctr --namespace=k8s.io containers ls  # 列出容器
ctr --namespace=k8s.io logs <容器ID>
```

**cri-o**

```
crictl logs <容器ID>
```

### 22 如何限制容器运行时的资源使用（如 CPU、内存）？

*  Kubernetes 层面： 通过 Pod 的 resources 字段限制。
* CRI 层面： 配置容器运行时的 Cgroups 参数（如 containerd 的 config.toml）。

### 23. 如何配置 containerd 使用私有镜像仓库？

修改 config.toml：

```
[plugins."io.containerd.grpc.v1.cri".registry]
  [plugins."io.containerd.grpc.v1.cri".registry.mirrors]
    [plugins."io.containerd.grpc.v1.cri".registry.mirrors."my-registry.com"]
      endpoint = ["https://my-registry.com"]
  [plugins."io.containerd.grpc.v1.cri".registry.configs]
    [plugins."io.containerd.grpc.v1.cri".registry.configs."my-registry.com".tls]
      insecure_skip_verify = true  # 跳过 TLS 验证（可选）
```

### 24. CRI 与 OCI（Open Container Initiative）的关系是什么？

* CRI： 定义 Kubernetes 与容器运行时的交互接口。
*  OCI： 定义容器镜像和运行时的标准（如镜像格式、运行时规范 runc）。
* 关系： CRI 兼容的运行时（如 containerd）通过 OCI 标准管理容器生命周期。

### 25. 如何优化 CRI 的性能以支持高密度容器部署？

* 启用容器运行时缓存（如 containerd 的快照机制）。
* 调整并发拉取镜像的线程数（`max_concurrent_downloads`）。
* **使用高性能存储驱动（如 overlay2）**