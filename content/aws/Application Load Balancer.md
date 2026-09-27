---
title: "Application Load Balancer"
tags:
  - aws
  - ec2
---

# Application Load Balancer

[AWS index](./index.md) · [← Auto Scaling Group](./Auto%20Scaling%20Group.md) · [EBS (Elastic Block Store) →](./EBS%20%28Elastic%20Block%20Store%29.md)

*OneNote page: Thursday, 27 August 2026 11:31 AM*

## At a glance

- The ALB gives clients one entry point and forwards requests to healthy instances in a target group.
- The walkthrough places the ALB in public subnets and application instances in private subnets across two availability zones.
- It sets security groups, health checks, the target group, the ALB and the Auto Scaling Group in that order.

## On this page

- [Why an ALB is needed](#why-an-alb-is-needed)
- [Traffic and target group flow](#traffic-and-target-group-flow)
- [Two availability zone architecture](#two-availability-zone-architecture)
- [Security groups](#security-groups)
- [Launch template](#launch-template)
- [Target group and health checks](#target-group-and-health-checks)
- [Create the ALB](#create-the-alb)
- [Connect the Auto Scaling Group](#connect-the-auto-scaling-group)
- [Verify private instances and target health](#verify-private-instances-and-target-health)
- [Listener rules](#listener-rules)

## Complete page transcription

### Why an ALB is needed

Pre-requisite:

<details><summary>Screenshot 1: accessible text</summary>

<pre>
#14 AWS 
ASG 
(Auto Scaling Group) 
Auto Scaling Group 
EC2
</pre>
</details>

EC2

- Custom AMI

- vCPU, RAM

- Security Group

etc.

Launch Template

Desired, Min, Max Capacity

Scaling Policy

AZ's   
etc.

Auto Scaling Group

EC2

EC2

EC2

Now the problems with ASG:

EC2

Custom AMI

vCPU, RAM

Security Group

etc.

Launch Template

Users

Desired, Min, Max Capacity

Scaling Policy

AZ's   
etc.

Auto Scaling Group

EC2

EC2

EC2

Multiple Instances Have Multiple Ips

If ASG launches 3 EC2 instances, each instance has a different IP.

Example:

Instance

URL

EC2 1

http://13.x.x.x:8080/

EC2 2

http://3.x.x.x:8080/

EC2 3

http://15.x.x.x:8080/

Question: Which IP should the user access?

This is not practical. Users should not know backend server IP addresses.

IP Changes During Replacement

If ASG terminates an unhealthy instance and launches a replacement, the new instance may get a new public IP.

If users were directly using the old IP, their application breaks.

No Intelligent Traffic Distribution

Without a Load Balancer, One instance may get too much traffic while another is idle.   
Also, users should not be responsible for deciding which instance receives a request.

That’s where Application Load Balancer comes into picture:

EC2

Custom AMI

vCPU, RAM

Security Group

etc.

Users

Launch Template

HTTP:80

Desired, Min, Max Capacity

Scaling Policy

AZ's   
etc.

Auto Scaling Group

App. Load Balancer

Manages (add, remove)

HTTP:8080

EC2

EC2

EC2

Also Register them with target group

Target Group

Target group is just a logical group of EC2 instances, which is used by Load Balancer to determine which healthy EC2 instance should receive requests.

Its not a server

Its AWS managed Object, which holds list of EC2 targets.

So consider it as a configuration.

EC2

EC2

EC2

Target

Target

Target

EC2   
instance

Target Group

Metadata

ALB

User

HTTP request on Port 80

Run rules, like

Path /order/*    
goes to which target group

Fetch the healthy target list from that specific target group

Forward the request to healthy EC2 instance on 8080 port

Response from SpringBoot

Response to browser

### Traffic and target group flow

Lets do the setup, but first lets visualize how our setup going to looks like

We will have 2 AZ - for better reliability (our app should not fail even if 1 AZ is down)

In Each AZ, we will have public and private subnet.

Public Subnet we will use to keep "Application Load Balancer", "NAT Gateways"

Private Subnet we will use to keep our EC2 instances.

Route tables:

Public Subnet attached to public route table (can talk to internet directly)

Private Subnet attached to private route table (no one from internet can talk to them directly but only via NAT Gateways)

Security Group:

ALB:

Inbound: can receive traffic from internet on port:80

Outbound: no restriction

EC2:

Inbound: can receive traffic only from ALB

Outbound: allow all traffic

Targe group, within VPC

Auto Scaling Group, within VPC

AWS Region

VPC

AZ-1a

Auto Scaling Group

Private Subnet-1a

private Route Table

Inbound - Security Group

Type

Protocol

Port

Source

HTTP

TCP

8080

ALB

Destination

Target

EC2

Target Group

Public Subnet-1a

Inbound - Security Group

Type

Protocol

Port

Source

HTTP

TCP

80

0.0.0.0/0

ALB

public  Route Table

outbound- Security Group

Destination

Target

Igw-0abc

Type

Destination

All traffic

Default allowed

Internet

Gateway

0.0.0.0/0

AZ-1b

private Route Table

Private Subnet-1b

Inbound - Security Group

Type

Protocol

Port

Source

HTTP

TCP

8080

ALB

Destination

Target

EC2

Public Subnet-1b

Public Route Table

Inbound - Security Group

Destination

Target

Type

Protocol

Port

Source

HTTP

TCP

80

0.0.0.0/0

ALB

Igw-0abc

0.0.0.0/0

### Two availability zone architecture

Lets Start:

EC2

Custom AMI

Step1: Setup Network (VPC, Subnet, Route table etc.)

AWS Region

VPC

AZ-1a

Private Subnet-1a

private Route Table

Destination

Target

Public Subnet-1a

public  Route Table

Destination

Target

Igw-0abc

Internet

Gateway

0.0.0.0/0

AZ-1b

private Route Table

Private Subnet-1b

Destination

Target

Public Subnet-1b

Public Route Table

Destination

Target

Igw-0abc

0.0.0.0/0

VPC -> Create VPC

<details><summary>Screenshot 2: accessible text</summary>

<pre>
VPC settings 
Resources to create Info 
Create only the VPC resource or the VPC and other networking resources. 
C 
VPC only 
VPC and more 
Name tag auto-generation Info 
Enter a value for the Name tag. This value will be used to auto-generate Name 
tags for all resources in the VPC. 
Auto-generate 
ALB-ASG-DEMO-VPC 
IPV4 CIDR block Info 
Determine the starting IP and the size of your VPC using CIDR notation. 
10.0.0.0/16 
65,536 IPs 
CIDR block size must be between /16 and /28. 
IPV6 CIDR block Info 
No IPv6 CIDR block 
Amazon-provided IPV6 CIDR block 
Tenancy Info 
Default 
Encryption settings - optional
</pre>
</details>

2AZ, we wanted

<details><summary>Screenshot 3: accessible text</summary>

<pre>
Number of Availability Zones (AZs) Info 
Choose the number of AZs in which to provision subnets. We recommend at 
least two AZs for high availability. 
1 
2 
ʒ 
Customize AZs 
Number of public subnets Info 
The number of public subnets to add to your VPC. Use public subnets for web 
applications that need to be publicly accessible over the internet. 
0 
2 
Number of private subnets Info 
The number of private subnets to add to your VPC. Use private subnets to 
secure backend resources that don&#x27;t need public access. 
0 
2 
4 
Customize subnets CIDR blocks 
NAT gateways ($) - updated Info 
NAT gateway allows private resources to access the internet from any availability 
zone within a VPC, providing a single managed internet exit point for the entire 
region. Additional charges apply. 
None 
Regional - new 
Zonal 
VPC endpoints Info 
Endpoints can help reduce NAT gateway charges and improve security by 
accessing S3 directly from the VPC. By default, full access policy is used. You can 
customize this policy at any time. 
None 
S3 Gateway 
DNS options Info 
Enable DNS hostnames 
Enable DNS resolution 
Additional tags
</pre>
</details>

2 public Subnet

2 private Subnet

No NAT Gateway, as we don’t want    
EC2 to talk to internet

<details><summary>Screenshot 4: accessible text</summary>

<pre>
VPC Show details 
Subnets (4) 
Route tables (3) 
Network connections (1) 
Your AWS virtual network 
Subnets within this VPC 
Route network traffic to resources 
Connections to other networks 
ALB-ASG-DEMO-VPC-vpc 
ap-south-1a 
ALB-ASG-DEMO-VPC-rtb-public 
ALB-ASG-DEMO-VPC-igw 
A ALB-ASG-DEMO-VPC-subnet- 
ALB-ASG-DEMO-VPC-rtb-private1-ap 
A 
ALB-ASG-DEMO-VPC-subnet- 
ALB-ASG-DEMO-VPC-rtb-private2-ap 
ap-south-1b 
B 
ALB-ASG-DEMO-VPC-subnet- 
B 
ALB-ASG-DEMO-VPC-subnet-
</pre>
</details>

### Security groups

Step2: Create Security Group for Application Load Balancer and Private EC2

You might think why we need to create Security Group first. Its because, we need security group in Launch Template.

So that when private EC2 instances will be created automatically, it will have this security group.

Goal:

ALB Security Group:

Inbound: Receive traffic from internet

Outbound: all traffic allowed

Private EC2 Security Group:

Inbound: Receive traffic only from ALB

Outbound: all traffic allowed

Create security group for ALB

VPC -> Security Groups -> Create Security Group

<details><summary>Screenshot 5: accessible text</summary>

<pre>
Basic details 
Security group name | Info 
alb-s 
Name cannot be edited after creation. 
Description |Info 
allow internet HTTP traffic to ALB 
VPC | Info 
vpc-0dcf4a218577d9bf7 (ALB-ASG-DEMO-VPC-vpc) 
Inbound rules Info 
Type Info 
Protocol Info 
Port range Info 
Source Info 
Description - optional Info 
HTTP 
TCP 
80 
Anywhe ... 
Q 0.0.0.0/0 
Delete 
0.0.0.0/0 X 
Add rule 
Outbound rules Info 
Type Info 
Protocol Info 
Port range Info 
Destination Info 
Description - optional Info 
All traffic 
ALL 
ALL 
Custom 
Q 
Delete 
0.0.0.0/0 X 
Add rule 
Tags - optional 
A tag is a label that you assign to an AWS resource. Each tag consists of a key and an optional value. You can use tags to search and filter your resources or track your AWS costs. 
No tags associated with the resource. 
Add new tag 
You can add up to 50 more tags 
Cancel 
Create security group
</pre>
</details>

Allows users to reach ALB on port 80

Create security group for private EC2

VPC -> Security Groups -> Create Security Group

<details><summary>Screenshot 6: accessible text</summary>

<pre>
Basic details 
Security group name | Info 
private-ec2-sg 
Name cannot be edited after creation. 
Description |Info 
Allow Spring Boot traffic only from ALB 
VPC | Info 
vpc-0dcf4a218577d9bf7 (ALB-ASG-DEMO-VPC-vpc) 
Inbound rules Info 
Type 
Info 
Protocol Info 
Port range 
Info 
Source Info 
Description - optional Info 
Custom TCP 
TCP 
8080 
Custom 
Q sg-0c495812192837521 
Delete 
sg-0c495812192837521 X 
Add rule 
Outbound rules Info 
Type Info 
Protocol Info 
Port range Info 
Destination Info 
Description - optional Info 
All traffic 
ALL 
ALL 
Custom 
Q 
Delete 
0.0.0.0/0 X 
Add rule 
Tags - optional 
A tag is a label that you assign to an AWS resource. Each tag consists of a key and an optional value. You can use tags to search and filter your resources or track your AWS costs. 
No tags associated with the resource. 
Add new tag 
You can add up to 50 more tags 
Cancal 
Cuanto andmonitor 
AMALIA
</pre>
</details>

ALB is allowed to connect inbound to EC2 on port 8080.

Note:

I have selected ALB Security group in source.

### Launch template

Step3: Create Launch Template

<details><summary>Screenshot 7: accessible text</summary>

<pre>
#14 AWS 
ASG 
(Auto Scaling Group) 
Auto Scaling Group 
EC2
</pre>
</details>

We already seen, how to create Launch Template in

So, I am just modifying the Launch template which I previously created.

The only modification I needed is: I need to change the security group, we will select the new security group which we created for "EC2" (so that it can only accept traffic from Application Load balancer)

<details><summary>Screenshot 8: accessible text</summary>

<pre>
Launch templates (1/1) Info 
Actions 
Create launch template 
Q Search 
Launch instance from template 
Modify template (Create new version) 
D 
D 
D 
D 
Launch template ID 
Launch template name 
Default version 
Latest version 
Create time 
Created by 
Delete template 
lt-02f2fd0c45183e916 
myapp-lt-v1 
2 
08/22/2026 12:03:20 
arn:aws:iam:643537035131:r 
Delete template version 
Set default version 
Manage tags 
Create Spot Fleet 
Create Auto Scaling group 
View details
</pre>
</details>

<details><summary>Screenshot 9: accessible text</summary>

<pre>
Launch template name and version description 
Launch template name 
myapp-lt-v1 (lt-02f2fd0c45183e916) 
Template version description 
A prod webserver for MyApp 
Max 255 chars 
Auto Scaling guidance 
Info 
Select this if you intend to use this template with EC2 Auto Scaling 
Provide guidance to help me set up a template that I can use with EC2 Auto Scaling 
Source template 
0 
1 
Launch template for sboot ASG with custom AMI 
aunch template contents 
Specify the details of your launch template version below. Leaving a field blank will result in the field not being included in the launch template version. 
Application and OS Images (Amazon Machine Image) - required Info 
An AMI contains the operating system, application server, and applications for your instance. If you don&#x27;t see a suitable AMI below, use the search field or choose Browse more AMIs. 
Q Search our full catalog including 1000s of application and OS images 
AMI from catalog 
Recents 
My AMIs 
Quick Start 
Name 
Free tier eligible 
Q 
myapp-ami-v1 
Browse more AMIs 
Description 
AMI with JDK and spring boot jar 
Including AMIs from 
AWS, Marketplace and 
Image ID 
the Community 
ami-Oeac4d748ab71acc5 
Username 
root (Check with the AMI provider.)
</pre>
</details>

<details><summary>Screenshot 10: accessible text</summary>

<pre>
Instance type Info | Get advice 
Advanced 
Instance type 
tƷ.micro 
Free tier eligible 
Family: t3 2 vCPU 1 GiB Memory Current generation: true On-Demand Linux base pricing: 0.0112 USD per Hour 
All generations 
On-Demand SUSE base pricing: 0.0112 USD per Hour On-Demand Windows base pricing: 0.0204 USD per Hour 
On-Demand Ubuntu Pro base pricing: 0.0147 USD per Hour On-Demand RHEL base pricing: 0.04 USD per Hour 
Compare instance types 
Additional costs apply for AMIs with pre-installed software 
Key pair (login) Info 
You can use a key pair to securely connect to your instance. Ensure that you have access to the selected key pair before you launch the instance. 
Key pair name 
Don&#x27;t include in launch template 
C&#x27; Create new key pair 
Network settings Info 
Subnet 
Info 
Don&#x27;t include in launch template 
C Create new subnet [^ 
When you specify a subnet, a network interface is automatically added to your template. 
Availability Zone 
Info 
Don&#x27;t include in launch template 
G 
Enable additional zones La 
Not applicable for EC2 Auto Scaling 
Firewall (security groups) 
Info 
A security group is a set of firewall rules that control the traffic for your instance. Add rules to allow specific traffic to reach your instance. 
O Select existing security group 
Create security group 
Security groups 
Info 
Select security groups 
G Compare security group rules 
private-ec2-sg sg-0709590c1f300efb7 X 
VPC: vpc-0dcf4a218577d9bf7 
&gt; Advanced network configuration
</pre>
</details>

This is what we have updated in our existing Launch Template.

Whenever we modify existing Launch template, its Latest version get updated. So always make sure we used this updated version only.

<details><summary>Screenshot 11: accessible text</summary>

<pre>
D 
D 
Launch template ID 
Launch template name 
Default version 
Latest version 
Create time 
Created by 
Managed 
Operator 
D 
D 
D 
D 
D 
lt-02f2fd0c45183e916 
myapp-lt-v1 
2 
4 
08/22/2026 12:03:20 
arn:aws:iam :: 643537035131:r ... 
false 
1 
N
</pre>
</details>

### Target group and health checks

Step4: Create Target Group

We will create Target Group first, so that we can attach it with Auto Scaling Group and Application Load Balancer.

EC2 -> Target Groups -> Create target group

<details><summary>Screenshot 12: accessible text</summary>

<pre>
Create target group 
A target group can be made up of one or more targets. Your load balancer routes requests to the targets in a target group and performs health checks on the targets. 
Settings - immutable 
Choose a target type and the load balancer and listener will route traffic to your target. These settings can&#x27;t be modified after target group creation. 
Target type 
Indicate what resource type you want to target. Only the selected resource type can be registered to this target group. 
Instances 
IP addresses 
Supports load balancing to instances in a VPC. Integrate with Auto 
Supports load balancing to VPC and on-premises resources. 
Scaling Groups or ECS services for automatic management. 
Facilitates routing to IP addresses and network interfaces on the 
Suitable for: 
ALB 
NLB 
GWLB 
same instance. Supports IPv6 targets. 
Suitable for: 
ALB 
NLB 
GWLB 
Lambda function 
Application Load Balancer 
Supports load balancing to a single Lambda function. ALB required 
Allows use of static IP addresses and PrivateLink with an 
as traffic source. 
Application Load Balancer. NLB required as traffic source. 
Suitable for: 
ALB 
Suitable for: 
NLB 
Target group name 
Name must be unique per Region per AWS account. 
alb-tg 
Accepts: a-z, A-Z, 0-9, and hyphen (-). Can&#x27;t begin or end with hyphen. 1-32 total characters; Count: 6/32 
Protocol 
Port 
Protocol for communication between the load balancer and targets. 
Port number where targets receive traffic. Can be overridden for individual 
HTTP 
targets during registration. 
8080 
1-65535
</pre>
</details>

The Target Group defines the

protocol and default port that

the Load Balancer uses when

communicating with its

targets

<details><summary>Screenshot 13: accessible text</summary>

<pre>
IP address type 
Only targets with the indicated IP address type can be registered to this target group. 
IPv4 
Each instance has a default network interface (eth0) that is assigned the primary private IPv4 address. The instance&#x27;s primary private IPv4 address is the 
one that will be applied to the target. 
IPV6 
Each instance you register must have an assigned primary IPv6 address. This is configured on the instance&#x27;s default network interface (eth0). Learn 
more 
VPC 
Select the VPC with the instances that you want to include in the target group. Only VPCs that support the IP address type selected above are available in 
this list. 
vpc-0dcf4a218577d9bf7 (ALB-ASG-DEMO-VPC-vpc) 
0 
10.0.0.0/16 
Create VPC 
Protocol version 
HTTP1 
Send requests to targets using HTTP/1.1. Supported when the request protocol is HTTP/1.1 or HTTP/2. 
HTTP2 
Send requests to targets using HTTP/2. Supported when the request protocol is HTTP/2 or gRPC, but gRPC-specific features are not available. 
gRPC 
Send requests to targets using gRPC. Supported when the request protocol is gRPC.
</pre>
</details>

In which VPC, this target

group need to be created

<details><summary>Screenshot 14: accessible text</summary>

<pre>
Health checks 
The associated load balancer periodically sends requests, per the settings below, to the registered targets to test their status. 
Health check protocol 
HTTP 
Health check path 
Use the default path of &quot;/&quot; to perform health checks on the root, or specify a custom path if preferred. 
/ 
Up to 1024 characters allowed. 
Advanced health check settings 
Restore defaults 
Health check port 
The port the load balancer uses when performing health checks on targets. By default, the health check port is the same as the target group&#x27;s traffic port. However, you can specify a different port as an override. 
O Traffic port 
Override 
Healthy threshold 
The number of consecutive health checks successes required before considering an unhealthy target healthy. 
2 
2-10 
Unhealthy threshold 
The number of consecutive health check failures required before considering a target unhealthy. 
2 
2-10 
Timeout 
The amount of time, in seconds, during which no response means a failed health check. 
5 
seconds 
2-120 
Interval 
The approximate amount of time between health checks of an individual target 
30 
seconds 
5-300 
Success codes 
The HTTP codes to use when checking for a successful response from a target. You can specify multiple values (for example, &quot;200,202&quot;) or a range of values (for example, &quot;200-299&quot;). 
200
</pre>
</details>

We can define the health endpoints   
which we have added in our app.   
like:   
/health   
/actuator/health

We don’t need to add the EC2 instances in this group manually, ASG (Auto Scaling Group) will do.

<details><summary>Screenshot 15: accessible text</summary>

<pre>
= EC2 &gt; Target groups &gt; Create target group 
Step 1 
Create target group 
Register targets - recommended 
Step 2 - recommended 
This is an optional step to create a target group. However, to ensure that your load balancer routes traffic to this target group you must register your targets. 
Register targets 
Available instances (0) 
Step 3 
Review and create 
Q Filter instances 
&lt; 1 &gt; 
Instance ID 
Name 
7 | State 
Security groups 
Zone 
Private IPv4 address 
Subnet ID 
Launch time 
D 
D 
D 
No instances 
0 selected 
Ports for the selected instances 
Ports for routing traffic to the selected instances. 
8080 
1-65535 (separate multiple ports with commas) 
Include as pending below 
Review targets 
Targets (0) 
Remove all pending 
Q Filter targets 
Show only pending 
&lt; 1 &gt; 
Instance ID 
Port 
State 
Security groups 
D 
Private IPv4 address 
Subnet ID 
D 
D 
D 
D 
D 
D 
Name 
Zone 
Launch time 
No instances added yet 
Specify instances above, or leave the group empty if you prefer to add targets later. 
0 pending 
Cancel 
Previous 
Next
</pre>
</details>

Simply review and click create.

<details><summary>Screenshot 16: accessible text</summary>

<pre>
Step 
Create target group 
Review and create 
Step 2 - recommended 
Review your target group configuration before creating 
Register targets 
Step 1: Target group details 
Edit 
Step 3 
Review and create 
Target group details 
Name 
Target type 
Protocol : Port 
Protocol version 
alb-tg 
Instance 
HTTP: 8080 
HTTP1 
VPC 
IP address type 
vpc-0dcf4a218577d9bf7 
IPv4 
Health check details 
Health check protocol 
Health check path 
Health check port 
Interval 
HTTP 
1 
traffic-port 
30 seconds 
Timeout 
Healthy threshold 
Unhealthy threshold 
Success codes 
5 seconds 
2 
200 
N 
Step 2: Register targets 
Edit 
Targets (0) 
Instance ID 
Name 
Port 
Zone 
D 
D 
D 
D 
No targets added 
Server-side tasks and status 
After completing and submitting the above steps, all server-side tasks and their statuses become available for monitoring. 
Cancel 
Previous 
Create target group
</pre>
</details>

<details><summary>Screenshot 17: accessible text</summary>

<pre>
alb-tg 
Actions 
Details 
arn:aws:elasticloadbalancing:ap-south-1:643537035131:targetgroup/alb-tg/7bfc37c4bad99f45 
Target type 
Protocol : Port 
Protocol version 
VPC 
Instance 
HTTP: 8080 
HTTP1 
vpc-Odcf4a218577d9bf7 
IP address type 
Load balancer 
IPV4 
None associated 
0 
Total targets 
Healthy 
Unhealthy 
Unused 
Initial 
Draining 
0 Anomalous 
Targets 
Monitoring 
Health checks 
Attributes 
Tags 
Registered targets (0) Info 
Anomaly mitigation: Not applicable 
Deregister 
Register targets 
Target groups route requests to individual registered targets using the protocol and port number specified. Health checks are performed on all registered targets according to the target group&#x27;s health check settings. Anomaly detection is 
automatically applied to HTTP/HTTPS target groups with at least 3 healthy targets. 
Q Filter targets 
&lt; 1 
&gt; 
D 
Instance ID 
Name 
Port 
Zone 
Health status 
Health status details 
Administrative o ... 
Override details 
Launch time 
D 
D 
D 
No registered targets 
You have not registered targets to this group yet 
Register targets
</pre>
</details>

### Create the ALB

Step5: Create Application Load Balancer in Public Subnet

AWS Region

VPC

AZ-1a

Private Subnet-1a

private Route Table

Destination

Target

Target Group

Public Subnet-1a

Inbound - Security Group

Type

Protocol

Port

Source

HTTP

TCP

80

0.0.0.0/0

ALB

public  Route Table

outbound- Security Group

Destination

Target

Igw-0abc

Type

Destination

All traffic

Default allowed

Internet

Gateway

0.0.0.0/0

AZ-1b

private Route Table

Private Subnet-1b

Destination

Target

Public Subnet-1b

Public Route Table

Inbound - Security Group

Destination

Target

Type

Protocol

Port

Source

HTTP

TCP

80

0.0.0.0/0

ALB

Igw-0abc

0.0.0.0/0

EC2 ->  Load Balancers ->  Create load balancer ->  Application Load Balancer

<details><summary>Screenshot 18: accessible text</summary>

<pre>
Basic configuration 
Load balancer name 
Name must be unique within your AWS account and can&#x27;t be changed after the load balancer is created. 
alb-demo 
A maximum of 32 alphanumeric characters including hyphens are allowed, but the name must not begin or end with a hyphen. 
Scheme |Info 
Scheme can&#x27;t be changed after the load balancer is created. 
Internet-facing 
Internal 
· Serves internet-facing traffic. 
· Serves internal traffic. 
· Has public IP addresses. 
· Has private IP addresses. 
. DNS name resolves to public IPs. 
· DNS name resolves to private IPs. 
· Requires a public subnet. 
. Compatible with the IPV4 and Dualstack IP address types. 
Load balancer IP address type | Info 
Select the front-end IP address type to assign to the load balancer. The VPC and subnets mapped to this load balancer must include the selected IP address types. Public IPv4 addresses have an additional cost. 
IPv4 
Includes only IPv4 addresses. 
Dualstack 
Includes IPv4 and IPV6 addresses. 
Dualstack without public IPv4 
Includes a public IPv6 address, and private IPv4 and IPV6 addresses. Compatible with internet-facing load balancers only. 
Network mapping Info 
The load balancer routes traffic to targets in the selected subnets, and in accordance with your IP address settings. 
VPC | Info 
The load balancer will exist and scale within the selected VPC. The selected VPC is also where the load balancer targets must be hosted unless routing to Lambda or on-premises targets, or if 
using VPC peering. To confirm the VPC for your targets, view target groups 12. 
vpc-0dcf4a218577d9bf7 (ALB-ASG-DEMO-VPC-vpc) 
10.0.0.0/16 
Create VPC LA
</pre>
</details>

Select the VPC in which we need to

create this ALB

<details><summary>Screenshot 19: accessible text</summary>

<pre>
Availability Zones and subnets | Info 
Select at least two Availability Zones and a subnet for each zone. A load balancer node will be placed in each selected zone and will automatically scale in response to traffic. The load balancer routes traffic to targets in the selected Availability Zones only. 
ap-south-1a (aps1-az1) 
Subnet 
configured to allow traffic. 
Only CIDR blocks corresponding to the load balancer IP address type are used. At least 8 available IP addresses are required for your load balancer to scale efficiently. Selected subnets must have Network ACL inbound and outbound ALLOW rules, and internet gateway (IGW) routes 
subnet-04177b4886fe4825d (ALB-ASG-DEMO-VPC-subnet-public1-ap-south-1a) 
10.0.0.0/20 
Network ACL ALLOW rules: Inbound (1) Outbound (1) | Route table: IGW (v) 
ap-south-1b (aps1-az3) 
Subnet 
Only CIDR blocks corresponding to the load balancer IP address type are used. At least 8 available IP addresses are required for your load balancer to scale efficiently. Selected subnets must have Network ACL inbound and outbound ALLOW rules, and internet gateway (IGW) routes 
configured to allow traffic. 
subnet-05fd9a82992ef5255 (ALB-ASG-DEMO-VPC-subnet-public2-ap-south-1b) 
10.0.16.0/20 
Network ACL ALLOW rules: Inbound (1) Outbound (1) 
Route table: IGW (v) 
Security groups Info 
A security group is a set of firewall rules that control the traffic to your load balancer. Listed security groups are in the same VPC as you selected above. Selected security groups must include at least 1 inbound rule and 1 outbound rule to allow traffic 
to your load balancer. 
Security groups 
Select up to 5 security groups 
Create security group 
alb-sg (sg-0c495812192837521) X 
Inbound rule (1) Outbound rule (1)
</pre>
</details>

2 AZ - public subnet,  for better reliability

Select the security group which we created for ALB

<details><summary>Screenshot 20: accessible text</summary>

<pre>
Listeners and routing Info 
A listener is a process that checks for connection requests using the port and protocol you configure. The rules that you define for a listener determine how the load balancer routes requests to its registered targets. 
Listener HTTP:80 
Remove 
Protocol 
Port 
HTTP 
80 
1-65535 
Default action | Info 
The default action is used if no other rules apply. Choose the default action for traffic on this listener. 
Routing action 
O Forward to target groups 
Redirect to URL 
Return fixed response 
Forward to target group | Info 
Choose a target group and specify routing weight or create target group La. 
Target group 
Weight 
Percent 
alb-tg 
HTTP 
1 
100% 
Target type: Instance, IPv4 | Target stickiness: Off 
0-999 
+ Add target group 
You can add up to 4 more target groups. 
Target group stickiness | Info 
Enables the load balancer to bind a user&#x27;s session to a specific target group. To use stickiness the client must support cookies. If you want to bind a user&#x27;s session to a specific target, turn on the Target Group attribute Stickiness. 
Turn on target group stickiness 
Listener tags - optional 
Consider adding tags to your listener. Tags enable you to categorize your AWS resources so you can more easily manage them.
</pre>
</details>

Attach this ALB with target group

### Connect the Auto Scaling Group

Step6: Create Auto Scaling Group in Private Subnet

EC2 -> Auto Scaling Groups -> Create Auto Scaling group

<details><summary>Screenshot 21: accessible text</summary>

<pre>
Name 
Auto Scaling group name 
Enter a name to identify the group. 
asg-demo 
Must be unique to this account in the current Region and no more than 255 characters. 
Launch template Info 
For accounts created after May 31, 2023, the EC2 console only supports creating Auto Scaling groups with launch templates. Creating Auto Scaling groups with launch configurations is 
not recommended but still available via the CLI and API until December 31, 2023. 
Launch template 
Choose a launch template that contains the instance-level settings, such as the Amazon Machine Image (AMI), instance type, key pair, and security groups. 
myapp-lt-v1 
Create a launch template La 
Version 
4 
Create a launch template version La 
Description 
Launch template 
Instance type 
myapp-lt-v1 L 
tƷ.micro 
It-02f2fd0c45183e916 
AMI ID 
Security groups 
Request Spot Instances 
ami-Oeac4d748ab71acc5 
No 
Key pair name 
Security group IDs 
sg-0709590c1f300efb7 L 
Additional details 
Storage (volumes) 
Date created 
Fri Aug 28 2026 00:38:27 GMT+0530 (India Standard
</pre>
</details>

Select the Launch template and double validate the version

<details><summary>Screenshot 22: accessible text</summary>

<pre>
Network Info 
For most applications, you can use multiple Availability Zones and let EC2 Auto Scaling balance your instances across the zones. The default VPC and default subnets are suitable for getting started 
quickly. 
VPC 
Choose the VPC that defines the virtual network for your Auto Scaling group. 
vpc-0dcf4a218577d9bf7 (ALB-ASG-DEMO-VPC-vpc) 
10.0.0.0/16 
Create a VPC La 
Availability Zones and subnets 
Define which Availability Zones and subnets your Auto Scaling group can use in the chosen VPC. 
Select Availability Zones and subnets 
aps1-az1 (ap-south-1a) | subnet-090398aa4dfcf020e (ALB-ASG-DEMO-VPC-subnet- 
× 
private1-ap-south-1a) 
10.0.128.0/20 
aps1-az3 (ap-south-1b) | subnet-068dff7d5c16373db (ALB-ASG-DEMO-VPC-subnet- 
X 
private2-ap-south-1b) 
10.0.144.0/20 
Create a subnet La 
Availability Zone distribution - new 
Auto Scaling automatically balances instances across Availability Zones. If launch failures occur in a zone, select a strategy. 
Balanced best effort 
Balanced only 
Reservations Then Balanced 
If launches fail in one Availability Zone, Auto Scaling will attempt 
If launches fail in one Availability Zone, Auto Scaling will continue 
Prioritizes launching into Capacity Reservations, distributing 
to launch in another healthy Availability Zone. 
to attempt to launch in the unhealthy Availability Zone to 
across AZs with available reservations. When reservations are 
preserve balanced distribution. 
fully utilized, falls back to balanced on-demand.
</pre>
</details>

Select VPC and 2 private subnets.   
ASG will distributed the EC2

instances in these subnets.

<details><summary>Screenshot 23: accessible text</summary>

<pre>
Load balancing Info 
Use the options below to attach your Auto Scaling group to an existing load balancer, or to a new load balancer that you define. 
Select Load balancing options 
No load balancer 
Attach to an existing load balancer 
Attach to a new load balancer 
Traffic to your Auto Scaling group will not be fronted by a load 
Choose from your existing load balancers. 
Quickly create a basic load balancer to attach to your Auto 
balancer. 
Scaling group. 
Attach to an existing load balancer 
Select the load balancers to attach 
Choose from your load balancer target groups 
Choose from Classic Load Balancers 
This option allows you to attach Application, Network, or Gateway Load Balancers. 
Existing load balancer target groups 
Only instance target groups that belong to the same VPC as your Auto Scaling group are available for selection. 
Select target groups 
alb-tg | HTTP 
X 
Application Load Balancer: alb-demo
</pre>
</details>

Attach the ASG with target group

<details><summary>Screenshot 24: accessible text</summary>

<pre>
Health checks 
Health checks increase availability by replacing unhealthy instances. When you use multiple health checks, all are evaluated, and if at least one fails, instance replacement occurs. 
EC2 health checks 
Always enabled 
Additional health check types - optional | 
Info 
Turn on Elastic Load Balancing health checks 
Recommended 
Elastic Load Balancing monitors whether instances are available to handle requests. When it reports an unhealthy instance, EC2 Auto Scaling can replace it on its next periodic check. 
EC2 Auto Scaling will start to detect and act on health checks performed by Elastic Load Balancing. To avoid unexpected terminations, first verify the settings of these health checks 
× 
in the Load Balancer console La 
Turn on VPC Lattice health checks 
VPC Lattice can monitor whether instances are available to handle requests. If it considers a target as failed a health check, EC2 Auto Scaling replaces it after its next periodic check. 
Turn on Amazon EBS health checks 
EBS monitors whether an instance&#x27;s root volume or attached volume stalls. When it reports an unhealthy volume, EC2 Auto Scaling can replace the instance on its next periodic health check. 
Health check grace period |Info 
This time period delays the first health check until your instances finish initializing. It doesn&#x27;t prevent an instance from terminating when placed into a non-running state. 
300 
seconds
</pre>
</details>

ASG will also consider the health status reported by the Load Balancer for targets in the Target Group.

<details><summary>Screenshot 25: accessible text</summary>

<pre>
Group size Info 
Set the initial size of the Auto Scaling group. After creating the group, you can change its size to meet demand, either manually or by using automatic scaling. 
Desired capacity type 
Choose the unit of measurement for the desired capacity value. vCPUs and Memory(GiB) are only supported for mixed instances groups configured with a set of instance attributes. 
Units (number of instances) 
Desired capacity 
Specify your group size. 
2 
Scaling Info 
You can resize your Auto Scaling group manually or automatically to meet changes in demand. 
Scaling limits 
Set limits on how much your desired capacity can be increased or decreased. 
Min desired capacity 
Max desired capacity 
2 
4 
Equal or less than desired capacity 
Equal or greater than desired capacity 
Automatic scaling - optional 
Choose whether to use a target tracking policy | Info 
You can set up other metric-based scaling policies and scheduled scaling after creating your Auto Scaling group. 
No scaling policies 
Target tracking scaling policy 
Your Auto Scaling group will remain at its initial size and will not dynamically resize to meet demand. 
Choose a CloudWatch metric and target value and let the scaling policy adjust the desired capacity in 
proportion to the metric&#x27;s value. 
Scaling policy name 
Target Tracking Policy 
Metric type | Info 
Monitored metric that determines if resource utilization is too low or high. If using EC2 metrics, consider enabling detailed monitoring for better scaling performance. 
Average CPU utilization 
Target value 
50 
Instance warmup 
Info 
300 
seconds
</pre>
</details>

<details><summary>Screenshot 26: accessible text</summary>

<pre>
Add tags - optional Info 
Add tags to help you search, filter, and track your Auto Scaling group across AWS. You can also choose to automatically add these tags to instances when they are launched. 
You can optionally choose to add tags to instances (and their attached EBS volumes) by specifying tags in your launch template. We recommend caution, however, because the tag values 
X 
for instances from your launch template will be overridden if there are any duplicate keys specified for the Auto Scaling group. 
Tags (1) 
Key 
Value - optional 
Tag new instances 
Name 
private-ec2-instance 
Remove 
Add tag 
49 remaining 
Cancel 
Previous 
Next
</pre>
</details>

AWS Region

VPC

AZ-1a

Auto Scaling Group

Private Subnet-1a

private Route Table

Inbound - Security Group

Type

Protocol

Port

Source

HTTP

TCP

8080

ALB

Destination

Target

EC2

Target Group

Public Subnet-1a

Inbound - Security Group

Type

Protocol

Port

Source

HTTP

TCP

80

0.0.0.0/0

ALB

public  Route Table

outbound- Security Group

Destination

Target

Igw-0abc

Type

Destination

All traffic

Default allowed

Internet

Gateway

0.0.0.0/0

AZ-1b

private Route Table

Private Subnet-1b

Inbound - Security Group

Type

Protocol

Port

Source

HTTP

TCP

8080

ALB

Destination

Target

EC2

Public Subnet-1b

Public Route Table

Inbound - Security Group

Destination

Target

Type

Protocol

Port

Source

HTTP

TCP

80

0.0.0.0/0

ALB

Igw-0abc

0.0.0.0/0

### Verify private instances and target health

Step 7: Verify Instances Are Private

<details><summary>Screenshot 27: accessible text</summary>

<pre>
Instances (1/3) Info 
Connect La 
Launch instances 
0 
Instance state V 
Actions V 
Saved filter sets 
V 
Choose filter setv 
Q Find Instance by attribute or tag (case-sensitive) 
&gt; 
D 
D 
D 
Name 
Instance ID 
Instance state 
D 
7 
Instance type 
Status check 
Availability Zone 
Public IPv4 DNS 
Public IPv4 ... 
Elastic IP 
IPV6 IPs 
Monitoring 
Security 
D 
D 
D 
D 
private-ec2-in ... 
i-Ob42ed9536b165968 
Running Q Q 
t3.micro 
3/3 checks passec ap-south-1b 
disabled 
private-e 
my-ec2 
i-042ea3e9d1047621a 
Running QQ 
t3.micro 
3/3 checks passec 
ap-south-1a 
ec2-13-206-221-46.ap -... 
13.206.221.46 
disabled 
launch-W 
private-ec2-in ... 
i-02e22cd0bc1611f7f 
Running Q Q 
t3.micro 
3/3 checks passec ap-south-1a 
disabled 
private-e 
1 
1
</pre>
</details>

We can check one of the ec2 instanced created by ASG -> its within Private subnet only

<details><summary>Screenshot 28: accessible text</summary>

<pre>
Instance summary for i-0b42ed9536b165968 (private-ec2-instance) Info 
Connect La 
Instance stateV 
Actions 
Updated less than a minute ago 
Instance ID 
Public IPv4 address 
Private IPv4 addresses 
i-Ob42ed9536b165968 
10.0.153.224 
IPv6 address 
Instance state 
Public DNS 
Running 
Hostname type 
Private IP DNS name (IPv4 only) 
IP name: ip-10-0-153-224.ap-south-1.compute.internal 
ip-10-0-153-224.ap-south-1.compute.internal 
Answer private resource DNS name 
Instance type 
Elastic IP addresses 
tƷ.micro 
Auto-assigned IP address 
VPC ID 
AWS Compute Optimizer finding 
vpc-0dcf4a218577d9bf7 (ALB-ASG-DEMO-VPC-vpc) La 
Opt-in to AWS Compute Optimizer for recommendations. | Learn more La 
IAM role 
Subnet ID 
Auto Scaling Group name 
subnet-068dff7d5c16373db (ALB-ASG-DEMO-VPC-subnet-private2-ap- 
asg-demo 
south-1b) 
IMDSv2 
Instance ARN 
Managed 
Required 
arn:aws:ec2:ap-south-1:643537035131:instance/i-0b42ed9536b165968 
false 
Operator
</pre>
</details>

Step 8: Check target health

EC2 -> Target Groups -> alb-tg -> Targets

<details><summary>Screenshot 29: accessible text</summary>

<pre>
alb-tg 
Actions 
Details 
[ arn:aws:elasticloadbalancing:ap-south-1:643537035131:targetgroup/alb-tg/7bfc37c4bad99f45 
Target type 
Protocol : Port 
Protocol version 
VPC 
Instance 
HTTP: 8080 
HTTP1 
vpc-0dcf4a218577d9bf7L 
IP address type 
Load balancer 
IPV4 
alb-demo La 
2 
0 2 
- 0 
0 0 
0 0 
Total targets 
Healthy 
Unhealthy 
Unused 
Initial 
Draining 
0 Anomalous 
&gt; Distribution of targets by Availability Zone (AZ) 
Select values in this table to see corresponding filters applied to the Registered targets table below. 
Targets 
Monitoring 
Health checks 
Attributes 
Tags 
Registered targets (2) Info 
Anomaly mitigation: Not applicable 
Deregister 
Register targets 
Target groups route requests to individual registered targets using the protocol and port number specified. Health checks are performed on all registered targets according to the target group&#x27;s health check settings. Anomaly detection is 
automatically applied to HTTP/HTTPS target groups with at least 3 healthy targets. 
Q Filter targets 
&lt; 1 &gt; 
Instance ID 
Name 
Port 
Zone 
Health status 
Health status details 
Administrative o ... 
Override details 
Launch ... 
Anomaly d 
D 
D 
D 
D 
D 
i-02e22cd0bc1611f7f 
private-ec2-ins ... 
8080 
ap-south-1a (a ... 
Healthy 
No override 
No override is curren ... 
August 28 ... 
Normal 
i-Ob42ed9536b165968 
private-ec2-ins ... 
8080 
ap-south-1b (a .. 
Healthy 
- 
No override 
No override is curren ... 
August 28 ... 
Normal
</pre>
</details>

Step 9: User connects to Application Load balancer DNS

EC2 -> Load Balancers -> alb-demo, Copy DNS name.

Open in browser:

<details><summary>Screenshot 30: accessible text</summary>

<pre>
A Not Secure 
alb-demo-1313453882.ap-south-1.elb.amazonaws.com 
Hi all - from shrayansh
</pre>
</details>

Last but not least:

### Listener rules

In Application Load balancer we can add multiple listener rules.

Like

Path based:   
/users/* ----forward to----> Target Group1   
/payment/* ----forward to ----> Target Group2   
   
Host based:   
admin.example.com --forward to ----> Target Group1   
api.example.com ---forward to -----> Target Group2   
   
etc.

<details><summary>Screenshot 31: accessible text</summary>

<pre>
alb-demo 
Actions 
Details 
Load balancer type 
Status 
VPC 
Load balancer IP address type 
Application 
Active 
vpc-0dcf4a218577d9bf7 L 
IPv4 
Scheme 
Hosted zone 
Availability Zones 
Date created 
Internet-facing 
ZP97RAFLXTNZK 
subnet-05fd9a82992ef5255 L ap-south-1b (aps1-az3) 
August 28, 2026, 01:22 (UTC+05:30) 
subnet-04177b4886fe4825d La ap-south-1a (aps1-az1) 
Load balancer ARN 
DNS name Info 
arn:aws:elasticloadbalancing:ap-south-1:643537035131:loadbalancer/app/alb-demo/85a46576a1b038f4 
alb-demo-1313453882.ap-south-1.elb.amazonaws.com (A Record) 
Listeners and rules 
Network mapping 
Resource map 
Security 
Monitoring 
Integrations 
Attributes 
Capacity 
Tags 
Listeners and rules (1) Info 
Manage rules V 
Manage listener 
Add listener 
A listener checks for connection requests on its configured protocol and port. Incoming requests are evaluated against rules in priority order; the first matching rule determines routing. If no rule matches, the default action is applied. 
V 
Q Filter listeners 
1 &gt; 
D 
D 
Protocol:Port 
Default action 
Rule 
V 
ARN 
Security policy 
Default SSL/TLS certificate 
mTLS 
Trust store 
D 
D 
· Forward to target group 
HTTP:80 
alb-tg LZ: 1 (100%) 
1 rule 
ARN 
Not applicable 
Not applicable 
Not applicable 
Not applica 
Target group stickiness: Off
</pre>
</details>

<details><summary>Screenshot 32: accessible text</summary>

<pre>
HTTP:80 Info 
Actions V 
Details 
A listener checks for connection requests on its configured protocol and port. Incoming requests are evaluated against rules in priority order; the first matching rule determines routing. If no rule matches, the default action is applied. 
Protocol:Port 
Load balancer 
Default actions 
HTTP:80 
alb-demo 
· Forward to target group 
alb-tg L7: 1 (100%) 
Target group stickiness: Off 
Listener ARN 
arn:aws:elasticloadbalancing:ap-south-1:643537035131:listener/app/alb-demo/85a46576a1b038f4/e27ea02caab30e5e 
Rules 
Attributes 
Tags 
Listener rules (1) Info 
Rule limits 
Actions 
Add rule 
Traffic received by the listener is routed according to the default action and any additional rules. Rules are evaluated in priority order from the lowest value to the highest value. 
Q Filter rules 
Priority 
Name tag 
Conditions (If) 
Transforms 
Actions (Then) 
ARN 
Actions 
Last 
Default 
If no other rule applies 
· Forward to target group 
ARN 
(default) 
alb-tg La: 1 (100%) 
0 
Target group stickiness: Off
</pre>
</details>

<details><summary>Screenshot 33: accessible text</summary>

<pre>
Add rule Info 
Requests that reach this rule are evaluated against its conditions. If a request matches all of the rule conditions, then a secondary evaluation is done to perform any specified transforms, and finally the request is 
routed according to the rule&#x27;s actions. 
Listener details: HTTP:80 
Name and tags Info 
Tags can help you manage, identify, organize, search for and filter resources. 
Conditions (0 values) Info 
Rule limits 
Define 1-5 condition values. Additional conditions can&#x27;t be added once the limit is reached. 
Add condition 
Host header 
ues for this rule. 
Path 
Query string 
Il (O) Info 
HTTP request method 
t headers and URLs before routing. 
HTTP header 
Source IP 
rms. 
Actions Info 
Requests matching all rule conditions route according to the rule actions. 
Routing action 
Forward to target groups 
Redirect to URL 
Return fixed response 
Forward to target group | Info 
Choose a target group and specify routing weight or create target group La. 
Target group 
Weight 
Percent 
Select a target group 
1 
100% 
0-999
</pre>
</details>

<details><summary>Screenshot 34: accessible text</summary>

<pre>
Conditions (1 value) Info 
Rule limits 
Define 1-5 condition values. Additional conditions can&#x27;t be added once the limit is reached. 
Path (value) = /users/* 
Remove 
Match pattern type 
Value matching 
Regex matching 
Match with glob syntax, using * and ? as wildcards. 
Match with regex syntax. 
Path condition value 
Case sensitive. 
= 
/users/* 
Valid characters are a-z, A-Z, 0-9 and special characters. Path must be 1-128 characters. 
+ Add OR condition value
</pre>
</details>

<details><summary>Screenshot 35: accessible text</summary>

<pre>
Actions Info 
Requests matching all rule conditions route according to the rule actions. 
Routing action 
Forward to target groups 
Redirect to URL 
Return fixed response 
Forward to target group | Info 
Choose a target group and specify routing weight or create target group La. 
Target group 
Weight 
Percent 
0 
alb-tg 
HTTP 
1 
100% 
Target type: Instance, IPv4 | Target stickiness: Off 
0-999 
+ Add target group 
You can add up to 4 more target groups. 
Target group stickiness | Info 
Enables the load balancer to bind a user&#x27;s session to a specific target group. To use stickiness the client must support cookies. If you want to bind a user&#x27;s session to a specific target, turn on the Target Group attribute Stickiness. 
Turn on target group stickiness
</pre>
</details>

[AWS index](./index.md) · [← Auto Scaling Group](./Auto%20Scaling%20Group.md) · [EBS (Elastic Block Store) →](./EBS%20%28Elastic%20Block%20Store%29.md)

*Screenshots are represented by their OneNote accessible text. The original bitmap images are not embedded; image text may contain recognition errors.*
