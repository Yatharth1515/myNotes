---
title: "Auto Scaling Group"
tags:
  - aws
  - ec2
---

# Auto Scaling Group

[AWS index](./index.md) · [← EC2](./EC2.md) · [Application Load Balancer →](./Application%20Load%20Balancer.md)

*OneNote page: Saturday, 22 August 2026 10:29 AM*

## At a glance

- An AMI captures the configured instance; a launch template defines how to start replacements.
- The Auto Scaling Group uses desired, minimum and maximum capacity to manage instances.

## On this page

- [From EC2 to Auto Scaling](#from-ec2-to-auto-scaling)
- [Create an AMI](#create-an-ami)
- [Launch template](#launch-template)
- [Auto Scaling Group](#auto-scaling-group)
- [Verify the instances](#verify-the-instances)

## Complete page transcription

### From EC2 to Auto Scaling

Pre-requisite:

<details><summary>Screenshot 1: accessible text</summary>

<pre>
#12 AWS 
EC2 
(Elastic Compute Cloud) 
Physical Server 
CPU 
RAM 
SSD 
AMI 
&gt; 
Hypervisor (AWS Nitro) 
&gt; 
VCPU 
EC2-1 
EC2-2 
EC2-3 
Ubuntu 
Windows 
Amazon Linux 
2 vCPU 
4 vCPU 
8 vCPU 
Key Pair 
4 GB RAM 
8 GB RAM 
16 GB RAM 
20 GB Disk 
50 GB Disk 
100 GB Disk
</pre>
</details>

<details><summary>Screenshot 2: accessible text</summary>

<pre>
Launch an instance Info 
Amazon EC2 allows you to create virtual machines, or instances, that run on the AWS Cloud. Quickly get started by following the simple steps below. 
Name and tags Info 
Name 
myec2 
Add additional tags 
Application and OS Images (Amazon Machine Image) Info 
An AMI contains the operating system, application server, and applications for your instance. If you don&#x27;t see a suitable AMI below, use the search field or choose 
Browse more AMIs. 
Q Search our full catalog including 1000s of application and OS images 
Recents 
Quick Start 
Amazon 
macOS 
Ubuntu 
Windows 
Red Hat 
SUSE Linux 
Debian 
Q 
Linux 
Browse more AMIs 
aws 
Canonical 
Mac 
Ubuntu 
Microsoft 
Including AMIs from 
Red Hat 
SUSE 
debian 
AWS, Marketplace and 
the Community 
Amazon Machine Image (AMI) 
Amazon Linux 2023 kernel-6.18 AMI 
ami-Ob910d1016287a5e7 (64-bit (x86), uefi-preferred) / ami-00ddc0cd6fdb761eb (64-bit (Arm), uefi) 
Virtualization: hvm ENA enabled: true Root device type: ebs
</pre>
</details>

<details><summary>Screenshot 3: accessible text</summary>

<pre>
Instance type Info | Get advice 
Instance type 
t3.micro 
Free tier eligible 
All generations 
Family: t3 2 vCPU 1 GiB Memory Current generation: true On-Demand Linux base pricing: 0.0112 USD per Hour 
On-Demand SUSE base pricing: 0.0112 USD per Hour On-Demand Windows base pricing: 0.0204 USD per Hour 
On-Demand Ubuntu Pro base pricing: 0.0147 USD per Hour On-Demand RHEL base pricing: 0.04 USD per Hour 
Compare instance types 
Additional costs apply for AMIs with pre-installed software 
t3.micro
</pre>
</details>

<details><summary>Screenshot 4: accessible text</summary>

<pre>
Create key pair 
X 
Key pair name 
Key pairs allow you to connect to your instance securely. 
my-ec2-key 
The name can include up to 255 ASCII characters. It can&#x27;t include leading or trailing spaces. 
Key pair type 
RSA 
ED25519 
RSA encrypted private and public key 
ED25519 encrypted private and public 
pair 
key pair 
Private key file format 
.pem 
For use with OpenSSH 
.ppk 
For use with PuTTY 
A When prompted, store the private key in a secure and accessible location on 
your computer. You will need it later to connect to your instance. Learn 
more 
Cancel 
Create key pair
</pre>
</details>

<details><summary>Screenshot 5: accessible text</summary>

<pre>
Network settings Info 
Edit 
Network 
Info 
vpc-06a42df24a27c3d21 
Subnet 
Info 
No preference (Default subnet in any availability zone) 
Auto-assign public IP 
Info 
Enable 
Firewall (security groups) 
Info 
A security group is a set of firewall rules that control the traffic for your instance. Add rules to allow specific traffic to reach your instance. 
Create security group 
Select existing security group 
We&#x27;ll create a new security group called &#x27;launch-wizard-2&#x27; with the following rules: 
Allow SSH traffic from 
Helps you connect to your instance 
Anywhere 
0.0.0.0/0 
Allow HTTPS traffic from the internet 
To set up an endpoint, for example when creating a web server 
Allow HTTP traffic from the internet 
To set up an endpoint, for example when creating a web server 
A Rules with source of 0.0.0.0/0 allow all IP addresses to access your instance. We recommend setting security group rules to allow access from 
X 
known IP addresses only.
</pre>
</details>

<details><summary>Screenshot 6: accessible text</summary>

<pre>
Configure storage Info 
Advanced 
1x 
8 
GiB 
gpƷ 
Root volume, 3000 IOPS, Not encrypted 
Add new volume 
Click refresh to view backup information 
The tags that you assign determine whether the instance will be backed up by any Data Lifecycle Manager policies. 
File systems 
S3 Files - new 
EFS 
FSX 
None
</pre>
</details>

### Create an AMI

<details><summary>Screenshot 7: accessible text</summary>

<pre>
EC2 &gt; Instances 
EC2 
‹ 
Instances (1/1) Info 
Last updated 
less than a minute ago 
Connect 
Instance state 
Actions 
Launch instances 
Saved filter sets 
Dashboard 
Q Filter actions 
Running 
Q Find Instance by attribute or tag (case-sensitive) 
AWS Global View [ 
Instance diagnostics 
Instance state = running 
× 
Clear filters 
Events 
Instance settings 
1 
&gt; 
· Instances 
Networking 
Name 
Instance ID 
Instance state 
Instance type 
Status check 
Availability Zone 
Public IP 
Instances 
Security 
my-ec2 
i-042ea3e9d1047621a 
Running Q Q 
t3.micro 
3/3 checks passec 
ap-south-1a 
13.206.2 
Instance Types 
Image and templates 
Launch Templates 
Storage 
Spot Requests 
Monitor and troubleshoot 
Savings Plans
</pre>
</details>

<details><summary>Screenshot 8: accessible text</summary>

<pre>
Instances (1/1) Info 
Last updated 
Launch instances 
D 
2 minutes ago 
Connect 
Instance state 
Actions 
Saved filter sets 
Running 
Q Find Instance by attribute or tag (case-sensitive) 
Q Filter actions 
Instance diagnostics 
Instance state = running 
X 
Clear filters 
Instance settings 
&gt; 
Networking 
D 
Name 
Instance ID 
Instance state 
Instance type 
Statw 
Public IP 
D 
Create image 
Security 
my-ec2 
i-042ea3e9d1047621a 
Running Q 
tƷ.micro 
) 3 
13.206.2 
Create template from instance 
Image and templates 
Launch more like this 
Storage 
Monitor and troubleshoot
</pre>
</details>

<details><summary>Screenshot 9: accessible text</summary>

<pre>
Image details 
Instance ID 
i-042ea3e9d1047621a (my-ec2) 
Image name 
myapp-ami-v1 
Maximum 127 characters. Can&#x27;t be modified after creation. 
Image description - optional 
AMI with JDK and Spring Boot application configured as service 
Maximum 255 characters 
Reboot instance 
When selected, Amazon EC2 reboots the instance so that data is at rest when snapshots of the attached volumes are taken. This ensures data consistency. 
Instance volumes 
Storage type 
Device 
Snapshot 
Size 
Volume type 
IOPS 
Throughput 
Delete on 
Encrypted 
termination 
EBS 
/dev/xv ... 
Create new snapshot from v ... 
8 
EBS General Purpose SSD - g ... V 
300 
Enable 
Enable 
Add volume 
During the image creation process, Amazon EC2 creates a snapshot of each of the above volumes. 
Tags - optional 
A tag is a label that you assign to an AWS resource. Each tag consists of a key and an optional value. You can use tags to search and filter your resources or track your AWS costs. 
O Tag image and snapshots together 
O 
Tag image and snapshots separately 
Tag the image and the snapshots with the same tag. 
Tag the image and the snapshots with different tags. 
No tags associated with the resource. 
Add new tag 
You can add up to 50 more tags. 
Cancel 
Create image
</pre>
</details>

<details><summary>Screenshot 10: accessible text</summary>

<pre>
EC2 &gt; AMIs &gt; ami-06830491f681b9b55 
V 
EC2 
Image summary for ami-06830491f681b9b55 
La EC2 Image Builder 
Actions 
Launch instance from AMI 
Dashboard 
Image details 
AWS Global View La 
AMI ID 
Image type 
Platform details 
Root device type 
Events 
ami-06830491f681b9b55 
machine 
Linux/UNIX 
EBS 
Instances 
AMI name 
Owner account ID 
Architecture 
Usage operation 
Instances 
myapp-ami-v1 
643537035131 
x86_64 
RunInstances 
Instance Types 
Root device name 
Status 
Source 
Virtualization type 
Launch Templates 
/dev/xvda 
Pending 
643537035131/myapp-ami-v1 
hvm 
Spot Requests 
Savings Plans 
Boot mode 
State reason 
Creation date 
Kernel ID 
uefi-preferred 
08/22/2026 06:40:41 
Reserved Instances 
Dedicated Hosts 
Description 
Product codes 
RAM disk ID 
Deprecation time 
Capacity Reservations 
AMI with JDK and Spring Boot application 
configured as service 
Capacity Manager New 
Last launched time 
Block devices 
Deregistration protection 
Allowed image 
/ Images 
/dev/xvda=snap-0d8f912520456bdc8:8:t 
Disabled 
AMIS 
rue:gp3 
AMI Catalog 
Source AMI ID 
Source AMI Region 
Public SSM parameter name 
Elastic Block Store 
ami-Ob910d1016287a5e7 
ap-south-1
</pre>
</details>

### Launch template

<details><summary>Screenshot 11: accessible text</summary>

<pre>
Create launch template 
Creating a launch template allows you to create a saved instance configuration that can be reused, shared and launched at a later time. Templates can have multiple versions. 
Launch template name and description 
Launch template name - required 
myapp-lt-v1 
Must be unique to this account. Max 128 chars. No spaces or special characters like &#x27;&amp;&#x27;, &#x27;*&#x27;, &#x27;@&#x27;. 
Template version description 
Launch Template for spring boot ASG using custom AMI 
Max 255 chars 
Auto Scaling guidance | Info 
Select this if you intend to use this template with EC2 Auto Scaling 
Provide guidance to help me set up a template that I can use with EC2 Auto Scaling 
Template tags 
Source template 
Launch template contents 
Specify the details of your launch template below. Leaving a field blank will result in the field not being included in the launch template. 
Application and OS Images (Amazon Machine Image) - required Info 
An AMI contains the operating system, application server, and applications for your instance. If you don&#x27;t see a suitable AMI below, use the search field or choose Browse more AMIs. 
Q Search our full catalog including 1000s of application and OS images 
Recents 
My AMIs 
Quick Start 
Owned by me 
Shared with me 
Q 
Browse more AMIs 
Including AMIs from 
AWS, Marketplace and 
the Community 
Amazon Machine Image (AMI) 
myapp-ami-v1 
ami-06830491f681b9b55 
08/22/2026 06:40:41 Virtualization: hvm 
ENA enabled: true 
Root device type: ebs Boot mode: uefi-preferred
</pre>
</details>

<details><summary>Screenshot 12: accessible text</summary>

<pre>
Instance type Info | Get advice 
Advanced 
Instance type 
t3.micro 
Free tier eligible 
Family: t3 2 vCPU 1 GiB Memory Current generation: true On-Demand Linux base pricing: 0.0112 USD per Hour 
All generations 
On-Demand SUSE base pricing: 0.0112 USD per Hour On-Demand Windows base pricing: 0.0204 USD per Hour 
On-Demand Ubuntu Pro base pricing: 0.0147 USD per Hour On-Demand RHEL base pricing: 0.04 USD per Hour 
Compare instance types 
Additional costs apply for AMIs with pre-installed software 
Key pair (login) 
Info 
You can use a key pair to securely connect to your instance. Ensure that you have access to the selected key pair before you launch the instance. 
Key pair name 
my-ec2-key 
C&#x27; Create new key pair 
Network settings Info 
Subnet 
Info 
Don&#x27;t include in launch template 
G&#x27; Create new subnet La 
When you specify a subnet, a network interface is automatically added to your template. 
Availability Zone 
Info 
Don&#x27;t include in launch template 
C 
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
mysecuritygroup sg-049b13a1cdd6e8df1 X 
VPC: vpc-06a42df24a27c3d21 
Advanced network configuration
</pre>
</details>

<details><summary>Screenshot 13: accessible text</summary>

<pre>
Virtual server type (instance type) 
Storage (volumes) Info 
tƷ.micro 
EBS Volumes 
Hide details 
Firewall (security group) 
mysecuritygroup 
Volume 1 (AMI Root) 
Storage (volumes) 
AMI Volumes are not included in the template unless modified 
1 volume(s) - 8 GiB 
Storage type| 
Info 
Device name - required 
Info 
Snapshot |Info 
EBS 
/dev/xvda 
snap-0d8f912520456bdc8 
ancel 
Create launch template 
Size (GiB) 
Info 
Volume type | Info 
IOPS |Info 
8 
gpƷ 
Free tier eligible 
3000 
Delete on termination 
Info 
Encrypted 
Info 
KMS key| 
Info 
Yes 
Not encrypted 
Don&#x27;t include in launch template 
D 
KMS keys are only applicable when encryption is set on this volume. 
Throughput | Info 
Volume initialization rate - optional |Info 
EBS card index - new, optional | Info 
125 
Enter a value 
Don&#x27;t include in launch template 
Min: 100 MiB/s, Max: 300 MiB/s. Additional charges apply 
The selected instance type does not support multiple EBS card indexes. 
Add new volume 
Resource tags Info 
No resource tags are currently included in this template. Add a resource tag to include it in the launch template. 
Add new tag 
You can add up to 50 more tags. 
&gt; Advanced details Info
</pre>
</details>

### Auto Scaling Group

<details><summary>Screenshot 14: accessible text</summary>

<pre>
Launch template Info 
For accounts created after May 31, 2023, the EC2 console only supports creating Auto Scaling groups with launch templates. Creating Auto Scaling groups with launch configurations is 
not recommended but still available via the CLI and API until December 31, 2023. 
Launch template 
Choose a launch template that contains the instance-level settings, such as the Amazon Machine Image (AMI), instance type, key pair, and security groups. 
0 
myapp-lt-v1 
Create a launch template La 
Version 
Default (1) 
Create a launch template version La 
Description 
Launch template 
Instance type 
Launch Template for spring boot ASG using custom AMI 
myapp-lt-v1 
t3.micro 
It-01759be4abe8f5170 
AMI ID 
Security groups 
Request Spot Instances 
ami-06830491f681b9b55 
No 
Key pair name 
Security group IDs 
my-ec2-key 
sg-049b13a1cdd6e8df1L 
Additional details 
Storage (volumes) 
Date created 
Sat Aug 22 2026 12:37:47 GMT+0530 (India Standard 
Time) 
Cancel 
Next
</pre>
</details>

<details><summary>Screenshot 15: accessible text</summary>

<pre>
Network Info 
For most applications, you can use multiple Availability Zones and let EC2 Auto Scaling balance your instances across the zones. The default VPC and default subnets are suitable for getting started 
quickly. 
VPC 
Choose the VPC that defines the virtual network for your Auto Scaling group. 
vpc-06a42df24a27c3d21 
172.31.0.0/16 Default 
Create a VPC La 
Availability Zones and subnets 
Define which Availability Zones and subnets your Auto Scaling group can use in the chosen VPC. 
Select Availability Zones and subnets 
aps1-az1 (ap-south-1a) | subnet-0f3bea01f6bb5f1fe X 
172.31.32.0/20 Default 
aps1-az3 (ap-south-1b) | subnet-075852bc286b9da6a X 
172.31.0.0/20 Default 
Create a subnet La 
Availability Zone distribution - new 
Auto Scaling automatically balances instances across Availability Zones. If launch failures occur in a zone, select a strategy. 
O 
Balanced best effort 
Balanced only 
Reservations Then Balanced 
If launches fail in one Availability Zone, Auto Scaling will attempt 
If launches fail in one Availability Zone, Auto Scaling will 
Prioritizes launching into Capacity Reservations, distributing 
to launch in another healthy Availability Zone. 
continue to attempt to launch in the unhealthy Availability Zone 
across AZs with available reservations. When reservations are 
to preserve balanced distribution. 
fully utilized, falls back to balanced on-demand.
</pre>
</details>

<details><summary>Screenshot 16: accessible text</summary>

<pre>
Set the initial size of the Auto Scaling group. After creating the group, you can change its size to meet demand, either manually or by using automatic scaling. 
Desired capacity type 
Choose the unit of measurement for the desired capacity value. vCPUs and Memory(GiB) are only supported for mixed instances groups configured with a set of instance attributes. 
Units (number of instances) 
Desired capacity 
Specify your group size. 
3 
Scaling Info 
You can resize your Auto Scaling group manually or automatically to meet changes in demand. 
Scaling limits 
Set limits on how much your desired capacity can be increased or decreased. 
Min desired capacity 
Max desired capacity 
2 
5 
Equal or less than desired capacity 
Equal or greater than desired capacity 
Automatic scaling - optional 
Choose whether to use a target tracking policy | Info 
You can set up other metric-based scaling policies and scheduled scaling after creating your Auto Scaling group. 
No scaling policies 
O Target tracking scaling policy 
Your Auto Scaling group will remain at its initial size and will not dynamically resize to meet demand. 
Choose a CloudWatch metric and target value and let the scaling policy adjust the desired capacity in 
proportion to the metric&#x27;s value. 
Scaling policy name 
Target Tracking Policy 
Metric type 
Info 
Monitored metric that determines if resource utilization is too low or high. If using EC2 metrics, consider enabling detailed monitoring for better scaling performance. 
Average CPU utilization 
Target value 
50 
Instance warmup 
Info 
300 
seconds 
Disable scale in to create only a scale-out policy
</pre>
</details>

### Verify the instances

<details><summary>Screenshot 17: accessible text</summary>

<pre>
Auto Scaling groups (1) Info 
Last updated 
less than a minute ago 
Launch configurations 
Launch templates 
Actions 
Create Auto Scaling group 
V 
Q Search your Auto Scaling groups 
1 
Status - new 
Launch template/configuration [ 
Instances 
Instance health - new 
Desired capacity 
Min 
D 
D 
Name 
myapp-asg-demo 
At desired capacity 
myapp-lt-v1 | Version Default 
3/3 Healthy 
2
</pre>
</details>

<details><summary>Screenshot 18: accessible text</summary>

<pre>
Instance ID 
Instance state 
Instance type 
Public IPv4 ... 
D 
D 
Status check 
Availability Zone 
Public IPv4 DNS 
i-06f266d017bff2375 
Running 
tƷ.micro 
Initializing 
ap-south-1b 
ec2-13-233-156-233.ap ... 
13.233.156.233 
i-042ea3e9d1047621a 
Running 
t3.micro 
3/3 checks passec 
ap-south-1a 
ec2-13-206-221-46.ap -... 
13.206.221.46 
i-050dcdabd6f8c763c 
Running 
t3.micro 
3/3 checks passec 
ap-south-1a 
ec2-13-233-124-23.ap -... 
13.233.124.23
</pre>
</details>

<details><summary>Screenshot 19: accessible text</summary>

<pre>
V 
1 
G 
A Not Secure 
13.233.124.23:8080 
Hi all - from shrayansh
</pre>
</details>

<details><summary>Screenshot 20: accessible text</summary>

<pre>
G 
A Not Secure 
13.233.156.233:8080 
1 
Hi all - from shrayansh
</pre>
</details>

[AWS index](./index.md) · [← EC2](./EC2.md) · [Application Load Balancer →](./Application%20Load%20Balancer.md)

*Screenshots are represented by their OneNote accessible text. The original bitmap images are not embedded; image text may contain recognition errors.*
