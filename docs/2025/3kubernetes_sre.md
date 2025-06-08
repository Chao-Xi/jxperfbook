
####  1. What is the difference between a Deployment and a StatefulSet in Kubernetes?

A Deployment is used for stateless applications where the state of the application does not need to be preserved. 

It manages replica sets and ensures that the desired number of pod replicas are running.


A StatefulSet, on the other hand, is used for stateful applications where each pod needs to be uniquely identified and must retain its state even after rescheduling.

**It assigns a stable identity (DNS name and persistent volume) to each pod**

**Key differences:**

* **Pod Identity**: StatefulSet pods have stable hostnames (e.g., web-0, web-1) while Deployment pods do not
* **Storage**: *StatefulSets are typically used with persistent volume claims (PVCs) that are retained even when pods are deleted.*
* **Scaling behavior**: StatefulSets create/delete pods in order, while Deployments do it in parallel.

**Example Use Case:**

- Deployment: Web servers, microservices.
- StatefulSet: Databases like MySQL, MongoDB, Kafka

**Deployment**

* Pods are anonymous
* Pods don't have stable storage
* Easy scaling (add/remove pods)
* Stateless applications

**StatefulSet**

- Each pod has a unique identity (pod-0, pod-1)
- Each pod has its own persistent volume
- Scaling pods is more controlled with stable identities
- Stateful applications (e.g., databases)

#### 2. Explain how Kubernetes handles rolling updates and rollbacks.

Kubernetes supports **rolling updates using the Deployment controller**. 

It updates pods in a controlled fashion to minimize downtime. 

The process gradually replaces old pods with new ones, ensuring that some pods are always running

RollingUpdate Strategy: Default update strategy in Deployments.


* **maxUnavailable**: How many pods can be unavailable during the update.
* **maxSurge**: How many extra pods can be created during the update.

**Rollback**:

If a rollout fails or a bug is introduced, Kubernetes allows you to rollback to the previous version

```
kubectl rollout undo deployment <deployment-name>

kubectl rollout status deployment my-deployment

kubectl rollout history deployment my-deployment

kubectl rollout undo deployment my-deployment
```

Benefits:

- Zero-downtime deployment.
- Version tracking of rollouts.
- Fast recovery from bad deployments.

#### 3. What are Kubernetes DaemonSets and give a real-world use case?

A DaemonSet ensures that a copy of a pod runs on every node (or a subset of nodes) in the cluster

Use Cases:

* **Running a node-level monitoring agent** (e.g., Prometheus Node Exporter).
* **Running a log collector** (e.g., Fluentd, Filebeat).
* **Running a storage driver or system daemon**.

When a new node is added to the cluster, the DaemonSet controller automatically schedules the pod on the new node.

#### 4. What are the differences between ConfigMap and Secret in Kubernetes?


Both ConfigMap and Secret are used to inject configuration into pods, but they serve different purposes

**ConfigMap**

* Store non-sensitive config data
* Plain text
* Not encrypted
* Yes

**Secret**

* Store sensitive data (passwords, tokens)
* Base64 encoded
* Can be encrypted at rest (KMS)
* Yes


#### 5. What is the role of etcd in a Kubernetes cluster?

etcd is a distributed key-value store used by Kubernetes to store all cluster data. 

**It acts as the source of truth for the Kubernetes control plane.**

**Responsibilities:**

* **Stores configuration data, cluster state, secrets, and service discovery info**.
* Supports consistent reads and writes across the cluster.


Key Features:

- **Highly available and distributed**.
- **Uses Raft consensus algorithm for leader election and consistency**.
- Requires backup and disaster recovery strategy.

Security Considerations:

* TLS encryption.
* Authentication and access control.

```
To view keys in etcd (if directly accessing):

ETCDCTL_API=3 etcdctl get "" --prefix --keys-only
```

#### 6. How does Kubernetes networking work? Explain the Pod-to-Pod communication.

**Kubernetes networking is based on a flat network model**, where all pods can communicate with each other without NAT (Network Address Translation), regardless of the node they’re on.

**Core Networking Rules:**

1. Every pod gets its own IP address.
2. All pods can reach each other directly via IP.
3. Containers within a pod share the same network namespace (localhost).
4. **Communication between pods across nodes is handled by CNI plugins (e.g., Calico,Flannel, Cilium).**

**Pod-to-Pod Communication Steps:**

- Pod A wants to reach Pod B using Pod B’s IP.
- If both pods are on the same node: traffic stays local.
- If on different nodes: the packet is routed via virtual network interfaces provided by the CNI plugin.

```
kubectl exec -it pod-a -- curl <pod-b-ip>:<port>
```

#### 7. What are Kubernetes Network Policies and how do they work?

**A NetworkPolicy in Kubernetes is a resource used to control traffic flow at the IP address or port level between pods**

Default Behavior:

**By default, all pods can communicate with all other pods. NetworkPolicies restrict this.**

Features:

* *Allow/deny ingress (incoming) or egress (outgoing) traffic.
* Based on labels, namespaces, and IP blocks.
* **Require a CNI plugin that supports NetworkPolicy (e.g., Calico, Cilium)**

**Example: Allow ingress traffic to pods with label app=db only from app=web:**

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
 name: allow-web-to-db
spec:
  podSelector:
    matchLabels:
      app: db
ingress:
- from:
  - podSelector:
     matchLabels:
       app: web
```

Note: NetworkPolicies are additive — start from "deny all" and explicitly allow traffic.


Network Policies control the communication between pods and services in a Kubernetes cluster, **based on IP addresses or labels. These policies are used to enforce security rules, isolating applications and reducing the attack surface**

Key Features:

- **Can restrict incoming and outgoing traffic to/from pods**.
- Works by defining ingress (incoming) and egress (outgoing) rules.
- Can target specific pod selectors or namespaces.

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
	name: allow-frontend
spec:
	podSelector:
		matchLabels:
			app: frontend
ingress:
- from:
	- podSelector:
		matchLabels:
			app: backend
ports:
- protocol: TCP
	port: 80
```

#### 8. Explain the Kubernetes control plane components and their responsibilities

The Kubernetes control plane is the brain of the cluster and consists of several components:

1. kube-apiserver: Entry point for all REST API requests. Validates and processes requests.
2. etcd: Stores all cluster state and configuration data.
3. kube-scheduler: Assigns pods to nodes based on resource availability and constraints.
4. kube-controller-manager: Runs all controllers (e.g., Node, Deployment, ReplicaSet controllers) to ensure the desired state.
5. cloud-controller-manager: Manages cloud provider-specific control loops (like load balancers, volumes)

Workflow Example:

- You apply a deployment YAML.
- kube-apiserver receives it.
- etcd stores the desired state.
- kube-controller-manager ensures replicas match.
- kube-scheduler assigns pods to nodes.

This architecture allows scalability, self-healing, and declarative infrastructure.


#### 9. What is the role of Kubernetes Ingress and how does it differ from a Service?

An Ingress is a Kubernetes resource that manages external access to services, typically HTTP/HTTPS traffic. 

**It acts as a Layer 7 (application layer) reverse proxy.**

**Service**

L4 (TCP/UDP) / Expose internal or external service / Yes / TLS Support Manually via Service/LoadBalance


**Ingress**

L7 (HTTP/HTTPS) / Expose multiple services under a single IP / No (path/host-based rules) / Built-in SSL termination


```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: web-ingress
spec:
 rules:
 - host: example.com
 http:
  paths:
  - path: /app1
    pathType: Prefix
    backend:
      service:
        name: app1-service
        port:
          number: 80
```

**Ingress Controllers**(e.g., NGINX, Traefik) are required to implement ingress rules.

#### 10. How do you perform debugging in Kubernetes when a pod is not starting?


**1. Describe the pod:**

```
kubectl describe pod <pod-name>
```

Check events at the bottom — look for image pull errors, scheduling issues, resource limits.

**2. Check logs:**

```
kubectl logs <pod-name>

If multiple containers:
kubectl logs <pod-name> -c <container-name>
```

**3. Check status**

```
kubectl get pods
```

Look for status like CrashLoopBackOff, ImagePullBackOff, Pending.


**4. Exec into container (if running):**

```
kubectl exec -it <pod-name> -- /bin/sh
```

**5. Use ephemeral debug containers:**

```
kubectl debug -it <pod-name> --image=busybox
```

**6.Check node status:**

```
kubectl get nodes
kubectl describe node <node-name>
```

**Common Issues:**


- Missing ConfigMap/Secret
- Insufficient resources
- Incorrect image or tag
- Network policy restrictions
- Node taints or affinity rules

#### 11. What is a Kubernetes Job and how does it differ from a Deployment?

A Job in Kubernetes is used to run one-time or batch tasks that terminate once completed successfully. It ensures that a pod runs to completion (success or failure) and can be retried if it fails.


A Deployment, by contrast, is used for long-running, stateless applications that must be kept running continuously.


**Use Cases for Job:**

- Database migrations
- Sending notification emails
- Data backups

**Types of Jobs:**

- **One-off Job**: Runs a pod to completion once.
- **Parallel Job**: Runs multiple pods in parallel.
- **Completions & Parallelism**: Controls number of successful completions and concurrent pods.


#### 12. What are Init Containers in Kubernetes and why are they used?


Init Containers are specialized containers that run **before the main application container in a pod**. They are used to perform setup tasks such as:


- Waiting for a service to become available
- Cloning a git repository
- Setting environment preconditions

Features:

- Run **sequentially** before app containers.
- Must complete **successfully** before main containers start.
- Support different images and tools than main containers

```
initContainers:
- name: wait-for-db
  image: busybox
  command: ['sh', '-c', 'until nslookup mydb; do echo waiting; sleep 2; done']
```

Benefits:

- Separation of concerns.
- Improved modularity and debugging.
- Retry logic before main workload runs.

#### 13. What are taints and tolerations in Kubernetes?

Taints are applied to nodes to prevent pods from being scheduled on them unless the pod has a matching toleration

```
kubectl taint nodes <node-name> key=value:NoSchedule
```

Taint Effects:

- **NoSchedule**: Pod will not be scheduled unless it tolerates the taint.
- **PreferNoSchedule**: Avoid scheduling if possible.
- **NoExecute**: Pod is evicted if already running.

Use Cases:

- **Reserve nodes for specific workloads (e.g., GPU jobs).**
- **Ensure critical workloads get priority on dedicated nodes.**

#### 15. What are sidecar containers and how are they used in Kubernetes?

**A sidecar container is a helper container that runs in the same pod as the main application and provides supporting functionality.**

Common Use Cases:

- Logging agents (e.g., Filebeat)
- Proxies (e.g., Envoy in Istio)
- Data synchronization tools
- Service mesh communication

```
containers:
- name: app
  image: myapp
- name: sidecar
  image: busybox
command: ["tail", "-f", "/dev/null"]
```

- • Shares the same network namespace and volumes.
- • Can start and stop independently of the main container.
- • Promotes modularity and separation of concerns.

#### 16. What are Kubernetes Volumes and how do they differ from Docker volumes?

In Kubernetes, a Volume is a directory accessible to containers in a pod that is preserved across container restarts.

Differences from Docker Volumes:

- **Kubernetes volumes are tied to the pod's lifecycle, not the container.**
- Kubernetes supports different volume types (e.g., hostPath, emptyDir, configMap, persistentVolumeClaim).

Common Volume Types:

- emptyDir: Temporary storage shared between containers.
- hostPath: Maps a file/directory from the host node.
- persistentVolumeClaim: For persistent storage (e.g., AWS EBS, NFS).

```
volumes:
- name: data
  emptyDir: {}
```

- Share data between containers.
- Preserve logs or cache.
- Mount external storage (persistent).

#### 17. Explain Kubernetes PersistentVolumes (PV) and PersistentVolumeClaims (PVC).

1. Admin creates a PV or defines a StorageClass.
2. User creates a PVC.
3. Kubernetes binds a matching PV to the PVC.
4. Pod mounts the PVC.

Access Modes:

- • ReadWriteOnce — one node read/write.
- • ReadOnlyMany — multiple nodes read-only.
- • ReadWriteMany — multiple nodes read/write.

#### 18. What is a Kubernetes ServiceAccount and when do you use it?

A ServiceAccount provides an identity for pods to access the Kubernetes API or external


Default Behavior:

- • Every pod is associated with a default service account in its namespace.
- • Tokens are automatically mounted into pods via /var/run/secrets/....


Use Cases:

- • Granting fine-grained API permissions via RBAC.
- • Interacting with cloud provider APIs (e.g., via Workload Identity).

```
apiVersion: v1
kind: ServiceAccount
metadata:
name: read-only


Mounting in Pod:

spec:
serviceAccountName: read-only


RBAC Binding Example:

kind: RoleBinding
roleRef:
 kind: Role
 name: pod-reader
 apiGroup: rbac.authorization.k8s.io
subjects:
- kind: ServiceAccount
  name: read-only
```

#### 20. What is a Kubernetes Horizontal Pod Autoscaler (HPA) and how does it work?

The Horizontal Pod Autoscaler (HPA) automatically scales the number of pods in a deployment, replication controller, or statefulset based on CPU/memory usage or custom metrics.

How it Works:

- • Monitors pod metrics via the Metrics Server.
- • If usage exceeds target thresholds, it increases replicas.
- • If usage drops, it scales down.

```
kubectl autoscale deployment myapp --cpu-percent=50 --min=2 --max=10
```

```
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
spec:
 minReplicas: 2
 maxReplicas: 10
 metrics:
 - type: Resource
   resource:
    name: cpu
    target:
     type: Utilization
     averageUtilization: 50
```

Dependencies:

- • Metrics Server must be installed.
- • Works with custom metrics (Prometheus Adapter) in v2.

#### 24. What are Kubernetes Admission Controllers?


Admission Controllers are plugins that intercept requests to the Kubernetes API after authentication and authorization but before persistence.

Two Types:

- • Validating Admission Controllers: Validate the request (e.g., resource quota).
- • Mutating Admission Controllers: Modify the request (e.g., inject sidecar).


Examples:

- • NamespaceLifecycle: Prevents deletion of system namespaces.
- • LimitRanger: Enforces resource limits.

- • MutatingAdmissionWebhook: Used by Istio, Linkerd to inject proxies.
- • ValidatingAdmissionWebhook: Used for custom validations.

Use Case:

**Injecting a monitoring agent in all pods using a Mutating Admission Webhook.**

#### 30. What is Kubernetes Pod Disruption Budget (PDB)?

A **PodDisruptionBudget** ensures that a minimum number or percentage of pods in a deployment/statefulset remain available **during voluntary** disruptions, such as:

- Node drain
- Rolling updates
- Maintenance tasks

PDB Example:


```
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: myapp-pdb
spec:
minAvailable: 2
selector:
 matchLabels:
 app: myapp
```

**Key Parameters:**

- minAvailable: Minimum pods that must be running.
- maxUnavailable: Maximum pods that can be down.

**PDB does not apply to involuntary disruptions (e.g., hardware failure).**


### 33. What is a Kubernetes ReplicaSet and how does it differ from a Deployment?

A ReplicaSet ensures that a specified number of pod replicas are running at all times. It is primarily used to maintain pod availability and redundancy.

Differences with Deployment:

- **A Deployment is a higher-level controller that manages ReplicaSets and provides rolling updates, rollbacks, and more.**
- A ReplicaSet is responsible for maintaining the desired pod count but does not manage deployments, upgrades, or rollbacks.


#### 34. How does Kubernetes handle pod scheduling?

Kubernetes uses the **Scheduler** to assign pods to nodes in the cluster based on resource availability, constraints, and other scheduling policies

Scheduling Steps:

1. **Predicate Phase**: Filters nodes that don't meet pod requirements (e.g., resource requests, taints).
2. **Priority Phase**: Prioritizes nodes based on constraints like affinity and anti-affinity rules, resource usage, etc
3. **Binding Phase**: Assigns the pod to the selected node.

Types of Scheduling Constraints:

- • **Node Affinity**: Scheduling based on node labels.
- • **Taints and Tolerations**: Restricting pod scheduling on specific nodes.
- • **Pod Affinity/Anti-Affinity**: Scheduling based on other pods in the cluster.

```
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
     nodeSelectorTerms:
     - matchExpressions:
      - key: "kubernetes.io/hostname"
        operator: In
        values:
        - node1
```

#### 35. What is the Kubernetes API Server and its role?


The **Kubernetes API Server** is the central control plane component that exposes the 

Kubernetes API and handles all requests to the cluster.

Role:

- • Receives and validates API requests.
- • **Acts as the interface between clients (kubectl, controllers, etc.) and the cluster.**
- • Stores resource state in etcd.
- • **Communicates with other components (e.g., Scheduler, Controller Manager)**.

#### 36. What is Kubernetes Ingress and how does it differ from a Service?

**Ingress is a set of rules that allow inbound connections to reach the services in a Kubernetes cluster, often managing HTTP/HTTPS traffic.**

Differences from Service:

**Service** provides a stable endpoint for accessing a set of pods, generally used for internal communication.

**Ingress** manages external access, typically for HTTP/S traffic, by defining routing rules based on URL paths, hostnames, etc.

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
spec:
  rules:
  - host: www.example.com
    http:
     paths:
     - path: /foo
       pathType: Prefix
       backend:
        service:
          name: foo-service
        port:
         number: 80
```

Benefits:

- URL path-based routing.
- SSL termination.
- Load balancing and reverse proxy features.

#### 37. How does Kubernetes handle cluster upgrades?

Kubernetes handles cluster upgrades in a rolling fashion to minimize downtime. The upgrade process includes:

**1. Control Plane Upgrades:**
   
Upgrade kube-apiserver, kube-controller-manager, kube-scheduler first. Ensure backward compatibility with existing nodes.

**3. Node Upgrades:**

- Upgrade nodes one at a time to avoid disrupting the entire cluster.
- Use kubectl drain to evict pods and kubectl uncordon to bring nodes back online.

Best Practices:

* Test the upgrade in a non-production environment.
* Upgrade kubelet and kubectl to the matching version after upgrading the control plane.

#### 38. What is Kubernetes Horizontal Pod Autoscaler (HPA) and how is it configured?

The Horizontal Pod Autoscaler (HPA) automatically adjusts the number of replicas of a pod based on observed metrics (e.g., CPU, memory, custom metrics).

**How it works:**

HPA monitors the metrics (usually from the Metrics Server) and adjusts pod replicas to meet the target utilization.

**Configuration Example:**

```
kubectl autoscale deployment myapp --cpu-percent=50 --min=1 --max=10
```

**Custom Metrics:**

To scale based on custom metrics, you need to use an adapter like the Prometheus Adapter.

```
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
	name: myapp-hpa
spec:
	minReplicas: 2
	maxReplicas: 10
metrics:
 - type: Resource
   resource:
	name: cpu
	target:
		type: Utilization
		averageUtilization: 50
```

#### 39. What is Kubernetes ServiceAccount and how is it used for API access?

A ServiceAccount provides an identity for pods to interact with the Kubernetes API.

Key Features:

- Service accounts are used for **API authentication.**
- They allow fine-grained RBAC policies to **control access**.
- A default service account is automatically created in each namespace.

#### 42. How does Kubernetes handle high availability (HA) for control plane components?

To achieve high availability for the Kubernetes control plane:

1. Multiple API Servers: Deploy multiple kube-apiserver instances behind a load balancer.
2. Etcd Cluster: Set up a distributed etcd cluster with an odd number of nodes (3, 5, etc.) to ensure quorum.
3. Control Plane Pods: Deploy multiple replicas of components like kube-scheduler and kube-controller-manager.

#### 44. What is the purpose of kubelet in Kubernetes?

The kubelet is an agent that runs on each node in the cluster and ensures the containers in the pods are running as expected.

Responsibilities:

- • Watches the API server for pod specifications and ensures the desired state is met on the node.
- • Monitors containers and restarts them if necessary (e.g., if they crash).
- • Reports node health to the API server.

#### 49. What are Kubernetes Endpoints and how are they related to Services?

Endpoints are objects that represent the IP addresses of the pods that back a Kubernetes Service.

When a Service is created, Kubernetes automatically creates Endpoints objects that match the pods selected by the Service's label selector.

How They Work:

- • **The Service proxy forwards requests to the endpoints associated with the service.**
- • When a pod is added or removed, the list of endpoints is updated.

#### 50. What is the purpose of kubectl drain and kubectl cordon?


These commands are used for node maintenance.

* **kubectl cordon**: Marks a node as unschedulable, preventing new pods from being scheduled on it.
* **kubectl drain:** Evicts all pods from the node, marking the node as unschedulable and preparing it for maintenance.

```
kubectl cordon node1
kubectl drain node1 --ignore-daemonsets
```

- Draining a node before maintenance (e.g., upgrading hardware or OS).
- Temporarily isolating a node from the cluster.