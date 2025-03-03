# 从容器到调度：准备好这些 CRI 面试题

### 1. 什么是 CRI（Container Runtime Interface）？

CRI（Container Runtime Interface）是 Kubernetes 定义的一个接口，目的是为了实现容器的运行时抽象，使得 Kubernetes 能够支持多种不同的容器运行时（如 Docker、containerd、CRI-O 等）。

**CRI 是 Kubernetes 的一个关键组件，它提供了一种标准化的方式来让 Kubernetes 与不同的容器运行时交互**。

* **容器运行时（Container Runtime）**： 负责创建和管理容器的组件，它直接与操作系统交互，执行容器的生命周期管理。
* **CRI 的作用**： **通过定义一个统一的接口**，允许 Kubernetes 与容器运行时之间进行通信和交互，从而支持不同类型的容器运行时。