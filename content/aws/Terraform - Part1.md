---
title: "Terraform - Part1"
tags:
  - aws
  - terraform
---

# Terraform - Part1

[AWS index](./index.md) · [← AWS Networking - Part3](./AWS%20Networking%20-%20Part3.md) · [Terraform - Part2 →](./Terraform%20-%20Part2.md)

*OneNote page: Saturday, 4 July 2026 11:08 AM*

## At a glance

- Terraform describes infrastructure in files and calls AWS APIs to create it.
- The VPC diagram provides the target architecture for the next page.

## On this page

- [Why Terraform](#why-terraform)
- [Target infrastructure](#target-infrastructure)
- [Terraform resource reference](#terraform-resource-reference)

## Complete page transcription

### Why Terraform

The Problem Terraform Solves:   
-------------------------------------------

We have been creating AWS resources manually (clicking through the AWS Console). Now we want to automate this.

Terraform is a tool that lets us write instructions for creating AWS resources, instead of clicking.

So in simple term, using Terraform:   
   
We can write instructions --> Terraform reads them --> Terraform calls AWS API --> AWS creates resources

### Target infrastructure

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

### Terraform resource reference

<details><summary>Screenshot 2: accessible text</summary>

<pre>
aws_nat_gateway 
aws_nat_gateway_eip_association 
aws_network_acl 
aws_network_acl_association 
aws_network_acl_rule 
aws_network_interface 
aws_network_interface_attachment 
aws_network_interface_permission 
aws_network_interface_sg_attachment 
aws_route 
aws_route_table 
aws_route_table_association 
aws_security_group 
aws_security_group_rule 
aws_subnet 
aws_vpc 
aws_vpc_block_public_access_ 
exclusion 
aws_vpc_block_public_access_options
</pre>
</details>

[AWS index](./index.md) · [← AWS Networking - Part3](./AWS%20Networking%20-%20Part3.md) · [Terraform - Part2 →](./Terraform%20-%20Part2.md)

*Screenshots are represented by their OneNote accessible text. The original bitmap images are not embedded; image text may contain recognition errors.*
