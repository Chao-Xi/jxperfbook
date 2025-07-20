
## Part 1

### 1. StatefulSet Pods Restart with Different Hostnames

Issue: StatefulSet guarantees stable network identities. 

If pods restart with different hostnames, **it indicates the StatefulSet configuration might have been deleted and recreated, losing persistent identity**.

Root Cause

- PVCs or StatefulSet might have been deleted without volumeClaimTemplates.
- Misconfigured storage class or persistent volume reclaim policy.

Fix:

- **Ensure StatefulSet uses volumeClaimTemplates**.
- Avoid deleting the StatefulSet; **use kubectl apply or kubectl rollout.**
- **Validate persistentVolumeReclaimPolicy is Retain to keep PVCs**.

### 2. 504 Gateway Timeout in Microservices

**Root Cause:**

* Readiness probes failing.
* Inter-service networking issues.
* Ingress timeout or service selector misconfiguration.


**Resolution:**

* Check readiness probes: `kubectl describe pod <pod>`.
* Inspect Ingress controller logs.
* Increase timeout values in NGINX/HAProxy config.
* Use kubectl port-forward to debug services locally.

### 3. Zero Downtime Application Upgrade Strategy

Recommended Strategy:

* Use **rolling updates** with appropriate **maxUnavailable=0**.
* Configure **readiness probes** to delay traffic routing until the new pod is ready.
* Use **PodDisruptionBudgets** to control pod availability.
* For **mission-critical service**s, consider B**lue/Green or Canary deployments** using **Argo Rollouts or Flagger**.

### 4. Namespace Overusing CPU

Detection:

* Monitor CPU usage: **`kubectl top pods -n <namespace>`**.
* Use Prometheus/Grafana dashboards.

Prevention:

- Set ResourceQuota for namespaces.
- Apply LimitRange to define default CPU/memory limits per pod.

```
apiVersion: v1
kind: ResourceQuota
metadata:
  name: cpu-quota
  namespace: <namespace>
spec:
  hard:
   requests.cpu: "2"
   limits.cpu: "4"
```

### 5. Deployment Rollout Stuck: ProgressDeadlineExceeded

Root Cause:

- **New pods fail readiness probes**.
- **ImagePull errors or misconfigurations**.

Steps to Troubleshoot:

- `kubectl rollout status deployment <name>`
- `kubectl describe pod <pod>` – check for probe errors or crash loops.
- Check events: `kubectl get events`

Fix

- Correct the readiness/liveness probes.
- Reapply deployment or rollback: `kubectl rollout undo`

### 6. Intermittent CoreDNS DNS Resolution Issues

Troubleshooting Steps:

* Check CoreDNS logs: `kubectl logs -n kube-system -l k8s-app=kube-dns`
* Use `kubectl exec -it <pod> -- nslookup <svc-name>`
* Validate if nodes have DNS issues (`/etc/resolv.conf`).

Fix:

- Increase CoreDNS memory/CPU.
- Add more CoreDNS replicas.
- Upgrade CoreDNS if bug-related.


### 7. Application Killed with OOMKilled

Troubleshooting:


- Check pod description: `kubectl describe pod <pod>`
- Inspect container logs for memory usage pattern.


Solution:

- Set appropriate resource requests/limits.
- Use a memory profiler (Valgrind, pprof) inside containers.
- Use HPA to scale pods if high usage is expected.


### 8. Blue/Green or Canary Deployment with Rollback

- Use separate deployments for Blue and Green.
- Switch service selector after verification.

For Canary:

- Use Argo Rollouts or Flagger with progressive traffic shifting.


Rollback:

Use `kubectl rollout undo deployment <name>` or switch service back to stable deployment.


### 9. Secure Secrets Management

**Best Practices:**


- **Mount Secrets as volumes instead of env vars.**
- Use tools like HashiCorp Vault or Sealed Secrets for encryption at rest and access control.
- Avoid logging secrets by configuring applications and log scrapers carefully

### 10. Pods Being Frequently Rescheduled

**Diagnosis:**

- `kubectl describe node <node>` – check for disk pressure, memory pressure.
- **Check taints, node auto-scaling event**s.

Fix:

- Increase node resources.
- Implement **podAntiAffinity or nodeSelector to control pod placement**.
- Set **priorityClassName** to prevent eviction of critical pods.

### 11. Helm Chart Upgrade Breaks Production

Diagnosis:

Run `helm history <release>` to inspect changes.
Use `helm diff` upgrade plugin for visibility.

Rollback:

`Run helm rollback <release> <revision>` to revert to the previous working version.


### 12. HPA Not Scaling with High CPU Usage

**Checkpoints:**

Metrics Server installed and running.
Run `kubectl get --raw /apis/metrics.k8s.io/v1beta1/nodes`.

Fix:

- Deploy Metrics Server if missing.
- Define proper resource requests and limits.
- Use this HPA config:

```
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
spec:
metrics:
- type: Resource
 resource:
  name: cpu
  target:
   averageUtilization: 70
   type: Utilization
```

### 13. Node Joins but Schedules No Pods

Possible Issues:

- Node is tainted (NoSchedule).
- Node status is NotReady.
- kubelet or networking misconfigured.


Fix:

* Check taints: `kubectl describe node <node>`
* Validate kubelet logs and network plugin.

### 14. Network Policy Blocking Traffic


Debug Steps:

- Use `kubectl get networkpolicies -n <ns>`
- Check if egress/ingress rules block unintended pods.
- Use netshoot or busybox pods to test connectivity.

### 15. Legacy App Requiring Persistent IP

Solution:

* Use a Service with type=ClusterIP + static pod hostname.
* For external access, **use LoadBalancer with ExternalIP** or HostPort on dedicated node.

### 17. Secure Multi-Tenant Cluster

Controls:

- Use **RBAC** to restrict access per namespace.
- Apply **Network Policies** to isolate traffic.
- Use **PodSecurityPolicies or OPA Gatekeeper**.
- Enable audit logging and monitor activity per namespace.

### 18. Ingress Stops Routing Traffic

**Debug**:

Check Ingress controller logs.
Validate service endpoints: `kubectl get endpoints <svc>`
Look for cert or path issues in annotations.


**Fix**:

- Redeploy Ingress or check IngressClass.
- Ensure backend service is healthy and matches selector.

### 20. Securely Inject & Rotate DB Credentials

**Best Practices:**

- Store credentials in Kubernetes Secrets.
- **Use external secret managers like AWS Secrets Manager or HashiCorp Vault.**
- **Rotate credentials using a sidecar or sync controller (e.g., External Secrets Operator)**.

## Part 2

#### 1. What is the difference between ClusterIP and ExternalName service types?

* ***ClusterIP (default): Exposes the service on an internal IP, accessible only within the cluster.**
* ExternalName: **Maps a service to an external DNS name via CNAME, no internal proxying**.

```
apiVersion: v1
kind: Service
metadata:
	name: external-service
spec:
 type: ExternalName
 externalName: api.external.com
```

**Use when you want to reference external services (e.g., AWS RDS) via DNS.**

#### 2. How does Kubernetes handle failed liveness probes over time?

Kubernetes uses liveness probes to check if a container is healthy. If the probe fails for a number of attempts (failureThreshold), it restarts the container.

```
livenessProbe:
  httpGet:
   path: /healthz
   port: 8080
initialDelaySeconds: 10
periodSeconds: 5
failureThreshold: 3
```

If the container fails 3 checks (over 15 seconds), it will be restarted automatically.

#### 3. How do you set up Pod Anti-Affinity rules?

Use anti-affinity to avoid scheduling similar pods on the same node for high availability.

```
affinity:
  podAntiAffinity:
  requiredDuringSchedulingIgnoredDuringExecution:
  - labelSelector:
      matchExpressions:
      - key: app
        operator: In
        values:
        - frontend
topologyKey: "kubernetes.io/hostname"
```

#### 4. Explain how Kubernetes RBAC integrates with third-party identity providers.

Use OIDC (OpenID Connect) integration with identity providers like Google, Azure AD, or Okta.

**1. Enable OIDC in the API server:**

```
--oidc-issuer-url=https://accounts.google.com \
--oidc-client-id=your-client-id
```


#### 5. How does Kubernetes handle node scaling with Cluster Autoscaler?

**Cluster Autoscaler (CA) automatically scales node groups in cloud environments (e.g., AWS ASG, GKE node pools).**

```
helm repo add autoscaler https://kubernetes.github.io/autoscaler
 helm install cluster-autoscaler autoscaler/cluster-autoscaler \
 --set autoDiscovery.clusterName=<your-cluster> \
 --set awsRegion=<region>
```

CA monitors unschedulable pods and scales nodes up/down accordingly.


#### 10. How do you handle stuck finalizers during resource deletion?

Resources with finalizers won't delete until cleanup logic runs.

**View the finalizers:**

**`kubectl get <resource> -o json`**

**Remove finalizers (carefully):**

```
kubectl patch <resource> --type merge -p '{"metadata":{"finalizers":[]}}'
```

Only use this when the cleanup controller is failing


#### 11. What are ephemeral containers and how do you use them for debugging?

**Ephemeral containers are temporary containers added to a running pod for debugging without affecting the original container’s state or lifecycle.**

Use Case: Debugging a crashed pod or an image without debugging tools.

```
kubectl debug -it <pod-name> --image=busybox --target=<container-name>
```

Note: Requires Kubernetes v1.18+ and EphemeralContainers feature enabled.

#### 12. Explain the role of kube-dns or CoreDNS in service discovery.

- CoreDNS is the default DNS service for Kubernetes.
- It resolves internal service names like `my-service.my-namespace.svc.cluster.local` to ClusterIP.
- Uses /etc/resolv.conf in pods, pointing to kube-dns or coredns.

#### 14. What are some potential performance bottlenecks in Kubernetes workloads?

- CPU/Memory limits too low (throttling or OOMKilled)
- Disk I/O bottlenecks (especially for stateful apps)
- Network bottlenecks
- Node pressure (insufficient resources)
- High API server load due to misconfigured controllers or operators

Monitor with:
 
- Prometheus + Grafana
- kubectl top pod, top node

#### 15. How do you handle network segmentation in a multi-tenant Kubernetes cluster?

- Namespaces for logical isolation.
- Network Policies to control traffic.

```
kind: NetworkPolicy
spec:
  podSelector: 
  matchLabels: 
    role: 
      backend
ingress:
- from:
  - podSelector:
      matchLabels: 
       role: 
         frontend
```

- Use Calico/Cilium for advanced segmentation.
- Use RBAC to restrict access to namespace resource

#### 16. What is the difference between ephemeral volumes and persistent volumes?

- Ephemeral Tied to the pod lifecycle. Data lost after pod restarts.
- Persistent Backed by PersistentVolume, survives pod restarts.

**Ephemeral Volume Example:**

```
volumes:
- name: temp-storage
  emptyDir: {}
```

**Persistent Volume Example:**

```
volumeMounts:
 - mountPath: /data
   name: data-storage
volumes:
 - name: data-storage
    persistentVolumeClaim:
      claimName: my-pv
```

#### 17. How do you manage dynamic secrets in Kubernetes for applications?


* HashiCorp Vault with Vault Agent Injector.
* AWS Secrets Manager + CSI Driver
* External Secrets Operator


**Vault injects secrets into pods using sidecar.**

- Vault injects secrets into pods using sidecar.
- Use annotations:

```
annotations:
  vault.hashicorp.com/agent-inject: "true"
  vault.hashicorp.com/role: "my-app"
```


#### 18. How would you perform a rolling update of a ConfigMap without downtime?

**ConfigMaps are not automatically updated in running pods.**

Use a checksum annotation to trigger rollout:

```
annotations:
  configmap-checksum: "{{ sha256sum .Values.configmap | trunc 63 }}"
```

Process:

1. **Update ConfigMap**.
2. **Re-deploy Deployment with new annotation**.
3. Kubernetes rolls pods gracefully.

#### 19. What is kubectl kustomize and how does it compare with Helm?

**Kustomize**: Template-free customization of Kubernetes YAML.

**Helm: Uses Go templates. Supports packaging, versioning, and dependencies.**

#### 20. How would you detect and recover from node pressure scenarios automatically?

Monitor node conditions:

```
kubectl describe node <node> | grep -A5 Conditions
```

Recover strategies:

Enable Eviction Thresholds in kubelet:

```
--eviction-hard=memory.available<500Mi,nodefs.available<10%
```

- Use Cluster Autoscaler to add more nodes.
- Alert on NodePressure, DiskPressure, MemoryPressure via Prometheus.

### 21. How do PodDisruptionBudgets (PDBs) interact with cluster upgrades?

PDBs limit how many pods can be unavailable during voluntary disruptions (e.g., upgrades).

```
spec:
  minAvailable: 80%
```


- During kubectl drain, nodes won't evict pods beyond the PDB limits, delaying upgrade.
- Adjust or temporarily remove PDBs for smooth upgrades.

#### 23. How would you migrate workloads between clusters?

1. Backup workloads:

```
kubectl get all --export -n <ns> -o yaml > backup.yaml
```

2. Backup PVs (via Velero or cloud snapshots).
3. Restore in new cluster:

```
kubectl apply -f backup.yaml
```

Tools: Velero, Heptio Ark, Cluster API, GitOps tools (e.g., ArgoCD).

#### 24. How do you detect pod-level DNS issues?

1. Use nslookup or dig inside pod:

```
kubectl exec -it <pod> -- nslookup myservice
```

2. Check CoreDNS logs:

```
kubectl logs -n kube-system -l k8s-app=kube-dns
```

3. Validate /etc/resolv.conf inside the pod.

#### 25. How does container resource throttling work in Kubernetes?

- Throttling occurs when CPU usage exceeds the **CPU limit**.
- Memory is not throttled; instead, container gets **OOMKilled**.

```
kubectl top pod <pod>
kubectl describe pod <pod>
```

- Set proper CPU/memory requests and limits.
- Monitor with Prometheus/Node Exporter.

```
helm diff upgrade my-release ./chart

 Use Helm test hooks to verify deployments.
 Run in staging first, then promote to production using GitOps.
```

