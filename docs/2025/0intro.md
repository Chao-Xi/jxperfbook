## 1.Introduction

I’m Jacob. I graduated from Stevens Institute of technology in Hoboken New Jersey. And got my master’s
degree of Computer Science. My major was data science and data system.

After I graduated, I took a contract job in startup company named Wrevel in New York city, working as
backend developer to build the company website with PHP and MYSQL database.

And then after one year, I finished my project and made my decision to came back to Shanghai and working as DevOps engineer in Blackboard. The Blackboard is a company which provides online teaching and learning SAAS system for universities and companies. 

My projects mainly include migrating the old CI/CD tool jenkins servers and jobs from on-premsie servers lab to AWS cloud, include AWS infrastructure and jenkins servers build with AWS Cloudformation and ansible playbook an IAC tool. Move jenkins master from servers to Docker, design and **built new pipeline jobs to replace old multi-branch and free-style jobs**, convert all old and manually created jenkins jobs to groovy and script code. And I also created and moved part of jenkins servers and jobs from ec2 and dockers to kubernets cluster, try to achieve the dynamic spinning up and removing jenkins agents based on buildout requirement. And beside of this. My daily operation job like covered scheduled release jobs to update client site with Chef cookbook and Jenkins pipeline job.

After that I landed my new opportunity in SAP Jam and Workzone team and working as Senior DevOps engineer. 

Jam is more like company version content management system and social system. Compared to the job before, I have more opportunities to manage and operate on on-premises data centers, we have like more than 30 DCs globally. 

Our application is build with ruby on rails in Docker, besides writing code and operate our DCs with
Chef-solo. My most important project is build our Jam application with Kubernetes on AWS and Azure from scratch and convert part of monolithic services to micro service. 

The whole procedures like, containerize our infrastructure components like Elasticsearch,
RabbitMQ and Haproxy etc. Set up Kubernetes Cluster on Azure via AKS and on AWS with EKS with Terraform Code. Build out Cloud Infrastructure like CDN, S3(Storage account), RDS MySQL, Aurora, or Azure DBs and VNet with Terraform code. 

Use HELM Chart and kustomise, with GitOps way and ArgoCD as CD tool to deploy all JAM components into Kubernetes Cluster.**Use SealedSecret and ArgoCD to encrypt secrets and push into Git Repo to store our encrypted secrets.** 

Monitor Cluster with Prometheus Operator, writing or own ServiceMonitor and Metrics / Exporter and AlertManger to help us collect data from a variety of sources and report alert. 

Get application log, operational log and audit log by EFK. And I also enabled Service Mesh inside the
cluster. Install Istio and enable envoy sidecar inject in our main namespace and track the network
tracing with Kiali & Jaeger and enable mutual X509 TLS service call from internal and external.

And after that, I’m trying to adopt security as service like **hadolint**, and apply it to pipeline, mainly help us to do the docker image scan in CI pipeline. 

When I was wokring new Product called DWZ(Digital Work ZONE). And also trying to move part of our service components from **Self maintenance like objectstore**, **rabbitmq and Log service** to SAP BTP platform. 

And In UBS, I worked in two branch companies.  I worked in AM(Assesst management) team firstly, I mainly work as Cloud archiect, design and setup the hybrid cloud infra for the WIND market data service which helps onboard the service on Azure public cloud as upstram data resource and send the data to on-premise downstream AM trading system. 

And after that, I was internal transfered to Global Investmen bank research team as Senior Devops Engineer.

Woked with global team to migrate global research analyst platform to China region mainly like, setup on-premise datacenter for different envionments like servers, LBers, NAS storage, and deployed middlewares and service like postgres, ES, kafka, nginx, tomcat etc with Gilab-CI as CI pipeline and designed new puppet modules as CD pipeline. To automate all buildout step to avoid any manual jobs

And aprart from this project. And now I'm work in gobal team to manage and operate the global systems. Like helped and setup OpenAI service in Azure cloud with terraform code, Azure Devops pipeline to automate the setup process. And Helped setup Azure translate service in HCI AKS cluster for Chinese-Eng translate service specific for China platform

Recently I working on NEW project with Vault, Azure AD and PWM API to help automate password rotaion process to replace any manul jobs to rorate account password in yearly basis

Besides these, I passed AWS solutions architect test and azure administrator test 104 and Certified Istio service mesh and gitlab CICD test, 

I also create multiple tutorial books for my colleague this year mainly about Azure ,AWS, Elasticsearch, Reddis, Chef & Ansible, Istio So that’s all about me these couple years.

## Why left the current team

1. I working in a global team, this team actually based in LDN. So I have to work with them like meetings, discussion, troubleshootings every day late night, that's the main resaon. I don't think it's good to my health to stay here any longer
2. In the financial company, I dont see my future here. Cause, comparing to new techs, codes or new solutions, they are more concern about procedures. Every day, I spend more times on writing documents, go-through steps, meeting with compliance team than working on my codes and my operations jobs. And I dont' even have any chance to learn new techs here. Thus I feel like I gonna be wasted in this team for longer time. 
3. Another thing I dont see myself in Global team can get prometed cause Im not sitting with them. I only one in China. 


## Project blocker and resolver

Actualluy happened in SAP. I introduced, brought in and set up the open-source monitoring tool Prometheus and Grafana as our Kubernetes cluster monitoring and alerting solution. Before it, we are using the traditional monitor tool Zabbix and it was set up by OPS team which only provide basic info like server CPU, memory, network throughout, disk info etcs. 

We are couple blocked in this project, 

First no one in our team has experience to set up the whole monitor, view, and alerting system from scratch. 

Second, most of them do not has faith in open-source solution and they prefer enterprise solutions. like zabbix or datadog

However, on cloud native infrastructure, Prometheus solution has more advantages especially it not only can used to monitor the basic infra and it also be easily customized to monitor different services, like cache service, queue service, and search services. Compared to enterprise solution, it’s more flexible, we can have full management privilege, and it’s the most prevalent monitor solution currently. 

After I give my teams the detail intro for this solution. I started to make proof of concept(POC) on the project, I choose one of our internal test Kubernetes cluster works as my demo environment to test the availability. 

In the poc part, I also encounter some blocks like I’m not expert for all service, so I need to consult to developer who write the service, and with the help to customize the metrics for services. 

And after the couple months, the whole system setup, which provide much more stability for our internal tool and we can easily do the troubleshooting and receive the alarm from alert system. Then we adopt the solutions and push this solution to our main production service cluster. Everyone in our team started to learn it and got they own task to write different metrics for the all microservices.

And after setup, I also brought in Loki, Grafana, promtail solution as Log aggregrator easiy working together with prometheus solution. More like two birds with one stone

## 2.Self Description

1. I consider myself as a good leaner, I’m willing to learn all new stuffs. I learn things efficiently and effectively. You can check my github, I push my tech articles into it almost everyday. And every time when I faced a project, there some techs I never touched before. I don’t just jump into this project, I’m willing to spend 2 or 3 days to explore this tech. **I can always deliver high quality project**
2. I consider myself more extrovert than introvert, I prefer to work in a team rather than work alone. I’d like share my opinion and listen to others' advices on the job.
3. Im pretty good at time management, Im never missed deadline. I don’t like procreations, if the jobs or tickets must be done in a sprint, I’d like do it right way, not holding it back to the due day. So I can alway deliver assigned issues on time no holding back at all


## 3.What is Devops definition

DevOps is the gray area between development (Dev) and operations (Ops) teams in the product development
process. DevOps is a culture that emphasizes communication, integration, and collaboration in the product development cycle.

 As such, it removes barrier between software development and operations teams, enabling them to integrate and deploy products quickly and continuously.
 
 
* Accept failure as normal, try to spot it and resolve it on time
* Reduce the cost of failure, so try to implement gradual changes, like migration, update on system
* Build automation tool to reduce the uncertainty of manual work, like build the cicd pipeline.
* Measure everything, build the monitor-tools, log aggregator and alert system to help to supervise whole system
* Reduce the cost.
* Reduces the time required to recover from failures on average.
* Increase the frequency of deployments.
* Reduce deployment failure rate.


## 4 Well-Architected Framework

* Security
* Reliability
* Performance Efficiency
* Cost Optimization
* Operation


### **1.Security**

**Data protection:**

* Encrypting and protecting your data at rest with encryption algorithm and in transit with ssl
* Versioning, for example, our most data was stored in S3 bucket in AWS, we use versioning, one data
with different version, can protect against accidental overwrites deletes.

**Privilege Management**

1. Password Management (such as password rotation policies)
2. Enable Multi-Factor Authentication(MFA) in your account to login into our cloud service, we use DUO on our phone to send notification every time, we want to login into the account.
3. Role Based Access Controls, we give every server like ec2 different role, which can restrict they can only access different resource necessary
4. Access Control Lists (ACLs), different operators, only get temporary access to certain servers only authorized by managers

**Infrastructure Protection**

We set our servers in different VPC, AZ, security group, public subnet, private subnet, and only can access them by jump host/ bastion host

**Detective Controls**

We use cloudtrail audit and monitor on all account, you can discover and troubleshoot security and operational issues by capturing a comprehensive history of changes that occurred in your AWS account within a specified period of time.

And use cloudwatch and sms to monitor the aws resource and use prometheus and EKF on our clusters

### 2. Reliability (everything turned to code)

* Automatically recover from failure
* Scale horizontally

**Automatically recover from failure**

* AWS Servers like EC2, we use cloudformation to put all servers settings into code
* AWS RDS, We use auto-back and Multi-AZ which can synchronically backup the database into other secondary RDS to protect rds failure
* other servers like Kubernetes cluster or other virtual machine, we use terraform to put all servers settings into code

**Scale horizontally**

* aws we use auto scaling group for our EC2 servers
* HPA and deployment for our containers
* backup all logs into s3 bucket

### 3. Performance Efficiency


* Compute= =>. Autoscaling
* Storage => type of EBS, SSD HDD storage compare their connections and IOPS
* Database => read replicas, dynamodb for web sessions, metadata, Aurora, multiple write and read
* time-space trade => ElastiCache(redis) and cache for redis, Direct Connect

### 4.Cost Optimization

* Monitoring usage and spending every month
* Autoscaling
* decommission resources that you no longer need, stop resources that are temporarily not needed
* Merge low used resource, like some rds, we used shared rds instead.