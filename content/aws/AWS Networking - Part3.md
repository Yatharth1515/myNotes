---
title: "AWS Networking - Part3"
tags:
  - aws
  - networking
---

# AWS Networking - Part3

[AWS index](./index.md) · [← AWS Networking - Part2](./AWS%20Networking%20-%20Part2.md) · [Terraform - Part1 →](./Terraform%20-%20Part1.md)

*OneNote page: Friday, 12 June 2026 10:56 PM*

## At a glance

- This walkthrough adds instance security groups and subnet network ACLs to the VPC.
- Most steps on this page appear in screenshots; expand each screenshot transcription below.

## On this page

- [VPC layout](#vpc-layout)
- [Security groups](#security-groups)
- [Network ACLs](#network-acls)

## Complete page transcription

### VPC layout

VPC (CIDR: 192.168.0.0/23)

Default Route Table

### Security groups

<details><summary>Screenshot 1: accessible text</summary>

<pre>
VPC dashboard 
&lt; 
Create VPC 
Launch EC2 Instances 
Note: Your Instances will launch in the Asia Pacific region. 
AWS Global View [7 
Filter by VPC: 
Resources by Region 
You are using the following Amazon VPC resources 
Virtual private cloud 
Your VPCs 
VPCs 
Mumbai 2 
Subnets 
See all regions 
Route tables 
Internet gateways 
Subnets 
Mumbai 5 
Egress-only Internet 
gateways 
See all regions 
DHCP option sets 
Elastic IPs 
Route Tables 
Mumbai 4 
Managed prefix lists 
See all regions 
NAT gateways 
Peering connections 
Internet Gateways 
Mumbai 2 
Route servers 
› See all regions 
Security 
Network ACLs 
Egress-only Internet Gateways 
Mumbai 0 
Security groups 
See all regions
</pre>
</details>

<details><summary>Screenshot 2: accessible text</summary>

<pre>
Create security group Info 
A security group acts as a virtual firewall for your instance to control inbound and outbound traffic. To create a new security group, complete the fields below. 
Basic details 
Security group name | Info 
private-sg-ec2 
Name cannot be edited after creation. 
Description | Info 
private ec2, no inbound 
VPC Info 
vpc-Oe288bbc758779d85 (vpc-conceptsbyshrayansh-prod) 
Inbound rules Info 
This security group has no inbound rules. 
Add rule 
Outbound rules Info 
Type Info 
Protocol Info 
Port range Info 
Destination Info 
Description - optional Info 
HTTPS 
TCP 
443 
Anywhe ... 
Q 0.0.0.0/0 
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

<details><summary>Screenshot 3: accessible text</summary>

<pre>
EC2 &gt; 
Instances 
&gt; 
Launch an instance 
Launch an instance 
Info 
Amazon EC2 allows you to create virtual machines, or instances, that run on the AWS Cloud. Quickly get started by following the simp 
Name and tags Info 
Name 
e.g. My Web Server 
Add additional tags
</pre>
</details>

<details><summary>Screenshot 4: accessible text</summary>

<pre>
Network settings Info 
VPC - required 
Info 
vpc-0e288bbc758779d85 (vpc-conceptsbyshrayansh-prod) 
G 
192.168.0.0/23 
Subnet 
Info 
subnet-06f64e033b2a4addc 
public-subnet-1a 
C&#x27; Create new subnet La 
VPC: vpc-0e 288bbc758779d85 
Owner: 643537035131 
Availability Zone: ap-south-1a (aps1-az1) 
Zone type: Availability Zone IP addresses available: 122 
CIDR: 192.168.0.0/25) 
Auto-assign public IP 
Info 
Enable 
Firewall (security groups) |Info 
A security group is a set of firewall rules that control the traffic for your instance. Add rules to allow specific traffic to reach your instance. 
Create security group 
O 
Select existing security group 
Common security groups 
Info 
Select security groups 
C Compare security group rules 
private-sg-ec2 sg-07e7f989dd895c2bf X 
VPC: vpc-0e288bbc758779d85 
Security groups that you add or remove here will be added to or removed from all your network interfaces. 
Advanced network configuration
</pre>
</details>

### Network ACLs

<details><summary>Screenshot 5: accessible text</summary>

<pre>
VPC dashboard 
&lt; 
Create VPC 
Launch EC2 Instances 
Note: Your Instances will launch in the Asia Pacific region. 
AWS Global View [7 
Filter by VPC: 
Resources by Region 
You are using the following Amazon VPC resources 
Virtual private cloud 
Your VPCs 
VPCs 
Mumbai 2 
Subnets 
See all regions 
Route tables 
Internet gateways 
Subnets 
Mumbai 5 
Egress-only Internet 
gateways 
See all regions 
DHCP option sets 
Elastic IPs 
Route Tables 
Mumbai 4 
Managed prefix lists 
See all regions 
NAT gateways 
Peering connections 
Internet Gateways 
Mumbai 2 
Route servers 
› See all regions 
Security 
Network ACLs 
Egress-only Internet Gateways 
Mumbai 0 
Security groups 
See all regions
</pre>
</details>

<details><summary>Screenshot 6: accessible text</summary>

<pre>
Create network ACL Info 
A network ACL is an optional layer of security that acts as a firewall for controlling traffic in and out of a subnet. 
Network ACL settings 
Name - optional 
Creates a tag with a key of &#x27;Name&#x27; and a value that you specify. 
private-subnet-NACL 
7 
VPC 
VPC to use for this network ACL. 
vpc-0e288bbc758779d85 (vpc-conceptsbyshrayansh-prod) 
Tags 
A tag is a label that you assign to an AWS resource. Each tag consists of a key and an optional value. You can use tags to search and filter your resources or track your AWS costs. 
Key 
Value - optional 
Q Name 
X 
Q private-subnet-NACL 
X 
Remove tag 
Add tag 
You can add 49 more tags 
Cancel 
Create network ACL
</pre>
</details>

<details><summary>Screenshot 7: accessible text</summary>

<pre>
You successfully created acl-Of19affd74d4ee2a0 / private-subnet-NACL. 
X 
acl-Of19affd74d4ee2a0 / private-subnet-NACL 
Actions 
Details Info 
Network ACL ID 
Associated with 
Default 
VPC ID 
acl-Of19affd74d4ee2a0 
No 
vpc-0e288bbc758779d85 / vpc-conceptsbyshrayansh- 
prod 
Owner 
643537035131 
Inbound rules 
Outbound rules 
Subnet associations 
Tags 
Inbound rules (1) 
Edit inbound rules 
Q Filter inbound rules 
&lt; 1 &gt; 
D 
Rule number 
Type 
Protocol 
Port range 
Source 
| Allow/Deny 
0 
D 
* 
All traffic 
All 
All 
0.0.0.0/0 
Deny
</pre>
</details>

<details><summary>Screenshot 8: accessible text</summary>

<pre>
Edit inbound rules Info 
Inbound rules control the incoming traffic that&#x27;s allowed to reach the VPC. 
Rule number Info 
Type Info 
Protocol Info 
Port range Info 
Source Info 
Allow/Deny Info 
100 
All traffic 
ALL 
ALL 
0.0.0.0/0 
Allow 
Remove 
* 
All traffic 
ALL 
V 
ALI 
0.0.0.0/0 
Deny 
Add new rule 
Sort by rule number 
Cancel 
Preview changes 
Save changes
</pre>
</details>

<details><summary>Screenshot 9: accessible text</summary>

<pre>
acl-Of19affd74d4ee2a0 / private-subnet-NACL 
Actions 
Details Info 
Network ACL ID 
Associated with 
Default 
VPC ID 
acl-Of19affd74d4ee2a0 
No 
vpc-0e288bbc758779d85 / vpc-conceptsbyshrayansh- 
prod 
Owner 
643537035131 
Inbound rules 
Outbound rules 
Subnet associations 
Tags 
Outbound rules (1) 
Edit outbound rules 
Q Filter outbound rules 
&lt; 1 &gt; 
D 
Rule number 
V | Type 
Protocol 
v | Port range 
Destination 
v | Allow/Deny 
D 
All traffic 
All 
All 
0.0.0.0/0 
Deny
</pre>
</details>

<details><summary>Screenshot 10: accessible text</summary>

<pre>
Edit outbound rules Info 
Outbound rules control the outgoing traffic that&#x27;s allowed to leave the VPC. 
Rule number Info 
Type Info 
Protocol Info 
Port range Info 
Destination Info 
Allow/Deny Info 
200 
All traffic 
ALL 
ALL 
0.0.0.0/0 
Allow 
Remove 
* 
All traffic 
ALL 
ALL 
0.0.0.0/0 
Deny 
Add new rule 
Sort by rule number 
Cancel 
Preview changes 
Save changes
</pre>
</details>

<details><summary>Screenshot 11: accessible text</summary>

<pre>
acl-00683e4ac64f10b5d 
acl-00683e4ac64f10b5d 
Actions 
Edit inbound rules 
Details Info 
Edit outbound rules 
Network ACL ID 
Associated with 
Default 
VPC ID 
Edit subnet associations 
acl-00683e4ac64f10b5d 
2 Subnets 
Yes 
vpc-0e288bbc758779d85 / vpc-conceptsbysl 
prod 
Manage tags 
Owner 
Delete 
643537035131 
Inbound rules 
Outbound rules 
Subnet associations 
Tags 
Outbound rules (2) 
Edit outbound rules 
Q Filter outbound rules 
1 &gt; 
Rule number 
Destination 
7 
Allow/Deny 
D 
D 
Type 
Protocol 
Port range 
100 
All traffic 
0.0.0.0/0 
Allow 
All traffic 
All 
All 
0.0.0.0/0 
Deny
</pre>
</details>

<details><summary>Screenshot 12: accessible text</summary>

<pre>
Edit subnet associations Info 
Change which subnets are associated with this network ACL. 
Available subnets (1/2) 
Filter subnet associations 
&lt; 1 &gt; 
Name 
Subnet ID 
Associated with 
Availability Zone 
IPV4 CIDR 
IPV6 CIDR 
- 
D 
D 
D 
D 
D 
public-subnet-1a 
subnet-06f64e033b2a4addc 
acl-00683e4ac64f10b5d 
aps1-az1 (ap-south-1a) 
192.168.0.0/25 
private-subnet-1b 
subnet-0c36555e6d75397d8 
acl-00683e4ac64f10b5d 
aps1-az3 (ap-south-1b) 
192.168.0.128/25 
Selected subnets 
subnet-0c36555e6d75397d8 / private-subnet-1b X 
Cancel 
Save changes
</pre>
</details>

[AWS index](./index.md) · [← AWS Networking - Part2](./AWS%20Networking%20-%20Part2.md) · [Terraform - Part1 →](./Terraform%20-%20Part1.md)

*Screenshots are represented by their OneNote accessible text. The original bitmap images are not embedded; image text may contain recognition errors.*
