### 1. What are all the types of applications you have deployed?

* Web applications (e.g., Java Spring Boot, Node.js),
* microservices architectures, 
* databases (e.g., MySQL, MongoDB), 
* message brokers (e.g., Apache Kafka), 
* monitoring tools (e.g., Prometheus, Grafana), 
* CI/CD pipelines (e.g., Jenkins), 
* Containerized applications using Docker and Kubernetes

### 2 How have you injected the secrets in ConfigMaps

* Secrets should not be injected in ConfigMaps as ConfigMaps are not designed for  sensitive data. 
* Instead, Kubernetes Secrets should be used. **Secrets can be injected into pods via environment variables or mounted as file**s.

### 3 How do you find which pod is taking more system resources across nodes using kubectl?

Use 

```
kubectl top pod --all-namespaces
``` 

to list resource usage by pods. 

Combine it with `kubectl describe pod <pod-name>` to get detailed resource usage

### 4 How do you know which worker node is consuming more resources across the clusters using kubectl?

Use `kubectl top nodes` to see resource consumption across nodes. 

This will show CPU and memory usage on each node.

### 5. What are the steps for configuring Prometheus and Grafana for monitoring  Kubernetes clusters?

1. Deploy Prometheus using Helm or a custom YAML configuration.
2. Set up Kubernetes **service discovery for Prometheus**.
3. Deploy Grafana and configure it to use Prometheus as a data source.
4. **Import Kubernetes monitoring dashboards in Grafana**.
5. Set up **alerting rules in Prometheus as needed**.

### 6. If 20 pods are running, how do you visualize the metrics of these pods in  Grafana?

Configure Grafana to use Prometheus as the data source. Use existing Kubernetes dashboards or create custom dashboards to visualize metrics for all pods.

### 7 What is Apache Kafka?

Apache Kafka is a distributed event streaming platform used for building real-time  data pipelines and streaming applications. It’s designed to handle large volumes of data in a  distributed, fault-tolerant manner.

### 8. How do you set up a Docker Hub private registry and integrate it with a CI/CD  pipeline? What is the procedure?

1. Create a private repository on Docker Hub.
2. Configure CI/CD pipeline to authenticate with Docker Hub using credentials.
3. Build Docker images in CI pipeline and push them to the private repository.
4. Pull images from the private registry during the CD process

### 9. What is the difference between a hard link and a soft link?


* **Hard Link**: A direct reference to the file’s data on the disk. 
	* **Deleting the original file does not affect the hard link.**


* **Soft Link (Symbolic Link)**: A pointer to the original file’s path.
	* **If the original file is deleted, the soft link becomes broken**.

### 10. What is the use of the break command in shell scripting? In what scenarios have you used it?

**The `break` command is used to exit a loop prematurely**. It is commonly used when a specific condition is met, and you no longer need to continue the loop

### 11. How do you count the number of "devops" words in 15 HTML files?

To count the number of times the word "devops" appears across 15 HTML files, you can use the  following shell command:

```
grep -o -i "devops" *.html | wc -l
```

*  `grep -o -i "devops" *.html`: Searches for the word "devops" (case-insensitive due to -i) in all HTML files and outputs each occurrence on a new line (-o).
* `wc -l`: Counts the number of lines output by grep, which corresponds to the number of occurrences of "devops". 


### 12. What is the `terraform taint` command?

The `terraform taint` command is used to manually mark a specific resource for recreation during the next `terraform apply`. 


**When a resource is tainted, Terraform will destroy it and create a new instance of it on the next apply.**

This is useful when you know a resource needs to be replaced but Terraform hasn't automatically determined that it should be

```
Syntax: terraform taint <resource_address>
Example: terraform taint aws_instance.example
```

### 13. What are the possible ways to secure a state file in Terraform?

1. **Encrypt the State File**: Store the **state file in an encrypted format using tools like AWS S3** 
bucket encryption, Azure Blob encryption, or Google Cloud Storage encryption.
2. **Use Remote Backends with Security Controls**: Use a remote backend like AWS S3 with IAM roles and policies, HashiCorp Consul with ACLs, or Terraform Cloud, which manages access 
control and encryption.
3. **Enable Versioning**: Use versioning on the state file storage to recover from any unintended 
changes or deletions.
4. **Access Control**: Restrict access to the state file using IAM policies, RBAC (Role-Based Access Control), or similar mechanisms.
5. **Use Backend Locking**: Use state locking mechanisms provided by backends like S3 with DynamoDB or Consul to prevent concurrent operations from corrupting the state file.

### 14. If you provision 100 servers and someone deletes 50 VMs manually, what happens if you apply the `terraform apply` command?

When you run `terraform apply`, Terraform will compare the state file with the actual infrastructure. 

It will notice that 50 VMs are missing and will attempt to recreate those missing VMs to match the state defined in your configuration. 

The end result will be 100 VMs again.

### 15. What is the syntax for `for_each` in Terraform?

The `for_each` meta-argument in Terraform allows you to create multiple instances of a resource or 
module based on the items in a map or set. 

The syntax is as follows:

```
resource "aws_instance" "example" {
	for_each = var.instances
	ami = each.value["ami"]
	instance_type = each.value["instance_type"]
	tags = {
 		Name = each.key
 	}
 }
```

In this example, 

**var.instances is a map, and each.key refers to the current key in the map,** 
while each.value refers to the associated value.

### 16. What are the advantages and disadvantages of multi-stage builds in  Docker?

#### **Advantages**

* **Smaller Image Size**: By separating the build environment from the runtime environment, only the necessary components are included in the final image, leading to smaller image sizes.
* **Improved Security**: Reduces the attack surface by excluding build tools and other unnecessary components from the final image.
* **Better Caching**: Allows for more efficient use of Docker’s caching mechanism, potentially speeding up the build process.
* **Separation of Concerns**: Different stages can focus on specific tasks, making the Dockerfile more organized and easier to maintain.

#### **Disadvantages**

* **Complexity**: Multi-stage Dockerfiles can be more complex and harder to understand, especially for those new to Docker.
* **Longer Build Times**: In some cases, the use of multiple stages may lead to longer build 
times due to additional steps and transitions between stages.
* **Troubleshooting**: Debugging and troubleshooting can be more challenging because intermediate stages are not preserved by default.

### 17. How do you deploy containers on different hosts, not the same host, within a Docker cluster?

To deploy containers on different hosts within a Docker cluster, you can use Docker Swarm or Kubernetes

**Docker Swarm: Use Docker Swarm’s built-in orchestration capabilities**. 

When deploying a service, Swarm will automatically distribute the containers across different nodes in the 
cluster. You can influence this behavior using placement constraints.

Example

```
docker service create --name myservice --replicas 2 --constraint 'node.role == worker' myimage
```

Kubernetes: Use Kubernetes, which will schedule pods on different nodes based on the available resources and any specified node selectors or affinities

### 18. If you have a Docker Compose setup, how do you deploy the web container on one host and the DB container on another host?

To deploy the web container on one host and the DB container on another using Docker Compose, 
you can:

1. **Use Docker Swarm**: Convert the Compose file into a stack file, and deploy it with docker 
stack deploy. Docker Swarm will distribute services across nodes.
2. **Manual Placement**: Use placement constraints in your docker-compose.yml file to specify which containers should run on which nodes.

```
version: '3.7'
services:
 web:
 image: my-web-image
 deploy:
  placement:
  constraints: [node.hostname == web-node]
 
 db:
 image: my-db-image
 deploy:
 	placement:
 	constraints: [node.hostname == db-node]
```

### 19. What is the difference between bridge networking and host networking in Docker

* **Bridge Networking**: **The default Docker network driver**. 
	* Containers connected to the same bridge network can communicate with each other. 
	* Each container gets its own IP address 
and is isolated from the host network. You can expose ports to the host using the `-p` option.
* **Host Networking**: The container shares the host's network stack, 
	* **meaning it doesn’t get its own IP address, and the container's network is the same as the host's network**. 
	* This can lead to performance improvements but reduces isolation between the host and the container.

### 20. How do you resolve merge conflicts

To resolve merge conflicts, follow these steps:

1. Identify the Conflict: Git will mark the conflicting areas in the files with conflict markers 
(<<<<<<<, =======, and >>>>>>>).
2. **Manually Resolve**: Open the conflicting file and decide how to combine the changes. Remove the conflict markers and modify the content to resolve the conflict.
3. **Stage the Resolved Files**: Once resolved, stage the files using `git add`.
4. **Commit the Changes**: Commit the resolved conflicts with `git commit`. If you were in the middle of a merge, this will complete the merge process.
5. **Test**: Run tests to ensure that the merge didn’t introduce any issues.

Example:

```
git add <resolved-files>
git commit -m "Resolved merge conflicts in X, Y, Z files
```