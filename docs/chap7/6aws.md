# **6 AWS** 

## **Stage 1: Manually create a VPN connection for two “sites”**

* Two “sites” in one AWS region under a single AWS account. (Each site is hosted in a VPC with an application of 1,000+ EC2 instances.)
* Customer asks to build a “site-to-site” VPN connection between the two VPCs 
	* so that any servers of the two applications can talk in a secured and scalable way.

> VPC peering is supposed to be not available in this region.



Candidate is expected to manually implement the VPN connection in the lab environment by deploying any necessary AWS resources or open-source tools (VPN solutions like openSwan or strongSwan).


## Provision the VPN connection with CloudFormation (CFN) template

In this stage, candidate is required to create the above VPN connection with a CloudFormation (CFN) template

* EC2 security group to enable for ingress and egress traffic.
* Resource and User Data - Use a script in User Data to install and start VPN Servers.
*  Define the outputs of the CFN with the following items:
	*  Instance IDs of the VPN Servers
	*  VPN Server is running in each VPC 
	*   VPN Server’s Public IP address


## Make the VPN connection robust

In this bonus stage, candidate is expected to improve the resilience of the VPN connection in Stage 1. Try
to propose an HA architecture for this connection to auto heal in case of failure.

Deliverables:

* The HA architecture chart, proposal description and necessary pseudocode. 
* Implement it and revise the CFN template to achieve auto-provision


## Solution Steps

### VPCA-INSTANCE  `Oregen`

* 1.Create `VPC-A`, region: Oregen, IPv4 CIDR: `10.100.0.0/16`
	* vpc-00bc9b0aaeb67e39a 
* 2.Createa Private subnet in: `VPCA-Private-Subnet`  `10.100.0.0/24`
* 3.Create a Route Table： `VPCA-Private-RT` , VPC: `VPC-A`
*  4. Edit Route Table:  Edit subnet associations: `VPCA-Private-Subnet`
*  5. Launch EC2 instance in this subnet `VPCA-Private-Subnet`
	* Name: `EC2-A `
	*  t2.micro,
	*  tag: `NAME` `VPCA-Private-EC2`
	*  Create a new security group
		* `EC2-A-SG`
		* All TCP: `10.200.0.0/16`
		* All ICMP - IPV4:  `10.200.0.0/16`
	* create new key: vpntest
* IP:  `10.100.0.195`

### VPCB-INSTANCE  `FRANKFURT`

* 1.Create `VPC-B`, region: Oregen, IPv4 CIDR: `10.200.0.0/16`
	* vpc-06e0539e9b9e00711
* 2. Create Internet Gateways `VPCB-IGW` and Attach to `VPC-B`
* 3. Createa public subnet in: 
	* `VPCB-Public-Subnet` 
	* `10.200.0.0/24`
	* Auto-assign IP settings: **Enable auto-assign public IPv4 address**
* 4. Create a Route Table： `VPCB-Public-RT` , VPC: `VPC-B`
	*  Edit Routes: `0.0.0.0/0` : `igw-0e4b20b5ec738efe3`
*  4. Edit Route Table:  Edit subnet associations: `VPCB-Public-Subnet`
*  5. Launch EC2 instance in this subnet `VPCB-Public-Subnet`
	* Name: `EC2-B `
	*  t2.micro,
	*  tag: `NAME` `VPCB-Router`
	*  Create a new security group
		* `EC2-B-SG`
		* SSH: TCP 22  My IP
		* All TCP: `10.100.0.0/16`
		* All ICMP - IPV4:  `10.200.0.0/16`
	* create new key: **vpnint**
	* Source/Destination check -> stop
* Private IP:  `10.200.0.41`
* Public IP:  3.67.225.7 

### SSH EC2-B

```
ls | grep vpn
vpnint.pem
vpntest.pem
```

```
$ chmod 400 vpnint.pem
$ ssh -i "vpnint.pem" ec2-user@3.67.225.7

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
No packages needed for security; 1 packages available
Run "sudo yum update" to apply all updates.
-bash: warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory
[ec2-user@ip-10-200-0-41 ~]$
```


### VPC A VPN

* VPC A Virtual Private Gateway
	* `VPC-A-VGW`:  `VPC-A`  and attach to `VPC-A`

* VPC A Customer Gateway
	* `VPC-A-CGW`:  IP address: `3.67.225.7 `

**Site-to-Site VPN connections**

* `VPCA-VPCB-VPN`
* `VPC-A-VGW`
* `VPC-A-CGW`
* static: `10.200.0.0/16

### Route table propagation `VPCA-Private-RT`

* `VPC-A-VGW:` Enable

### login EC2-B configure software VPN

```
sudo su
yum install -y openswan
```

> In /etc/ipsec.conf uncomment following line (if not already uncommented)
> 
> include /etc/ipsec.d/*.conf

```
vim /etc/sysctl.conf 

net.ipv4.ip_forward = 1
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.all.send_redirects = 0
```
 
```
service network restart
Restarting network (via systemctl):                        [  OK  ]
``` 

### Download  VPN configuration OpenSwan

* `vim /etc/ipsec.d/aws.conf`

```
conn Tunnel2
        authby=secret
        auto=start
        left=%defaultroute
        leftid=3.67.225.7
        right=35.164.50.218
        type=tunnel
        ikelifetime=8h
        keylife=1h
        phase2alg=aes128-sha1;modp1024
        ike=aes128-sha1;modp1024
        keyingtries=%forever
        keyexchange=ike
        leftsubnet=10.200.0.0/16
        rightsubnet=10.100.0.0/16
        dpddelay=10
        dpdtimeout=30
        dpdaction=restart_by_peer
```


* `vim /etc/ipsec.d/aws.secrets`
```
3.67.225.7 35.164.50.218: PSK "hvQ1jgPeP5Kfn725izXix.7OIwq6aFnI"
```

```
systemctl start ipsec

[root@ip-10-200-0-41 ec2-user]# systemctl status ipsec
● ipsec.service - Internet Key Exchange (IKE) Protocol Daemon for IPsec
   Loaded: loaded (/usr/lib/systemd/system/ipsec.service; disabled; vendor preset: disabled)
   Active: active (running) since Sat 2022-03-05 13:35:35 UTC; 33s ago
     Docs: man:ipsec(8)
           man:pluto(8)
           man:ipsec.conf(5)
  Process: 3122 ExecStartPre=/usr/sbin/ipsec --checknflog (code=exited, status=0/SUCCESS)
  Process: 3111 ExecStartPre=/usr/sbin/ipsec --checknss (code=exited, status=0/SUCCESS)
  Process: 2572 ExecStartPre=/usr/libexec/ipsec/_stackmanager start (code=exited, status=0/SUCCESS)
  Process: 2570 ExecStartPre=/usr/libexec/ipsec/addconn --config /etc/ipsec.conf --checkconfig (code=exited, status=0/SUCCESS)
 Main PID: 3143 (pluto)
   Status: "Startup completed."
   CGroup: /system.slice/ipsec.service
           └─3143 /usr/libexec/ipsec/pluto --leak-detective --config /etc/ipsec.conf --nofork

Mar 05 13:35:35 ip-10-200-0-41.eu-central-1.compute.internal pluto[3143]: | setup callback for interface eth0:500 fd 15
Mar 05 13:35:35 ip-10-200-0-41.eu-central-1.compute.internal pluto[3143]: loading secrets from "/etc/ipsec.secrets"
Mar 05 13:35:35 ip-10-200-0-41.eu-central-1.compute.internal pluto[3143]: loading secrets from "/etc/ipsec.d/aws.secrets"
Mar 05 13:35:35 ip-10-200-0-41.eu-central-1.compute.internal pluto[3143]: "Tunnel2" #1: initiating Main Mode
Mar 05 13:35:35 ip-10-200-0-41.eu-central-1.compute.internal pluto[3143]: "Tunnel2" #1: STATE_MAIN_I2: sent MI2, expecting MR2
Mar 05 13:35:35 ip-10-200-0-41.eu-central-1.compute.internal pluto[3143]: "Tunnel2" #1: STATE_MAIN_I3: sent MI3, expecting MR3
Mar 05 13:35:35 ip-10-200-0-41.eu-central-1.compute.internal pluto[3143]: "Tunnel2" #1: Peer ID is ID_IPV4_ADDR: '35.164.50.218'
Mar 05 13:35:35 ip-10-200-0-41.eu-central-1.compute.internal pluto[3143]: "Tunnel2" #1: STATE_MAIN_I4: ISAKMP SA established {auth=PRESHARED_KE...1024}
Mar 05 13:35:35 ip-10-200-0-41.eu-central-1.compute.internal pluto[3143]: "Tunnel2" #2: initiating Quick Mode PSK+ENCRYPT+TUNNEL+PFS+UP+IKEV1_A...1024}
Mar 05 13:35:35 ip-10-200-0-41.eu-central-1.compute.internal pluto[3143]: "Tunnel2" #2: STATE_QUICK_I2: sent QI2, IPsec SA established tunnel m...tive}
Hint: Some lines were ellipsized, use -l to show in full.
```


## Cloudformation

[**BonusBits CloudFormation Templates**](https://github.com/bonusbits/cloudformation_templates)

https://s3.amazonaws.com/bonusbits-public/cloudformation-templates/github/

**1VpcBCfn.yaml**

```
AWSTemplateFormatVersion : 2010-09-09

# VPC B is private VPC
Parameters:
  # VPC B
  VpcBCidrBlock:
    Description: VPC B CIDR Block
    Type: String
    Default: 10.200.0.0/16
  # Subnet B
  SubnetBCidrBlock:
    Description: Subnet CIDR Block
    Type: String
    Default: 10.200.0.0/24
  # EC2 Instance Type
  InstanceType:
    Description: EC2 Instance Type
    Type: String
    Default: t2.micro
    ConstraintDescription: must be a valid EC2 instance type
  # Key Pair Name
  KeyName:
    Description: Key Pair Name
    Type: AWS::EC2::KeyPair::KeyName
    Default: vpnint

Mappings:
  # EC2 Instance image Map
  RegionMap:
    eu-central-1:
      hvm: "ami-00e76d391403fc721"
    # us-west-2:
    #   hmv: "ami-0b9f27b05e1de14e9"

Resources:
  # VPC
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: !Ref VpcBCidrBlock
      EnableDnsSupport: true
      EnableDnsHostnames: true
      Tags:
        - Key: Name
          Value: VPCB-Public-Subnet
  
  # Internet Gateways
  InternetGateway:
    Type: AWS::EC2::InternetGateway
    Properties:
    Tags:
    - Key: Name
      Value: VPCB-IGW
  
  GatewayToInternet:
    Type: AWS::EC2::VPCGatewayAttachment
    DependsOn:
    - InternetGateway
    - VPC
    Properties:
      VpcId: !Ref VPC
      InternetGatewayId: !Ref InternetGateway
  
  # Subnet
  Subnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      CidrBlock: !Ref SubnetBCidrBlock
      MapPublicIpOnLaunch: True
      Tags:
      - Key: Name
        Value: VPCB-Public-Subnet
  # Route Table
  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref VPC
      Tags:
      - Key: Name
        Value: VPCB-Public-RT
  # Public Route
  PublicRoute:
    Type: AWS::EC2::Route
    DependsOn:
    - PublicRouteTable
    - InternetGateway
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref InternetGateway
  # Public Subnet RT Association
  PublicSubnetAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    DependsOn:
    - Subnet
    - PublicRouteTable
    Properties:
      RouteTableId: !Ref PublicRouteTable
      SubnetId: !Ref Subnet
  
  # EC2 Security Group
  InstanceSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      VpcId: !Ref VPC
      GroupDescription: TCP, ICMP, SSH
      SecurityGroupIngress:
      - IpProtocol: tcp
        FromPort: -1
        ToPort: -1
        CidrIp: 10.100.0.0/16
      - IpProtocol: icmp
        FromPort: -1
        ToPort: -1
        CidrIp: 10.100.0.0/16
      - IpProtocol: tcp
        FromPort: 22
        ToPort: 22
        CidrIp: 0.0.0.0/0
      SecurityGroupEgress:
      - IpProtocol: '-1'
        CidrIp: 0.0.0.0/0
      Tags:
      - Key: Name
        Value: EC2-B-SG
  # EC2 Instance
  EC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId:
        Fn::FindInMap: [RegionMap, Ref: AWS::Region, hvm]
      InstanceType:
        Ref: InstanceType
      SourceDestCheck: false
      KeyName:
        Ref: KeyName
      Tags:
      - Key: Name
        Value: VPCB-Router
      NetworkInterfaces:
      - GroupSet:
        - Ref: InstanceSecurityGroup
        AssociatePublicIpAddress: true
        DeviceIndex: 0
        DeleteOnTermination: true
        SubnetId: !Ref Subnet
      UserData:
        Fn::Base64: 
          !Sub |
            #!/bin/bash
            yum install -y openswan
            cat <<EOT >> /etc/sysctl.conf
            net.ipv4.ip_forward = 1
            net.ipv4.conf.all.accept_redirects = 0
            net.ipv4.conf.all.send_redirects = 0
            EOT   
            service network restart

Outputs:
  EC2Instance:
    Description: EC2 instance VPCB-EC2 ID
    Value: !Ref EC2Instance
  EC2PublicIP:
    Description: VPN Servers Public IP address
    Value: !GetAtt EC2Instance.PublicIp
```

**2VpnACfn.yaml**

```
AWSTemplateFormatVersion : 2010-09-09

# VPC A
Parameters:
  # VPC A
  VpcACidrBlock:
    Description: VPC A CIDR Block
    Type: String
    Default: 10.100.0.0/16
  # Customer Gateway A IP
  CustomerGatewayIp:
    Description: Gustomer Gateway IP from VPCB instance public IP
    Type: String
    Default: X.X.X.X
  # Subnet A
  SubnetACidrBlock:
    Description: Subnet CIDR Block
    Type: String
    Default: 10.100.0.0/24
  # EC2 Instance Type
  InstanceType:
    Description: EC2 Instance Type
    Type: String
    Default: t2.micro
    ConstraintDescription: must be a valid EC2 instance type
  # Key Pair Name
  KeyName:
    Description: Key Pair Name
    Type: AWS::EC2::KeyPair::KeyName
    Default: vpnint

Mappings:
  # EC2 Instance image Map
  RegionMap:
    # eu-central-1:
    #   hvm: "ami-00e76d391403fc721"
    us-west-2:
      hmv: "ami-0b9f27b05e1de14e9"

Resources:
  # VPC
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: !Ref VpcACidrBlock
      EnableDnsSupport: true
      EnableDnsHostnames: true
      Tags:
        - Key: Name
          Value: VPC-A
  # Customer Gateway
  CustomerGateway:
    Type: AWS::EC2::CustomerGateway
    Properties:
      Type: ipsec.1
      BgpAsn: 65000
      IpAddress: !Ref CustomerGatewayIp
      Tags:
      - Key: Name
        Value: "VPC-A-CGW"
  # VPN Gateway
  VPNGateway:
    Type: AWS::EC2::VPNGateway
    Properties:
      Type: ipsec.1
      Tags:
      - Key: Name
        Value: VPC-A-VGW
  AttachVPNGateway:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref VPC
      VpnGatewayId: !Ref VPNGateway
  # VPN Connection
  VPNConnection:
    Type: AWS::EC2::VPNConnection
    Properties:
      Type: ipsec.1
      StaticRoutesOnly: False
      CustomerGatewayId: !Ref CustomerGateway
      VpnGatewayId: !Ref VPNGateway
      Tags:
      - Key: Name
        Value: VPCA-VPCB-VPN
  # Subnet
  Subnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      CidrBlock: !Ref SubnetACidrBlock
      MapPublicIpOnLaunch: False
      Tags:
      - Key: Name
        Value: VPCA-Private-Subnet
  # Route Table
  RouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref VPC
      Tags:
      - Key: Name
        Value: VPCA-Private-RT
  RouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      RouteTableId: !Ref RouteTable
      SubnetId: !Ref Subnet
  RoutePropagation:
    Type: AWS::EC2::VPNGatewayRoutePropagation
    DependsOn: AttachVPNGateway
    Properties:
      RouteTableIds:
      - !Ref RouteTable
      VpnGatewayId: !Ref VPNGateway
  # EC2 Security Group
  InstanceSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      VpcId: !Ref VPC
      GroupDescription: TCP, ICMP
      SecurityGroupIngress:
      - IpProtocol: tcp
        FromPort: -1
        ToPort: -1
        CidrIp: 10.200.0.0/16
      - IpProtocol: icmp
        FromPort: -1
        ToPort: -1
        CidrIp: 10.200.0.0/16
      SecurityGroupEgress:
      - IpProtocol: '-1'
        CidrIp: 0.0.0.0/0
      Tags:
      - Key: Name
        Value: VPCA-Private-EC2
  # EC2 Instance
  EC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId:
        Fn::FindInMap: [RegionMap, Ref: AWS::Region, hvm]
      InstanceType:
        Ref: InstanceType
      KeyName:
        Ref: KeyName
      Tags:
      - Key: Name
        Value: VPCA-Private-EC2
      NetworkInterfaces:
      - GroupSet:
        - Ref: InstanceSecurityGroup
        AssociatePublicIpAddress: false
        DeviceIndex: 0
        DeleteOnTermination: true
        SubnetId: !Ref Subnet

Outputs:
  EC2Instance:
    Description: EC2 instance VPCA-Private-EC2 ID
    Value: !Ref EC2Instance
```  
  

