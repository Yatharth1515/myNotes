---
title: AWS Notes
tags:
  - aws
---

# AWS Notes

Read these pages in order. Each note links to the previous and next page.

## Identity and networking

1. [IAM (User, Policy, Groups and Roles)](./IAM%20%28User%2C%20Policy%2C%20Groups%20and%20Roles%29.md)
2. [AWS Networking - Part1](./AWS%20Networking%20-%20Part1.md) — Networks connect hosts through switches and routers.
3. [AWS Networking - Part2](./AWS%20Networking%20-%20Part2.md) — A VPC defines the network range, then subnets divide it by availability zone.
4. [AWS Networking - Part3](./AWS%20Networking%20-%20Part3.md) — This walkthrough adds instance security groups and subnet network ACLs to the VPC.

## Infrastructure as code

5. [Terraform - Part1](./Terraform%20-%20Part1.md) — Terraform describes infrastructure in files and calls AWS APIs to create it.
6. [Terraform - Part2](./Terraform%20-%20Part2.md) — The page follows the VPC, subnet, gateway and route table setup in sequence.

## Compute and scaling

7. [EC2](./EC2.md) — EC2 is a virtual machine running in an AWS data center.
8. [Auto Scaling Group](./Auto%20Scaling%20Group.md) — An AMI captures the configured instance; a launch template defines how to start replacements.
9. [Application Load Balancer](./Application%20Load%20Balancer.md) — The ALB gives clients one entry point and forwards requests to healthy instances in a target group.

## Storage

10. [EBS (Elastic Block Store)](./EBS%20%28Elastic%20Block%20Store%29.md) — The screenshots create an EBS volume, attach it to EC2, format it and mount it.
11. [S3 - Part1 (Architecture)](./S3%20-%20Part1%20%28Architecture%29.md) — The note starts from the fixed size of an EC2 disk and introduces S3.
12. [S3 - Part2 (Springboot <---> S3 integration)](./S3%20-%20Part2%20%28Springboot%20and%20S3%20integration%29.md) — The page demonstrates S3 access from a Spring Boot application.

The screenshot sections include accessible text from OneNote. Open the source notebook to inspect the original diagrams and screenshots.
