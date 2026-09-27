---
title: "AWS Networking - Part2"
tags:
  - aws
  - networking
---

# AWS Networking - Part2

[AWS index](./index.md) · [← AWS Networking - Part1](./AWS%20Networking%20-%20Part1.md) · [AWS Networking - Part3 →](./AWS%20Networking%20-%20Part3.md)

*OneNote page: Friday, 12 June 2026 4:42 PM*

## At a glance

- A VPC defines the network range, then subnets divide it by availability zone.
- The walkthrough creates public and private subnets, an internet gateway, a NAT gateway and their route tables.
- NAT gateway and Elastic IP are chargeable in the source walkthrough.

## On this page

- [From networking fundamentals to AWS](#from-networking-fundamentals-to-aws)
- [VPC](#vpc)
- [Subnets](#subnets)
- [Public and private subnets](#public-and-private-subnets)
- [Create subnets](#create-subnets)
- [Internet gateway](#internet-gateway)
- [NAT gateway](#nat-gateway)
- [Route tables](#route-tables)
- [Verify the VPC](#verify-the-vpc)

## Complete page transcription

### From networking fundamentals to AWS

In network fundamentals:

- Network

- Switch

- Routers

- NAT

- Internet Gateways

- CIDR

- Routing Table

Etc.

AWS Network

If we understood the fundamentals, then understanding AWS Network will be very easy:

In AWS terms, we have:

- VPC (Virtual Private Cloud)

- Subnet

- AWS Route tables

- NAT Gateways

- IGW (Internet Gateway)

### VPC

VPC (Virtual Private Cloud)

Its just a private network block defined by CIDR range.

AWS Region

VPC (CIDR: 192.168.0.0/23)

All services -> VPC

<details><summary>Screenshot 1: accessible text</summary>

<pre>
Console home 
&gt; 
All services 
Console home 
&lt; 
DataSync 
AWS Transform 
AWS Mainframe Modernization 
All services 
Amazon Elastic VMware Service 
myApplications 
Networking &amp; Content Delivery 
VPC 
CloudFront 
API Gateway 
Direct Connect 
AWS App Mesh 
Global Accelerator 
Route 53 
AWS Data Transfer Terminal 
Amazon Route 53 Global Resolver 
AWS Cloud Map 
RTB Fabric 
Application Recovery Controller
</pre>
</details>

Create VPC within a particular Region

<details><summary>Screenshot 2: accessible text</summary>

<pre>
[Option+S] 
Ask Amazon Q 
Asia Pacific (Mumbai) v 
Create VPC 
Launch EC2 Instances 
Service Health 
Note: Your Instances will launch in the Asia Pacific region. 
View complete service 
Resources by Region 
C Refresh Resources 
You are using the following Amazon VPC resources 
Settings 
VPCs 
Mumbai 2 
Endpoint Services 
Mumbai 0 
Block Public Access 
See all regions 
See all regions 
Zones LA 
Console Experiments 
Subnets 
Mumbai 3 
NAT Gateways 
Mumbai 0 
See all regions 
See all regions 
Additional Info 
VPC Documentation
</pre>
</details>

Add details -> Create VPC

<details><summary>Screenshot 3: accessible text</summary>

<pre>
VPC settings 
Resources to create Info 
Create only the VPC resource or the VPC and other networking resources. 
VPC only 
VPC and more 
Name tag - optional 
Creates a tag with a key of &#x27;Name&#x27; and a value that you specify. 
vpc-conceptsbyshrayansh-prod 
IPv4 CIDR block Info 
IPv4 CIDR manual input 
IPAM-allocated IPv4 CIDR block 
IPV4 CIDR 
192.168.0.0/23 
CIDR block size must be between /16 and /28. 
IPV6 CIDR block Info 
O No IPV6 CIDR block 
IPAM-allocated IPV6 CIDR block 
Amazon-provided IPv6 CIDR block 
IPv6 CIDR owned by me 
Tenancy Info 
Default 
VPC encryption control ($) | Info 
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
Q vpc-conceptsbyshrayansh-prod 
X 
Remove tag 
Add tag 
You can add 49 more tags 
Cancel 
Preview code 
Create VPC
</pre>
</details>

We will set up everything, that’s why VPC only

VPC name

Allocate private IP range for this network, /23 we will get around 512 IP addresses

VPC is created, also notice the Main (default) route table

<details><summary>Screenshot 4: accessible text</summary>

<pre>
vpc-0e288bbc758779d85 / vpc-conceptsbyshrayansh-prod 
Actions V 
Details Info 
VPC ID 
State 
Block Public Access 
DNS hostnames 
vpc-0e288bbc758779d85 
Available 
O Off 
Disabled 
DNS resolution 
Tenancy 
DHCP option set 
Main route table 
Enabled 
default 
dopt-00ec3ac7fa4633257 
rtb-064c0c3c03f4370fe 
Main network ACL 
Default VPC 
IPV4 CIDR 
IPV6 pool 
acl-00683e4ac64f10b5d 
No 
192.168.0.0/23 
IPV6 CIDR (Network border group) 
Network Address Usage metrics 
Route 53 Resolver DNS Firewall rule groups 
Owner ID 
Disabled 
- 
643537035131 
Encryption control ID 
Encryption control mode 
Resource map 
CIDRS 
Flow logs 
Tags 
Integrations 
Resource map Info 
Show all details 
VPC 
Subnets (0) 
Route tables (1) 
Network Connections (0) 
Your AWS virtual network 
Subnets within this VPC 
Route network traffic to resources 
Connections to other networks 
vpc-conceptsbyshrayansh-prod 
rtb-064c0c3c03f4370fe
</pre>
</details>

<details><summary>Screenshot 5: accessible text</summary>

<pre>
Routes (1) 
Q Filter routes 
Destination 
Target 
Status 
192.168.0.0/23 
local 
Active
</pre>
</details>

AWS Region

VPC (CIDR: 192.168.0.0/23)

Default Route Table

Destination

Target

192.168.0.0/23

local

### Subnets

Subnet

Big Network is divided into small sub networks.

For example:

VPC: 192.168.0.0/23

Means we can have . Means around 500+ hosts (some IPs are reserved for broadcast etc.) can be added in 1 VPC.

Now we can create small sub networks say around 100 IP address in each Subnet.

192.168.0.0/23 = 2^9 = 512

11000000 . 10101000 . 00000000. 00000000

I want around 100 IP addresses in 1 subnet

2^6 = 64 (too small, we can not fit 100 hosts)

2^7 = 128 (perfect fit)   
   
i.e /25 = 2^(32-25) = 2^7 = 128

(free for hosts)

(fixed by subnet)

(fixed by VPC)

Subnet

CIDR

IP Range

Total IPs

Subnet-1

192.168.0.0/25

192.168.0.0    
to

192.168.0.127

128

11000000 . 10101000 . 00000000.00000000

11000000 . 10101000 . 00000000.01111111

Subnet-2

192.168.0.128/25

192.168.0.128    
to

192.168.0.255

128

11000000 . 10101000 . 00000000.10000000

11000000 . 10101000 . 00000000.11111111

Subnet-3

192.168.1.0/25

192.168.1.0   
to

192.168.1.127

128

11000000 . 10101000 . 00000001.00000000

11000000 . 10101000 . 00000001.01111111

Subnet-4

192.168.1.128/25

192.168.1.128   
to

192.168.1.255

128

11000000 . 10101000 . 00000001.10000000

11000000 . 10101000 . 00000001.11111111

Generally for each AZ, we create its own Subnet.

AWS Region

VPC (CIDR: 192.168.0.0/23)

AZ-1a

Default Route Table

Destination

Target

Subnet-1a

192.168.0.0/23

local

192.168.0.0/25

Route Table

Destination

Target

EC2 instance

AZ-1b

Subnet-1b

192.168.0.128/25

Route Table

Destination

Target

EC2 instance

### Public and private subnets

2 Types of Subnet:

Public Subnet

Private Subnet

Public Subnet routing table do have entry for Internet Gateway.

and any host (or EC2) sitting in this Subnet, also has public IP address and can talk directly with Internet.

Mostly in public subnet we keep:

Load balancer

NAT Gateways

But there is not such hard rule, we can even create EC2 or database instance here.

Private Subnet routing table do not have entry for Internet Gateway.

Any hosts present within Private Subnet, uses NAT Gateway to talk to Internet.

Mostly in private subnets we keep:

EC2 instances

Database

AWS Region

VPC (CIDR: 192.168.0.0/23)

AZ-1a

Default Route Table

Destination

Target

Public Subnet-1a

192.168.0.0/23

local

Private IP: 192.168.0.0/25

public  Route Table

Destination

Target

Igw-0abc

NAT

Gateway

0.0.0.0/0

ID: Igw-0abc…

Internet

Gateway

Private IP: ….

Public IP: …..

Public IP: xyz

AZ-1b

Private Subnet-1b

Private IP: 192.168.0.128/25

private Route Table

Destination

Target

nat-1ab1c

0.0.0.0/0

EC2 instance

### Create subnets

lets create subnet for AZ-1a

VPC -> Subnets

<details><summary>Screenshot 6: accessible text</summary>

<pre>
VPC dashboard 
‹ 
Create VPC 
Launch EC2 Instances 
AWS Global View La 
Note: Your Instances will launch in the Asia Pacific region. 
Filter by VPC: 
Resources by Region 
C Refresh Resources 
You are using the following Amazon VPC resources 
Virtual private cloud 
Your VPCs 
VPCS 
Mumbai 2 
Endpoint Services 
Mumbai 0 
Subnets 
&gt; See all regions 
See all regions 
Route tables 
Internet gateways 
Subnets 
Mumbai 3 
NAT Gateways 
Mumbai 0 
Egress-only Internet 
gateways 
See all regions 
See all regions 
DHCP option sets 
Elastic IPs 
Route Tables 
Mumbai 2 
VPC Peering Connections 
Mumbai 0 
Managed prefix lists 
› See all regions 
See all regions 
NAT gateways
</pre>
</details>

Click on Create Subnet

<details><summary>Screenshot 7: accessible text</summary>

<pre>
VPC &gt; Subnets 
Subnets (3) Info 
Last updated 
Actions 
Create subnet 
VPC dashboard 
1 minute ago 
V 
V 
Q Find subnets by attribute or tag 
1 &gt; 
AWS Global View 
IPV4 CIDR 
D 
VPC 
IPV6 CIDR 
D 
State 
Block Public ... 
D 
Filter by VPC: 
Name 
Subnet ID 
D 
D 
1 
subnet-Of3bea01f6bb5f1fe 
Available 
vpc-06a42df24a27c3d21 
Off 
172.31.32.0/20 
Virtual private cloud 
Off 
172.31.16.0/20 
1 
subnet-09e56df6c66e24c67 
Available 
vpc-06a42df24a27c3d21 
Your VPCs 
- 
subnet-075852bc286b9da6a 
Available 
vpc-06a42df24a27c3d21 
Off 
172.31.0.0/20 
Subnets 
Route tables 
Internet gateways 
Egress-only Internet 
gateways 
DHCP option sets
</pre>
</details>

Select the VPC, in which this subnet need to be created

<details><summary>Screenshot 8: accessible text</summary>

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

Public Subnet name -> AZ -> subnet CIDR -> Create Subnet

<details><summary>Screenshot 9: accessible text</summary>

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

Since it’s a public subnet -> any future EC2 instance created in this public subnet. Automatically public IPv4 will be assigned to it -> which EC2 instance will use it to talk to Internet Gateway

Select public subnet -> Actions -> Edit subnet settings

<details><summary>Screenshot 10: accessible text</summary>

<pre>
You have successfully created 1 subnet: subnet-06f64e033b2a4addc 
× 
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
1 
Edit subnet settings 
&lt; 1 
Subnet ID 
VPC 
Block Public ... 
D 
State 
IPV4 CIDR 
Edit IPV6 CIDRs 
IPV6 CIC 
D 
D 
Name 
public-subnet-1a 
subnet-06f64e033b2a4addc 
Available 
vpc-Oe288bbc758779d85 | vpc -... 
Off 
192.168.0.0/25 
Edit network ACL association 
Edit route table association 
Edit CIDR reservations 
Share subnet 
Manage tags 
Delete subnet
</pre>
</details>

Click on "Enable auto-assign public IPv4 address" -> Save

<details><summary>Screenshot 11: accessible text</summary>

<pre>
Edit subnet settings Info 
Subnet 
Subnet ID 
Name 
subnet-06f64e033b2a4addc 
public-subnet-1a 
Auto-assign IP settings Info 
Enable AWS to automatically assign a public IPv4 or IPv6 address to a new primary network interface for an instance in this subnet. 
Enable auto-assign public IPv4 address Info 
Enable auto-assign customer-owned IPv4 address Info 
Option disabled because no customer owned pools found. 
Resource-based name (RBN) settings Info 
Specify the hostname type for EC2 instances in this subnet and optional RBN DNS query settings. 
Enable resource name DNS A record on launch Info 
Enable resource name DNS AAAA record on launch Info 
Hostname type Info 
Resource name 
IP name 
DNS64 settings 
Enable DNS64 to allow IPv6-only services in Amazon VPC to communicate with IPv4-only services and networks. 
Enable DNS64 Info 
Cancel 
Save
</pre>
</details>

Similarly, lets create private subnet in another AZ

<details><summary>Screenshot 12: accessible text</summary>

<pre>
Subnet settings 
Specify the CIDR blocks and Availability Zone for the subnet. 
Subnet 1 of 1 
Subnet name 
Create a tag with a key of &#x27;Name&#x27; and a value that you specify. 
private-subnet-1b 
The name can be up to 256 characters long. 
Availability Zone Info 
Choose the zone in which your subnet will reside, or let Amazon choose one for you. 
Asia Pacific (Mumbai) / aps1-az3 (ap-south-1b) 
IPV4 VPC CIDR block Info 
Choose the VPC&#x27;s IPv4 CIDR block for the subnet. The subnet&#x27;s IPV4 CIDR must lie within this block. 
192.168.0.0/23 
IPv4 subnet CIDR block 
192.168.0.128/25 
128 IP 
&lt; 
1 
&gt; 
&lt; 
Tags - optional 
Key 
Value - optional 
Q Name 
X 
Q private-subnet-1b 
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

Any name

<details><summary>Screenshot 13: accessible text</summary>

<pre>
public-subnet-1a 
subnet-06f64e033b2a4addc 
Available 
vpc-Oe288bbc758779d85 | vpc -... 
Off 
192.168.0.0/25 
- 
- 
private-subnet-1b 
subnet-0c36555e6d75397d8 
Available 
vpc-Oe288bbc758779d85 | vpc -... 
Off 
192.168.0.128/25 
1
</pre>
</details>

For our Private Subnet, we don’t need Public IPv4 address for EC2 instances.

Pls Note:   
Route table for each Subnet, we will set up later after set up of Internet Gateway and NAT Gateway.

### Internet gateway

Lets set up: INTERNET GATEWAY

AWS Region

VPC (CIDR: 192.168.0.0/23)

AZ-1a

Default Route Table

Destination

Target

Public Subnet-1a

192.168.0.0/23

local

Private IP: 192.168.0.0/25

public  Route Table

Destination

Target

Igw-0abc

NAT

Gateway

0.0.0.0/0

ID: Igw-0abc…

Internet

Gateway

Private IP: ….

Public IP: …..

Public IP: xyz

AZ-1b

Private Subnet-1b

Private IP: 192.168.0.128/25

private Route Table

Destination

Target

nat-1ab1c

0.0.0.0/0

EC2 instance

Internet Gateway: it’s a gate between our VPC and internet

2 things are required for anyone to talk to Internet Gateway:

Subnet should have Internet Gateway route in its Route Table

Host should have Public IP

VPC -> Internet Gateways

<details><summary>Screenshot 14: accessible text</summary>

<pre>
VPC dashboard 
‹ 
Create VPC 
Launch EC2 Instances 
AWS Global View La 
Note: Your Instances will launch in the Asia Pacific region. 
Filter by VPC: 
Resources by Region 
C Refresh Resources 
You are using the following Amazon VPC resources 
Virtual private cloud 
Your VPCs 
VPCS 
Mumbai 2 
Endpoint Services 
Mumbai 0 
Subnets 
&gt; See all regions 
See all regions 
Route tables 
Internet gateways 
Subnets 
Mumbai 3 
NAT Gateways 
Mumbai 0 
Egress-only Internet 
gateways 
See all regions 
See all regions 
DHCP option sets 
Elastic IPs 
Route Tables 
Mumbai 2 
VPC Peering Connections 
Mumbai 0 
Managed prefix lists 
› See all regions 
See all regions 
NAT gateways
</pre>
</details>

Create Internet Gateway

<details><summary>Screenshot 15: accessible text</summary>

<pre>
Internet gateways (1) Info 
Actions 
Create internet gateway 
Q Find internet gateways by attribute or tag 
&lt; 1 &gt; @ 
D 
|Name 
Internet gateway ID 
State 
V VPC ID 
| Owner 
D 
D 
igw-0611fa34273f92d5f 
Attached 
vpc-06a42df24a27c3d21 
643537035131
</pre>
</details>

Name -> create Internet Gateway

<details><summary>Screenshot 16: accessible text</summary>

<pre>
= VPC &gt; Internet gateways &gt; Create internet gateway 
Create internet gateway Info 
An internet gateway is a virtual router that connects a VPC to the internet. To create a new internet gateway specify the name for the gateway below. 
Internet gateway settings 
Name tag 
Creates a tag with a key of &#x27;Name&#x27; and a value that you specify. 
conceptsbyshrayansh-internetgateway 
Tags - optional 
A tag is a label that you assign to an AWS resource. Each tag consists of a key and an optional value. You can use tags to search and filter your resources or track your AWS costs. 
Key 
Value - optional 
Q Name 
X 
Q conceptsbyshrayansh-internetgateway 
X 
Remove 
Add new tag 
You can add 49 more tags. 
Cancel 
Create internet gateway
</pre>
</details>

Our newly created Internet Gateway -> Attach to VPC

<details><summary>Screenshot 17: accessible text</summary>

<pre>
The following internet gateway was created: igw-Of46d967b76d8d807 - conceptsbyshrayansh-internetgateway. You can now attach to a VPC to enable the VPC to communicate with the internet. 
Attach to a VPC 
× 
igw-Of46d967b76d8d807 / conceptsbyshrayansh-internetgateway 
Actions 
Attach to VPC 
Details Info 
Detach from VPC 
Internet gateway ID 
State 
VPC ID 
Owner 
igw-Of46d967b76d8d807 
Detached 
643537035131 
Manage tags 
Delete 
Tags (1) 
Manage tags 
Q Search tags 
&lt; 1 &gt; @ 
- 
Key 
Value 
Name 
conceptsbyshrayansh-internetgateway
</pre>
</details>

Select our VPC and Click Attach

<details><summary>Screenshot 18: accessible text</summary>

<pre>
= VPC &gt; Internet gateways &gt; Attach to VPC (igw-0f46d967b76d8d807) 
Attach to VPC (igw-0f46d967b76d8d807) Info 
VPC 
Attach an internet gateway to a VPC to enable the VPC to communicate with the internet. Specify the VPC to attach below. 
Available VPCs 
Attach the internet gateway to this VPC. 
Q vpc-0e288bbc758779d85 
X 
AWS Command Line Interface command 
Cancel 
Attach internet gateway
</pre>
</details>

### NAT gateway

Lets set up: NAT Gateway

AWS Region

VPC (CIDR: 192.168.0.0/23)

AZ-1a

Default Route Table

Destination

Target

Public Subnet-1a

192.168.0.0/23

local

Private IP: 192.168.0.0/25

public  Route Table

Destination

Target

Igw-0abc

NAT

Gateway

0.0.0.0/0

ID: Igw-0abc…

Internet

Gateway

Private IP: ….

Public IP: …..

Public IP: xyz

AZ-1b

Private Subnet-1b

Private IP: 192.168.0.128/25

private Route Table

Destination

Target

nat-1ab1c

0.0.0.0/0

EC2 instance

NAT Gateway:

It’s a gate between Private Network and Public Network.

Removes sender private IP (192.168.0.128) and replaces it with its own Public IP.

Sends it out through the Internet Gateway to the web.

When the response comes back, the NAT remembers who originally asked for it and forwards it back down the chain.

Another main Advantage of NAT is, its only support OUTGOING

Means, any packet coming from internet is DROPPED if there is no mapping present within NAT mapping table.

Or in other words

Talk must be first initiated by private network host only. Then only NAT will have entry in its Mapping table.

Remember this diagram from PART-1

My home Network

Routing Table

Destination

Next Hop

8.8.8.8:443

Internet Gateway IP address

Packet:

Source: 10.0.1.10

Source port: 54321

Destination: 8.8.8.8:443 (google)

Destination port: 443

Mapping Table

Host1

Specialized Router

With NAT

52.10.20.30:40001

10.0.1.10:54321

Private IP address:

10.0.1.10

Public IP address:

52.10.20.30

Packet:

Source: 52.10.20.30

Source port: 40001

Destination: 8.8.8.8:443 (google)

Destination port: 443

Notice that Source IP and port number is changed.

From host Private IP to NAT public IP and port

Internet Gateway   
(Specialized Router)

Internet

VPC -> NAT Gateways

<details><summary>Screenshot 19: accessible text</summary>

<pre>
V 
VPC dashboard 
Create VPC 
Launch EC2 Instances 
Note: Your Instances will launch in the Asia Pacific region. 
AWS Global View [ 
Filter by VPC: 
Resources by Region 
C Refresh Resources 
You are using the following Amazon VPC resources 
Virtual private cloud 
Your VPCs 
VPCs 
Mumbai 2 
Endpoint Services 
Mumbai 0 
Subnets 
See all regions 
See all regions 
Route tables 
Internet gateways 
Subnets 
Mumbai 5 
NAT Gateways 
Mumbai 0 
Egress-only Internet 
See all regions 
gateways 
See all regions 
DHCP option sets 
Elastic IPs 
Route Tables 
Mumbai 2 
VPC Peering Connections 
Mumbai 0 
Managed prefix lists 
See all regions 
See all regions 
NAT gateways
</pre>
</details>

Create NAT Gateway

<details><summary>Screenshot 20: accessible text</summary>

<pre>
NAT gateways Info 
Actions V 
Create NAT gateway 
Q Find NAT gateways by attribute or tag 
&lt; 1 &gt; 
Name 
NAT gateway ID 
Connectivity ... 
State 
State message 
Availability ... 
Route table ID 
Primary public I ... 
Primary private l ... 
Prim 
D 
D 
D 
D 
No NAT gateways found
</pre>
</details>

Enter details:

NOTE: NAT needs to talk with Internet gateway that’s why it should be present in Public Subnet only.

Provide the name for NAT Gateway

<details><summary>Screenshot 21: accessible text</summary>

<pre>
NAT gateway settings 
Name - optional 
Create a tag with a key of &#x27;Name&#x27; and a value that you specify. 
conceptsbyshrayansh-NAT 
The name can be up to 256 characters long. 
Availability mode Info 
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
Connectivity type 
Select a connectivity type for the NAT gateway. 
Public 
Private 
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
You can add 49 more tags. 
Cancel 
Create NAT gateway
</pre>
</details>

Manually select the public subnet in which we want to put the NAT Gateway

Allocate Elastic IP, this will provide stable public IP address.   
-  it does not change even if service restarted.

Why permanent bcoz, when a website replies it send back to NAT address, but if NAT public address changes frequently then its an issue.

NAT Gateway need to talk to Internet Gateway

That’s why we need to put it inside PUBLIC SUBNET

Pls Note:

NAT Gateway and Elastic IP is both chargeable. So delete it if you don't intent to use it.

### Route tables

Lets configure Public Routing Table:

AWS Region

VPC (CIDR: 192.168.0.0/23)

AZ-1a

Default Route Table

Destination

Target

Public Subnet-1a

192.168.0.0/23

local

Private IP: 192.168.0.0/25

public  Route Table

Destination

Target

Igw-0abc

NAT

Gateway

0.0.0.0/0

ID: Igw-0abc…

Internet

Gateway

Private IP: ….

Public IP: …..

Public IP: xyz

AZ-1b

Private Subnet-1b

Private IP: 192.168.0.128/25

private Route Table

Destination

Target

nat-1ab1c

0.0.0.0/0

EC2 instance

VPC -> Route tables

<details><summary>Screenshot 22: accessible text</summary>

<pre>
V 
VPC dashboard 
Create VPC 
Launch EC2 Instances 
Note: Your Instances will launch in the Asia Pacific region. 
AWS Global View [ 
Filter by VPC: 
Resources by Region 
C Refresh Resources 
You are using the following Amazon VPC resources 
Virtual private cloud 
Your VPCs 
VPCs 
Mumbai 2 
Endpoint Services 
Mumbai 0 
Subnets 
See all regions 
See all regions 
Route tables 
Internet gateways 
Subnets 
Mumbai 5 
NAT Gateways 
Mumbai 0 
Egress-only Internet 
See all regions 
gateways 
See all regions 
DHCP option sets 
Elastic IPs 
Route Tables 
Mumbai 2 
VPC Peering Connections 
Mumbai 0 
Managed prefix lists 
See all regions 
See all regions 
NAT gateways
</pre>
</details>

Create Route table

<details><summary>Screenshot 23: accessible text</summary>

<pre>
Route tables (1/2) Info 
Last updated 
1 minute ago 
Actions 
Create route table 
Q Find route tables by attribute or tag 
&lt; 1 &gt; 
Name 
Route table ID 
Explicit subnet associ ... 
Edge associations V 
Main 
VPC 
D 
Owner ID 
D 
D 
&gt; 
rtb-064c0c3c03f4370fe 
Yes 
vpc-0e288bbc758779d85 | vpc -... 
643537035131 
- 
rtb-037435253c27c04e6 
Yes 
vpc-06a42df24a27c3d21 
643537035131
</pre>
</details>

Provide name and choose VPC -> Click create route table

<details><summary>Screenshot 24: accessible text</summary>

<pre>
Create route table Info 
A route table specifies how packets are forwarded between the subnets within your VPC, the internet, and your VPN connection. 
Route table settings 
Name - optional 
Create a tag with a key of &#x27;Name&#x27; and a value that you specify. 
public-subnet-routetable 
VPC 
The VPC to use for this route table. 
vpc-Oe288bbc758779d85 (vpc-conceptsbyshrayansh-prod) 
Tags 
A tag is a label that you assign to an AWS resource. Each tag consists of a key and an optional value. You can use tags to search and filter your resources or track your AWS costs. 
Key 
Value - optional 
Q Name 
X 
Q public-subnet-routetable 
X 
Remove 
Add new tag 
You can add 49 more tags. 
Cancel 
Create route table
</pre>
</details>

Since it’s a public Route table -> need to add new entry in it for Internet Gateway

<details><summary>Screenshot 25: accessible text</summary>

<pre>
Route table rtb-031baf5458b24d0d4 | public-subnet-routetable was created successfully. 
× 
rtb-031baf5458b24d0d4 / public-subnet-routetable 
Actions 
Details Info 
Route table ID 
Main 
Explicit subnet associations 
Edge associations 
rtb-031baf5458b24d0d4 
No 
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
V 
Q Filter routes 
1 &gt; 
D 
D 
Destination 
Target 
Status 
Propagated 
Route Origin 
D 
D 
192.168.0.0/23 
local 
Active 
No 
Create Route Table
</pre>
</details>

<details><summary>Screenshot 26: accessible text</summary>

<pre>
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
Add route 
Cancel 
Preview 
Save changes
</pre>
</details>

Add new route for Internet Gateway

<details><summary>Screenshot 27: accessible text</summary>

<pre>
VPC &gt; Route tables &gt; rtb-031baf5458b24d0d4 
&gt; Edit routes 
Edit routes 
Destination 
Target 
Status 
Propagated 
Route Origin 
192.168.0.0/23 
local 
7 
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

Now we need to connect this Route Table with our Subnet   
   
Action -> Edit subnet associations

<details><summary>Screenshot 28: accessible text</summary>

<pre>
Updated routes for rtb-031baf5458b24d0d4 / public-subnet-routetable successfully 
× 
Details 
rtb-031baf5458b24d0d4 / public-subnet-routetable 
Actions 
Set main route table 
Edit subnet associations 
Details Info 
Edit edge associations 
Route table ID 
Main 
Explicit subnet associations 
Edge associations 
rtb-031baf5458b24d0d4 
No 
subnet-06f64e033b2a4addc / public-subnet-1a 
Edit route propagation 
Edit routes 
VPC 
Owner ID 
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
Both 
Edit routes 
V 
Q Filter routes 
1 
Destination 
Target 
Status 
Propagated 
Route Origin 
D 
D 
D 
D 
D 
0.0.0.0/0 
igw-Of46d967b76d8d807 
Active 
No 
Create Route 
192.168.0.0/23 
local 
Active 
No 
Create Route Table
</pre>
</details>

Select with which Subnet we need to connect this Route table

<details><summary>Screenshot 29: accessible text</summary>

<pre>
Edit subnet associations 
Change which subnets are associated with this route table. 
Available subnets (1/2) 
Q Filter subnet associations 
&lt; 1 &gt; @ 
D 
Name 
Subnet ID 
IPV6 CIDR 
Route table ID 
D 
IPV4 CIDR 
public-subnet-1a 
subnet-06f64e033b2a4addc 
192.168.0.0/25 
Main (rtb-064cl 
private-subnet-1b 
subnet-0c36555e6d75397d8 
192.168.0.128/25 
Main (rtb-064c 
Selected subnets 
subnet-06f64e033b2a4addc / public-subnet-1a X 
Cancel 
Save associations
</pre>
</details>

Public Route table is also set.

AWS Region

VPC (CIDR: 192.168.0.0/23)

AZ-1a

Default Route Table

Destination

Target

Public Subnet-1a

192.168.0.0/23

local

Private IP: 192.168.0.0/25

public  Route Table

Destination

Target

Igw-0abc

NAT

Gateway

0.0.0.0/0

ID: Igw-0abc…

Internet

Gateway

Private IP: ….

Public IP: …..

Public IP: xyz

AZ-1b

Private Subnet-1b

Private IP: 192.168.0.128/25

private Route Table

Destination

Target

nat-1ab1c

0.0.0.0/0

EC2 instance

now only Private Route table we need to set.

Provide name and choose VPC -> Click create route table -> provide Name and Select VPC

<details><summary>Screenshot 30: accessible text</summary>

<pre>
Create route table Info 
A route table specifies how packets are forwarded between the subnets within your VPC, the internet, and your VPN connection. 
Route table settings 
Name - optional 
Create a tag with a key of &#x27;Name&#x27; and a value that you specify. 
private-subnet-routetable 
VPC 
The VPC to use for this route table. 
vpc-0e288bbc758779d85 (vpc-conceptsbyshrayansh-prod) 
Tags 
A tag is a label that you assign to an AWS resource. Each tag consists of a key and an optional value. You can use tags to search and filter your resources or track your AWS costs. 
Key 
Value - optional 
Q Name 
X 
Q private-subnet-routetable 
X 
Remove 
Add new tag 
You can add 49 more tags. 
Cancel 
Create route table
</pre>
</details>

Since this Route table is for Private Subnet, in this route table we need to add entry for NAT Gateway

<details><summary>Screenshot 31: accessible text</summary>

<pre>
Route table rtb-07ae65c12d63da233 | private-subnet-routetable was created successfully. 
× 
rtb-07ae65c12d63da233 / private-subnet-routetable 
Actions V 
Details Info 
Route table ID 
Main 
Explicit subnet associations 
Edge associations 
rtb-07ae65c12d63da233 
No 
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
Both V 
Edit routes 
V 
Q Filter routes 
1 &gt; 
Destination 
7 |Target 
Status 
Propagated 
Route Origin 
D 
192.168.0.0/23 
local 
Active 
No 
Create Route Table
</pre>
</details>

<details><summary>Screenshot 32: accessible text</summary>

<pre>
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
Q local 
X 
NAT Gateway 
No 
Q 0.0.0.0/0 
X 
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

Lets associate this Route table with our private Subnet.

<details><summary>Screenshot 33: accessible text</summary>

<pre>
Updated routes for rtb-07ae65c12d63da233 / private-subnet-routetable successfully 
X 
&gt; Details 
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
VPC 
Edit routes 
Owner ID 
vpc-Oe288bbc758779d85 | vpc-conceptsbyshrayansh- 
643537035131 
Manage tags 
orod 
Delete 
Routes 
Subnet associations 
Edge associations 
Route propagation 
Tags 
Routes (2) 
Both V 
Edit routes 
Q Filter routes 
&lt; 
1 &gt; 
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
</pre>
</details>

<details><summary>Screenshot 34: accessible text</summary>

<pre>
Edit subnet associations 
Change which subnets are associated with this route table. 
Available subnets (1/2) 
Q Filter subnet associations 
&lt; 1 &gt; @ 
IPV4 CIDR 
D 
Name 
Subnet ID 
| IPV6 CIDR 
Route table ID 
D 
public-subnet-1a 
subnet-06f64e033b2a4addc 
192.168.0.0/25 
rtb-031baf545 
private-subnet-1b 
subnet-0c36555e6d75397d8 
192.168.0.128/25 
Main (rtb-064cl 
Selected subnets 
subnet-0c36555e6d75397d8 / private-subnet-1b X 
Cancel 
Save associations
</pre>
</details>

DONE

AWS Region

VPC (CIDR: 192.168.0.0/23)

AZ-1a

Default Route Table

Destination

Target

Public Subnet-1a

192.168.0.0/23

local

Private IP: 192.168.0.0/25

public  Route Table

Destination

Target

Igw-0abc

NAT

Gateway

0.0.0.0/0

ID: Igw-0abc…

Internet

Gateway

Private IP: ….

Public IP: …..

Public IP: xyz

AZ-1b

Private Subnet-1b

Private IP: 192.168.0.128/25

private Route Table

Destination

Target

nat-1ab1c

0.0.0.0/0

EC2 instance

### Verify the VPC

Lets verify this:

VPC  -> Select our VPC

<details><summary>Screenshot 35: accessible text</summary>

<pre>
VPC &gt; Your VPCs 
VPC dashboard 
‹ 
Your VPCs 
AWS Global View L 
VPCs 
VPC encryption controls 
Filter by VPC: 
Your VPCs (1/2) Info 
Last updated 
less than a minute ago 
Actions 
Create VPC 
Virtual private cloud 
Q Find VPCs by attribute or tag 
&lt; 1 &gt; 
D 
Your VPCs 
Name 
VPC ID 
0 
State 
Encryption c ... 
Encryption control ... 
Block Public ... 
IPV4 CIDR 
Pv6 CIDR 
Subnets 
vpc-06a42df24a27c3d21 
Available 
O Off 
172.31.0.0/16 
- 
Route tables 
vpc-conceptsbyshrayansh-prod 
vpc-0e288bbc758779d85 
Available 
Off 
192.168.0.0/23 
Internet gateways 
Egress-only Internet 
gateways 
DHCP option sets 
Elastic IPs
</pre>
</details>

<details><summary>Screenshot 36: accessible text</summary>

<pre>
vpc-0e288bbc758779d85 / vpc-conceptsbyshrayansh-prod 
Actions 
Details Info 
VPC ID 
State 
Block Public Access 
DNS hostnames 
vpc-0e288bbc758779d85 
Available 
Off 
Disabled 
DNS resolution 
Tenancy 
DHCP option set 
Main route table 
Enabled 
default 
dopt-00ecZac7fa4633257 
rtb-064c0c3c03f4370fe 
Main network ACL 
Default VPC 
IPV4 CIDR 
IPv6 pool 
acl-00683e4ac64f10b5d 
No 
192.168.0.0/23 
IPV6 CIDR (Network border group) 
Network Address Usage metrics 
Route 53 Resolver DNS Firewall rule groups 
Owner ID 
Disabled 
643537035131 
Encryption control ID 
Encryption control mode 
Resource map 
CIDRS 
Flow logs 
Tags 
Integrations 
Resource map Info 
Show all details 
VPC 
Subnets (2) 
Route tables (3) 
Network Connections (2) 
Your AWS virtual network 
Subnets within this VPC 
Route network traffic to resources 
Connections to other networks 
vpc-conceptsbyshrayansh-prod 
ap-south-1a 
rtb-064c0c3c03f4370fe 
conceptsbyshrayansh-internetgateway 
A public-subnet-1a 
192.168.0.0/25 
public-subnet-routetable 
conceptsbyshrayansh-NAT 
No IPv6 
private-subnet-routetable 
ap-south-1b 
B private-subnet-1b
</pre>
</details>

[AWS index](./index.md) · [← AWS Networking - Part1](./AWS%20Networking%20-%20Part1.md) · [AWS Networking - Part3 →](./AWS%20Networking%20-%20Part3.md)

*Screenshots are represented by their OneNote accessible text. The original bitmap images are not embedded; image text may contain recognition errors.*
