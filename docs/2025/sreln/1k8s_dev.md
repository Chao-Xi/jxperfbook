# 1 Using Kubernetes as a Developer

**1. You are generating Kubernetes resources using `kompose`. You use the following `kompose` labels in your `docker-compose.yaml` file:**

- `kompose.volume.size` as `1Gi` for the requests storage’s size in the PersistentVolumeClaim
- `kompose.service.type` as `node` to designate the service to be created
- `kompose.controller.type` as `daemonset` to convert the deployment upon creation

When you run `kompose convert`, the command fails. Why is this?

**You must define the `kompose.service.type` as nodeport instead.**

**2. How do the containers in Pods run in a Kubernetes cluster?**

**A Pod has a group of one or more containers and it keeps them colocated and coscheduled on the same physical node, sharing its resources.**

**3. You run a Deployment and create a Service. You confirmed that the Service exists, is reachable by Domain Name System (DNS) and IP, and has been crosschecked against the original YAML manifest. A Pod is not able to reach itself with a Service IP, however. What is the issue?**

**The network is not properly configured for hairpin traffic.**

**4. A static port is exposed on every worker node's network IP address passing traffic to a service. What is this an example of?**

**A NodePort**

**5. Why is Service an optional field in the Kubernetes web user interface (UI) deploy wizard?**

**Because not every application needs to expose a Service to an external IP address**

**6. You created a blue/green deployment for your Kubernetes application. After transferring all traffic to the green version, what will you do if users start to report bugs?**

**Switch the traffic back to the blue version and destroy the green version.**

**7. Which daemon keeps track of the kernel logs and identifies kernel vulnerabilities based on predefined rules?**

**Kernel Monitor**

**8. You have a simple Pod definition with two containers. One container is a sidecar and the Pod is scheduled to run once on all three worker nodes in a cluster. How many IP addresses will the Pods consume in total?**

**Three IP addresses, one per Pod**

**9. What does the kubectl port-forward command do?**

Forward one or more local ports to a Pod.

**10. An administrator created an application deployment from the command line. An update of the container image rescales the deployment to three instances. The admin ran the following set of commands in a Kubernetes command-line interface (CLI):  `kubectl create deployment nginx --image=nginx:1.7.9`**

`kubectl set image deployment/nginx nginx=nginx:1.9.1 --record=true`

`kubectl scale deployment/nginx --replicas=3`

Because of an incident that the admin must fix in the application version, they reverse the deployment revision as follows:  `kubectl rollout undo deployment/nginx`

What values will the Image and Replicas fields indicate for the deployment nginx if the admin runs the command `kubectl describe deployment/nginx`?

```
Image: nginx:1.7.9
Replicas: 3
```

**11. You want to create a new CronJob object called demo-job that uses the image training/demo-job and runs the /scripts/job.sh script every minute. What command runs the Kubernetes command-line interface (CLI)?**

```
kubectl create cronjob demo-job --restart=OnFailure --schedule="*/1 * * * *" 
                       --image=training/demo-job -- /scripts/job.sh
```

**13. What is true of a blue/green deployment?**

**During deployment, both the current and the new version of the application are running at the same time so that traffic can be drained from the current version and all new traffic can be sent to the new version.**


**14. Which metadata field in a Pod is populated by the system?**

**generation**

**15. You created a Deployment with five replicas using the command `kubectl apply -f https://eg.com/demo/controllers/dep.yaml`. How can you verify if all five replicas are ready in the Deployment?**

Run `kubectl get deployments` and check if the **AVAILABLE** label has the number five in it.

**16. How many ways can you discover a Service in Kubernetes?  4**

**17. How are files from a directory defined inside a ConfigMap?**

With the contents of a single file in the **data** attribute or a collection of key/value pairs that are also defined in the **data** attribute of the object

**18. You must implement a probe to stop Pods from receiving traffic when they are busy. The probe should also make sure the Pods are taken out within 20 seconds, checking on the /healthz:8080 endpoint. What would be the correct YAML to configure this?**

```
readinessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 3
  periodSeconds: 5
```

**19. Which container acts as a client proxy and proxies the connections to the main container?**

**Ambassador**

**20. Consider the following configuration file of a Service with multiple ports:**

```
apiVersion: v1
kind: Service
metadata:
  name: service-demo
spec:
  selector:
    app: PSDemo
  ports:
    - name: 0_http
      protocol: TCP
      port: 80
      targetPort: 2342
    - name: https-p
      protocol: TCP
      port: 443
      targetPort: 9871

```

This file results in an error because of an invalid port name. What fixes must you make to remove the error?

**Remove `0_` from `0_http`**


**21 Given the following PersistentVolume (PV) belonging to the storage class Recyclable:**

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: persistent-disk
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: Recyclable
  nfs:
    path: /tmp
    server: 172.17.0.2
```

**The PVC is created with 5 Gb of storage capacity and the PV locks the remaining amount.**

**22 You must load the following configuration file into Kubernetes as a ConfigMap:**

```
## application config appconfig.txt
## comments for dev use only

host=reports.example.com
port=80
timeout=6
```

When you run the command `kubectl create configmap myconfig --from-file=./appconfig.txt`, however, the ConfigMap is created with the comments intact, which is causing your application to throw an error. How would you fix the command?

```
kubectl create configmap myconfig --from-env-file=./appconfig.txt

```

**23 In Kubernetes, which environment variable controls the log destination?**

**`KUBE_LOGGING_DESTINATION`**


**24 What changes will you make to the following deployment definition to ensure that there were no unavailable Pods and never more than 12 Pods running nginx during a rolling update deployment?**

```
spec:
  replicas: 10
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 2
      maxUnavailable: 0
```

**25 You are working with a PersistentVolumeClaim and pass the same value to both the dataSourceRef and dataSource fields. This results in an error from the dataSourceRef field. You confirm that the value of the apiGroup is demo.kubernetes.ex. What is the cause of the error?**

**The object doesn't belong to any of the available apiGroup values.**

**26 Given the following PersistentVolumeClaim (PVC) and PersistentVolume (PV) definition:**

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: persistent-disk-claim
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 8Gi

apiVersion: v1
kind: PersistentVolume
metadata:
  name: persistent-disk
spec:
  capacity:
    storage: 8Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: Recyclable
  nfs:
    path: /tmp
    server: 172.17.0.2
```

**`Add the storageClassName: Recyclable attribute to the PVC.`**

**27 While working with kubectl, you encounter an error. To troubleshoot it, which verbosity level would you choose to get its detailed trace?**

```
--v=5

```

**28 Which is a valid label value?**

**An empty value**

**29 How are files from a directory defined inside a ConfigMap?**

With the contents of a single file in the data attribute or a collection of key/value pairs that are also defined in the data attribute of the object

30 You must mount a directory folder of configuration files located at /app/config/ on a Pod. What command can you use in a Kubernetes command-line interface (CLI) to create a ConfigMap named app-config with all files as key/value pairs?

**`kubectl create configmap app-config --from-file=/app/config/`**

31 Why would you use the shorthand R with the kubectl rollout command?

**To process the directory specified in `-f` recursively**

**32 What purpose does the `--dry-run option` flag serve when running the command: `kubectl rollout undo deployment/web-application-demo --to-revision=2 --dry-run`?**

**If set, the command only prints the object that would be sent without changing the cluster state.**

**33 You must implement a probe to stop Pods from receiving traffic when they are busy. The probe should also make sure the Pods are taken out within 20 seconds, checking on the /healthz:8080 endpoint. What would be the correct YAML to configure this?**

```
readinessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 3
  periodSeconds: 5
```

**34 Consider the following Pod definition**

```
apiVersion: v1
kind: Pod
metadata:
  name: cuda-test
spec:
  containers:
    - name: cuda-test
      image: "k8s.gcr.io/cuda-vector-add:v0.1"
      resources:
        limits:
          nvidia.com/gpu: 1
  nodeSelector:
    accelerator: nvidia-tesla-p100
```

**Equality-based requirements**


**35 A cluster administrator created a StorageClass resource named gce-storage-disk, allowing an external GCEPersistentDisk storage system to dynamically provision volumes. What actions must a developer take to bind PersistentVolumesClaims (PVC) to PersistentVolumes (PV) of the storage class gce-storage-disk?**

**Set the `spec.storageClassName: gce-storage-disk` attribute on PVC resources.**

**36 You are diagnosing a problem, but running kubectl get events produces too many events for you to see the problem. Which command would you use to view only the important events?**


**`kubectl get events --field-selector type!=Normal`**