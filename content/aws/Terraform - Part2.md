---
title: "Terraform - Part2"
tags:
  - aws
  - terraform
---

# Terraform - Part2

[AWS index](./index.md) · [← Terraform - Part1](./Terraform%20-%20Part1.md) · [EC2 →](./EC2.md)

*OneNote page: Saturday, 4 July 2026 6:05 PM*

## At a glance

- The page follows the VPC, subnet, gateway and route table setup in sequence.
- It is mostly screenshots; their accessible text is preserved in expandable blocks.

## On this page

- [Target infrastructure](#target-infrastructure)
- [VPC and subnets](#vpc-and-subnets)
- [Internet gateway](#internet-gateway)
- [NAT gateway](#nat-gateway)
- [Route tables](#route-tables)

## Complete page transcription

### Target infrastructure

Now, lets write Terraform files for below Infrastructure.

<details><summary>Screenshot 1: accessible text</summary>

<pre>
AWS Region 
VPC (CIDR: 192.168.0.0/23) 
Default Route Table 
AZ-1a 
Destination 
Target 
Public Subnet-1a 
192.168.0.0/23 
local 
Private IP: 192.168.0.0/25 
public Route Table 
Destination 
Target 
NAT . 
0.0.0.0/0 
lgw-Oabc 
ID: Igw-Oabc ... 
Gateway , 
Private IP: .... 
Internet 
Public IP: ..... 
Gateway 
Public IP: xyz 
AZ-1b 
Private Subnet-1b 
Private IP: 192.168.0.128/25 
private Route Table 
Destination 
Target 
EC2 instance 
0.0.0.0/0 
nat-1ab1c
</pre>
</details>

### VPC and subnets

<details><summary>Screenshot 2: accessible text</summary>

<pre>
Add details -&gt; Create VPC 
VPC settings 
Resources to create Info 
We will set up everything, that&#x27;s why VPC only 
Create only the VPC resource or the VPC and other networking resources 
O VPC only 
VPC and more 
Name tag - optional 
Creates a tag with a key of &#x27;Name&#x27; and a value that you specify. 
vpc-conceptsbyshrayansh-prod 
IPV4 CIDR block Info 
VPC name 
IPV4 CIDR manual input 
IPAM-allocated IPv4 CIDR block 
IPV4 CIDR 
192.168.0.0/23 
CIDR block size must be between /16 and /28. 
IPV6 CIDR block Info 
Allocate private IP range for this network, /23 we will get around 
No IPv6 CIDR block 
512 IP addresses 
IPAM-allocated IPV6 CIDR block 
Amazon-provided IPV6 CIDR block 
IPV6 CIDR owned by me 
Tenancy Info 
Default 
VPC encryption control ($) 
Info 
Monitor mode provides visibility into encryption status without blocking traffic. Enforce mode prevents unencrypted traffic. Additional charges apply La 
O None 
Monitor mode 
Enforce mode 
See which resources in your VPC are 
Requires all resources, except exclusions, in 
unencrypted but allow the creation of 
your VPC to be encryption-capable and blocks 
unencrypted resources. 
creation of unencrypted resources. 
Tags 
A tag is a label that you assign to an AWS resource. Each tag consists of a key and an optional value. You can use tags to search and filter your resources or track your AWS costs. 
Key 
Value - optional 
Q Name 
× 
vpc-conceptsbyshrayansh-prod 
X 
Remove tag 
Add tag 
You can add 49 more tags 
Cancel 
Preview code 
Create VPC
</pre>
</details>

<details><summary>Screenshot 3: accessible text</summary>

<pre>
Create subnet Info 
VPC 
VPC ID 
Create subnets in this VPC. 
vpc-0e288bbc758779d85 (vpc-conceptsbyshrayansh-prod) 
Associated VPC CIDRs 
IPV4 CIDRs 
192.168.0.0/23
</pre>
</details>

<details><summary>Screenshot 4: accessible text</summary>

<pre>
Subnet settings 
Specify the CIDR blocks and Availability Zone for the subnet. 
Subnet 1 of 1 
Subnet name 
Create a tag with a key of &#x27;Name&#x27; and a value that you specify. 
public-subnet-1a 
The name can be up to 256 characters long. 
Availability Zone Info 
Choose the zone in which your subnet will reside, or let Amazon choose one for you. 
Asia Pacific (Mumbai) / aps1-az1 (ap-south-1a) 
IPV4 VPC CIDR block Info 
Choose the VPC&#x27;s IPv4 CIDR block for the subnet. The subnet&#x27;s IPv4 CIDR must lie within this block. 
192.168.0.0/23 
IPv4 subnet CIDR block 
192.168.0.0/25 
128 IP 
V 
&gt; 
&lt; 
&gt; 
Tags - optional 
Key 
Value - optional 
Q Name 
X 
Q public-subnet-1a 
X 
Remove 
Add new tag 
You can add 49 more tags. 
Remove 
Add new subnet 
Cancel 
Create subnet
</pre>
</details>

<details><summary>Screenshot 5: accessible text</summary>

<pre>
Select public subnet -&gt; Actions -&gt; Edit subnet settings 
You have successfully created 1 subnet: subnet-06f64e033b2a4addc 
X 
Subnets (1/1) Info 
Last updated 
less than a minute ago 
Actions 
Create subnet 
Q Find subnets by attribute or tag 
View details 
Subnet ID : subnet-06f64e033b2a4addc 
X 
Clear filters 
Create flow log 
Edit subnet settings 
1 &gt; 
Name 
Subnet ID 
State 
VPC 
Block Public ... 
IPV4 CIDR 
|IPV6 CIC 
D 
D 
Edit IPv6 CIDRs 
public-subnet-1a 
subnet-06f64e033b2a4addc 
Available 
vpc-0e288bbc758779d85 | vpc -... 
off 
192.168.00/25 
Edit network ACL association 
- 
Edit route table association 
Edit CIDR reservations 
Share subnet 
Manage tags 
Delete subnet 
Click on &quot;Enable auto-assign public IPv4 address&quot; -&gt; Save 
Edit subnet settings Info 
Subnet 
Subnet ID 
Name 
subnet-06f64e033b2a4addc 
[ public-subnet-1a 
Auto-assign IP settings Info 
Enable AWS to automatically assign a public IPv4 or IPv6 address to a new primary network interface for an instance in this subnet. 
Ejable auto-assign public IPv4 address Info 
Enable auto-assign customer-owned IPv4 address Info 
Option disabled because no customer owned pools found. 
Resource-based name (RBN) settings Info 
Specify the hostname type for EC2 instances in this subnet and optional RBN DNS query settings. 
Enable resource name DNS A record on launch Info 
Enable resource name DNS AAAA record on launch Info 
Hostname type Info 
Resource name 
O IP name 
DNS64 settings 
Enable DNS64 to allow IPv6-only services in Amazon VPC to communicate with IPv4-only services and networks. 
Enable DNS64 Info 
Cancel 
Save
</pre>
</details>

<details><summary>Screenshot 6: accessible text</summary>

<pre>
AWS Region 
VPC (CIDR: 192.168.0.0/23) 
Default Route Table 
AZ-1a 
Destination 
Target 
Public Subnet-1a 
192.168.0.0/23 
local 
Private IP: 192.168.0.0/25 
public Route Table 
Destination 
Target 
NAT . 
0.0.0.0/0 
lgw-Oabc 
ID: Igw-Oabc ... 
Gateway , 
Private IP: .... 
Internet 
Public IP: ..... 
Gateway 
Public IP: xyz 
AZ-1b 
Private Subnet-1b 
Private IP: 192.168.0.128/25 
private Route Table 
Destination 
Target 
EC2 instance 
0.0.0.0/0 
nat-1ab1c
</pre>
</details>

### Internet gateway

<details><summary>Screenshot 7: accessible text</summary>

<pre>
Name -&gt; create Internet Gateway 
VPC &gt; Internet gateways &gt; Create internet gateway 
Create internet gateway Info 
An internet gateway is a virtual router that connects a VPC to the internet. To create a new internet gateway specify the name for the gateway below. 
Internet gateway settings 
Name tag 
wesw tag with a key of &#x27;Name&#x27; and a value that you specify. 
conceptsbyshrayansh-internetgateway 
A tag is a label that you assign to an AWS resource. Each tag consists of a key and an optional value. You can use tags to search and filter your resources or track your AWS costs. 
Tags - optional 
Key 
Value - optional 
Q Name 
Q conceptsbyshrayansh-internetgateway 
X 
Remove 
Add new tag 
ou can add 49 more tags. 
Cancel 
Create internet gateway 
Our newly created Internet Gateway -&gt; Attach to VPC 
wing internet gateway was created: igw-Of46d967b76d8d807 - conceptsbyshrayansh-internetgateway. You can now att 
way. You can now attach to a VPC to enable the VPC to communicate with the i 
Attach to a VPC 
X 
igw-Of46d967b76d8d807 / conceptsbyshrayansh-internetgateway 
Actions 
Attach to VPC 
Details Info 
Detach from VPC 
Internet gateway ID 
State 
VPC ID 
Owner 
Tigw-Of46d967b76d8d807 
Detached 
643537035131 
Manage tags 
Delete 
Tags (1) 
Manage tags 
Q Search tags 
1 &gt; 
- 
Key 
Value 
Name 
conceptsbyshrayansh-internetgateway 
Select our VPC and Click Attach 
VPC &gt; Internet gateways &gt; Attach to VPC (igw-Of46d967b76d8d807) 
Attach to VPC (igw-0f46d967b76d8d807) Info 
VDC 
Attach an internet gateway to &gt; VPC to enable the VPC to communicate with the internet. Specify the VPC to attach below. 
Available VPCs 
Available VPCS 
Attach the internet gateway to this VPC. 
Q vpc-0e288bbc758779d85 
x 
AWS Command Line Interface command 
Cancel 
Attach internet gateway
</pre>
</details>

<details><summary>Screenshot 8: accessible text</summary>

<pre>
AWS Region 
VPC (CIDR: 192.168.0.0/23) 
Default Route Table 
AZ-1a 
Destination 
Target 
Public Subnet-1a 
192.168.0.0/23 
local 
Private IP: 192.168.0.0/25 
public Route Table 
Destination 
Target 
NAT . 
0.0.0.0/0 
lgw-Oabc 
ID: Igw-Oabc ... 
Gateway , 
Private IP: .... 
Internet 
Public IP: ..... 
Gateway 
Public IP: xyz 
AZ-1b 
Private Subnet-1b 
Private IP: 192.168.0.128/25 
private Route Table 
Destination 
Target 
EC2 instance 
0.0.0.0/0 
nat-1ab1c
</pre>
</details>

### NAT gateway

<details><summary>Screenshot 9: accessible text</summary>

<pre>
NAT gateway settings 
Provide the name for NAT Gateway 
Name - optional 
Create a tag with a key of &#x27;Name&#x27; and Value that you specify. 
conceptsbyshrayansh-NAT 
The name can be up to 256 characters long. 
Manually select the public subnet in which we want to 
Availability mode Info 
put the NAT Gateway 
Choose whether to deploy across all zones in the region or restrict to a single availability zone. 
Regional - new 
Zonal 
Scales automatically across all regional AZs, simplifying management for multi AZ 
Provides granular control within a specific availability zone, adhering to subnet level 
deployments. 
settings. 
Subnet 
Select a subnet in which to create the NAT gateway. 
subnet-06f64e033b2a4addc (public-subnet-1a) 
- Allocate Elastic IP, this will provide stable public IP address. 
- it does not change even if service restarted. 
Connectivity type 
Select a connectivity type for the NAT gateway. 
- Why permanent bcoz, when a website replies it send back to NAT 
Public 
NAT Gateway need to talk to Internet Gateway 
address, but if NAT public address changes frequently then its an iss 
Private 
That&#x27;s why we need to put it inside PUBLIC SUBNET 
Elastic IP allocation ID Info 
Assign an Elastic IP address to the NAT gateway. 
eipalloc-00005bb7c3633b262 
Allocate Elastic IP 
Additional settings Info 
Tags 
A tag is a label that you assign to an AWS resource. Each tag consists of a key and an optional value. You can use tags to search and filter your resources or track your AWS costs. 
Key 
Value - optional 
Q Name 
X 
Q conceptsbyshrayansh-NAT 
X 
Remove 
Add new tag 
You can add 49 more tags 
Cancel 
Create NAT gateway
</pre>
</details>

<details><summary>Screenshot 10: accessible text</summary>

<pre>
Picture
</pre>
</details>

### Route tables

<details><summary>Screenshot 11: accessible text</summary>

<pre>
Action -&gt; Edit subnet associations 
Updated routes for rtb-031baf5458b24d0d4 / public-subnet-routetable successfully 
X 
&gt; Details 
rtb-031baf5458b24d0d4 / public-subnet-routetable 
Actions 
Set main route table 
Details Info 
Edit subnet associations 
Route table ID 
Main 
Explicit subnet associations 
Edge associations 
Edit edge associations 
rtb-031baf5458b24d0d4 
subnet-06f64e033b2a4addc / public-subnet-1a 
Edit route propagation 
VPC 
Owner ID 
Edit routes 
vpc-0e288bbc758779d85 | vpc-conceptsbyshrayansh- 
643537035131 
Manage tags 
prod 
Delete 
Routes 
Subnet associations 
Edge associations 
Route propagation 
Tags 
Routes (2) 
Both v 
Edit routes 
Q Filter routes 
&lt; 1 &gt; 
Target 
Propagated 
7 
D 
Destination 
Status 
Route Origin 
D 
0.0.0.0/0 
gw-Of46d967b76d8d807 
Active 
No 
Create Route 
192.168.0.0/23 
local 
Active 
No 
Create Route Table 
Select with which Subnet we need to connect this Route table 
Edit subnet associations 
Change which subnets are associated with this route table. 
Available subnets (1/2) 
Q Filter subnet associations 
&lt; 1 &gt; 
Name 
D 
Subnet ID 
IPV4 CIDR 
IPV6 CIDR 
Route table ID 
D 
public-subnet-1a 
subnet-06f64e033b2a4addc 
192.168.0.0/25 
Main (rtb-064cl 
private-subnet-1b 
subnet-0c36555e6d75397d8 
192.168.0.128/25 
Main (rtb-064cl 
Selected subnets 
subnet-06f64e033b2a4addc / public-subnet-1a X 
Cancel 
Save associations
</pre>
</details>

<details><summary>Screenshot 12: accessible text</summary>

<pre>
= VPC &gt; Route tables &gt; rtb-031baf5458b24d0d4 &gt; 
Edit routes 
Edit routes 
Destination 
Target 
Statu 
Propagated 
Route Origin 
192.168.0.0/23 
local 
Active 
No 
CreateRouteTable 
Q local 
X 
Add route 
Cancel 
Preview 
Save changes 
Add new route for Internet Gateway 
= VPC &gt; Route tables &gt; rtb-031baf5458b24d0d4 &gt; Edit routes 
Edit routes 
Destination 
Target 
Status 
Propagated 
Route Origin 
192.168.0.0/23 
local 
Active 
No 
CreateRouteTable 
Q local 
X 
Q 0.0.0.0/0 
X 
Internet Gateway 
No 
CreateRoute 
Remove 
Q igw-Of46d967b76d8d807 
X 
Add route 
Cancel 
Preview 
Save changes
</pre>
</details>

<details><summary>Screenshot 13: accessible text</summary>

<pre>
Updated routes for rtb-07ae65c12d63da233 / private-subnet-routetable successfully 
X 
› Details 
rtb-07ae65c12d63da233 / private-subnet-routetable 
Actions 
Set main route table 
Details Info 
Edit subnet associations 
Route table ID 
Main 
Explicit subnet associations 
Edge associations 
Edit edge associations 
rtb-07ae65c12d63da233 
No 
Edit route propagation 
- 
VPC 
Owner ID 
Edit routes 
vpc-0e288bbc758779d85 | vpc-conceptsbyshrayansh- 
643537035131 
Manage tags 
prod 
Delete 
Routes 
Subnet associations 
Edge associations 
Route propagation 
Tags 
Routes (2) 
Both v 
Edit routes 
Q Filter routes 
( 1 &gt; 
Destination 
Target 
Status 
Propagated 
Route Origin 
D 
0.0.0.0/0 
nat-Oe210bf2529e460f8 
Active 
No 
Create Route 
192.168.0.0/23 
local 
Active 
No 
Create Route Table 
Edit subnet associations 
Change which subnets are associated with this route table. 
Available subnets (1/2) 
Q Filter subnet associations 
&lt; 1 &gt; 
D 
Name 
IPV4 CIDR 
IPV6 CIDR 
Route table ID 
D 
Subnet ID 
public-subnet-1a 
subnet-06f64e033b2a4addc 
192.168.0.0/25 
rtb-031baf545 
private-subnet-1b 
subnet-0c36555e6d75397d8 
192.168.0.128/25 
Main (rtb-064c 
Selected subnets 
subnet-0c36555e6d75397d8 / private-subnet-1b X 
Cancel 
Save associations
</pre>
</details>

<details><summary>Screenshot 14: accessible text</summary>

<pre>
Route table rtb-07ae65c12d63da233 | private-subnet-routetable was created successfully. 
× 
rtb-07ae65c12d63da233 / private-subnet-routetable 
Actions 
Details Info 
Route table ID 
Main 
Explicit subnet associations 
Edge associations 
rtb-07ae65c12d63da233 
No 
- 
VPC 
Owner ID 
vpc-0e288bbc758779d85 | vpc-conceptsbyshrayansh- 
643537035131 
prod 
Routes 
Subnet associations 
Edge associations 
Route propagation 
Tags 
Routes (1) 
Both 
Edit routes 
Q Filter routes 
Destination 
Target 
Status 
Propagated 
Route Origin 
D 
192.168.0.0/23 
local 
Active 
No 
Create Route Table 
E 
VPC &gt; Route tables &gt; rtb-07ae65c12d63da233 &gt; Edit routes 
Edit routes 
Destination 
Target 
Status 
Propagated 
Route Origin 
192.168.0.0/23 
local 
Active 
No 
CreateRouteTable 
a local 
X 
Q 0.0.0.0/0 
X 
NAT Gateway 
No 
CreateRoute 
Remove 
Q nat-Oe210bf2529e460f8 
X 
Add route 
Cancel 
Preview 
Save changes
</pre>
</details>

<details><summary>Screenshot 15: accessible text</summary>

<pre>
Picture
</pre>
</details>

[AWS index](./index.md) · [← Terraform - Part1](./Terraform%20-%20Part1.md) · [EC2 →](./EC2.md)

*Screenshots are represented by their OneNote accessible text. The original bitmap images are not embedded; image text may contain recognition errors.*
