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

### 21. What command do you use to change the existing commit message?

To change the most recent commit message, you can use the following Git command

```
git commit --amend -m "New commit message"
```

If you have already pushed the commit to a remote repository, you will need to force push the 
changes:

```
git push --force
```

### 22. What is session affinity?

**Session affinity, also known as sticky sessions**, is a concept in load balancing where requests from a 
particular user are consistently directed to the same server (or pod) in a multi-server environment. 


**This ensures that the user's session data, which might be stored locally on the server, remains 
accessible throughout the session**.

### 23. What is pod affinity and its use case?

Pod affinity is a feature in Kubernetes that allows you to specify rules for scheduling pods to run on 
nodes that have other specified pods running on them. 

This can be useful when you want certain 
pods to be located together due to factors like data locality, network latency, or shared resources.

Use Case: An application where the frontend and backend services communicate frequently might 
use pod affinity to ensure that both are scheduled on the same node to reduce network latency

### 24. What is the difference between pod affinity and pod anti-affinity?

* **Pod Affinity:** Ensures that pods are scheduled on the same node or in proximity to each other.
* **Pod Anti-Affinity**: Ensures that pods are not scheduled on the same node or are placed far 
* apart from each other.

Example Use Case:

* **Pod Affinity:** Running frontend and backend on the same node for low-latency communication.
* **Pod Anti-Affinity**: Ensuring replicas of the same application are spread across different nodes to increase availability and fault tolerance.

### 25. What are readiness and liveness probes?

* **Readiness Probe**: Used to determine when a pod is ready to start accepting traffic. If the  readiness probe fails, the pod will be removed from the service endpoints, ensuring it does not receive traffic until it's ready.
* **Liveness Probe:** Used to determine if a pod is still running. If the liveness probe fails, Kubernetes will restart the pod, assuming it's in a failed state.

#### 26. Write a simple Groovy pipeline for a Java Spring Boot application that waits for user input for approval to move to the next stage, with stages for checkout,  build, push, and deploy

```
pipeline {
 agent any
 
 stages {
 	stage('Checkout') {
 		steps {
 		git 'https://github.com/your-repo/java-spring-boot-app.git'
 	}
 }
 
 stage('Build') {
 	steps {
 		sh './mvnw clean package'
 	}
 }
 
 stage('Push') {
 	steps {
 		sh 'docker build -t your-docker-repo/java-spring-boot-app .'
 		sh 'docker push your-docker-repo/java-spring-boot-app'
 	}
 }
 
 stage('Approval') {
 	steps {
 		input 'Do you want to deploy to production?'
 	}
 }
 
 stage('Deploy') {
 	steps {
 		sh 'kubectl apply -f deployment.yaml'
 		}
 	}
 } 
}
```

### 27. How do you export test reports in Jenkins?

To export test reports in Jenkins:

1. Ensure that your tests generate reports in a standard format **like JUnit XML or HTML**.
2. Use the **Publish JUnit test result report post-build action to archive test reports**.
3. You can also use the **Archive the artifacts** post-build action to store other types of reports.
4. The test reports can then be accessed and downloaded from the Jenkins build page

### 28. If 5 pods are running, how do you scale the number of pods to 10 using the  command line in Kubernetes?

To scale the number of pods from 5 to 10, use the following command:

```
kubectl scale --replicas=10 deployment/<your-deployment-name>
```

### 29. Can you explain the usage of the terraform import command?

The terraform import command is used to import existing infrastructure resources into Terraform's state file, allowing Terraform to manage them. 

This is particularly useful when you want to bring existing infrastructure under Terraform management without having to recreate resources.

```
terraform import <resource_type>.<resource_name> <resource_id>
```

```
terraform import aws_instance.my_instance i-1234567890abcdef0
```

### 30. In AWS, where do you store the state file, and how do you manage it?

In AWS, Terraform state files are typically stored in an S3 bucket, and the state can be managed using a combination of **S3 and DynamoDB for locking**.

Example Configuration:

```
terraform {
 backend "s3" {
 bucket = "my-terraform-state-bucket"
 key = "path/to/my/terraform.tfstate"
 region = "us-west-2"
 dynamodb_table = "terraform-lock-table"
 encrypt = true
 }}
```

Management

* **S3**: *Stores the state file securely, supports versioning, and can be encrypted*.
* **DynamoDB**: Provides locking to prevent multiple simultaneous operations on the same state file, avoiding conflicts.


This setup ensures that your Terraform state is stored securely and is protected from simultaneous 
access issues

#### 31. What is the biggest issue you have faced with Terraform, and how did you resolve it?

One significant issue I faced was managing Terraform state when multiple teams were working on the same infrastructure. 

**This was resolved by organizing the infrastructure into separate modules** and **using remote state management with proper locking mechanisms**

#### 32. What are the types of storage accounts in AWS S3?

In AWS S3, the different storage classes include:

* S3 Standard
* S3 Intelligent-Tiering
* S3 Standard-IA (Infrequent Access)
* S3 One Zone-IA
* S3 Glacier
* S3 Glacier Deep Archive

#### 33. Are you familiar with lifecycle management in S3 buckets? How do you set up lifecycle  policies?

Yes, lifecycle management in S3 allows you to define rules to transition objects between different storage classes or delete them after a certain period. 

Lifecycle policies can be set up using the S3 Management Console, AWS CLI, or Terraform by specifying the transitions and expiration actions in a JSON configuration file.

#### 34. What are the differences between load balancers, and why do we need them?

Load balancers distribute incoming network traffic across multiple servers. The main types are:

* **Application Load Balancer (ALB)**: Operates at the application layer (Layer 7), and is used for HTTP/HTTPS traffic with advanced routing capabilities.
* **Network Load Balancer (NLB)**: Operates at the transport layer (Layer 4) and is used for ultra-low latency TCP/UDP traffic.
* **Classic Load Balancer (CLB)**: Supports both Layer 4 and Layer 7, but is now mostly deprecated in favor of ALB and NLB

Load balancers improve fault tolerance, scalability, and ensure high availability.

#### 35. Have you worked with Auto Scaling Groups (ASG)

Yes, **I have worked with ASGs to automatically scale the number of instances in response to demand**. 

ASGs are configured with policies that adjust the desired capacity based on metrics such as CPU utilization, helping to maintain application performance and optimize costs.

#### 36. Can you write a simple Dockerfile?

Yes, here is a simple example of a Dockerfile for a Node.js application:

**Dockerfile**

```
FROM node: 14
WORKDIR /app
Copy package.json
RUN npm install
COPY . .
EXPOSE 3000
CMD [ "npm", "start"]
```

#### 37. If you want to expose your application to the public internet or access your application within a cluster, how would you do that in Kubernetes? 

To expose your application to the public internet, you can use a Kubernetes **Service** of type **LoadBalancer or NodePort.** 

For internal access within the cluster, you can use a **ClusterIP** service. Additionally, you might use an Ingress controller for more advanced routing.

### 38. Why do we need a ConfigMap in Kubernetes?

A ConfigMap is used to store non-confidential configuration data in key-value pairs. 

It allows you to decouple configuration artifacts from image content, enabling you to modify application settings without rebuilding your container images

### 39. Which AWS services do you consider when setting up a CI/CD pipeline for a microservices application?

 For a CI/CD pipeline, I would consider using:
 
* **AWS CodeCommit** for source control.
* **AWS CodeBuild** for building and testing.
* **AWS CodeDeploy** or Amazon EKS for deployment.
* **AWS CodePipeline** to orchestrate the CI/CD process.
* **Amazon S3** for artifact storage.
* **AWS Lambda** for any custom automation tasks.

#### 40. On a day with unusually high traffic for an e-commerce application, how would you, as a cloud engineer, manage the current setup to handle the load smoothly

I would ensure that **Auto Scaling Groups** are properly configured to handle the increased demand by automatically adding more instances. 


I'd also verify that the load balancers are evenly distributing traffic and consider enabling caching (e.g., using Amazon CloudFront) to reduce the load on backend servers. Monitoring tools like CloudWatch would be used to  track performance metrics and adjust resources in real-time

**Strategies:**

* **Auto Scaling**: Scale up instances automatically to handle the increased load.
* **Caching**: Use a caching layer to reduce the load on your application servers.
* **Load Balancing**: Distribute traffic evenly across available instances.
* **Database Optimization**: Ensure your database is properly configured and optimized for performance.
* **Monitoring**: Closely monitor system metrics to identify bottlenecks and adjust resources accordingly