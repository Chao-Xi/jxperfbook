
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
 
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

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
 
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

- Use kubectl get networkpolicies -n <ns>
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