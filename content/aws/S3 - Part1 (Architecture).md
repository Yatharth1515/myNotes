---
title: "S3 - Part1 (Architecture)"
tags:
  - aws
  - storage
---

# S3 - Part1 (Architecture)

[AWS index](./index.md) · [← EBS (Elastic Block Store)](./EBS%20%28Elastic%20Block%20Store%29.md) · [S3 - Part2 (Springboot <---> S3 integration) →](./S3%20-%20Part2%20%28Springboot%20and%20S3%20integration%29.md)

*OneNote page: Saturday, 12 September 2026 10:55 AM*

## At a glance

- The note starts from the fixed size of an EC2 disk and introduces S3.
- The bucket screens cover region, object ownership, versioning and encryption.

## On this page

- [Why S3](#why-s3)
- [Create and configure a bucket](#create-and-configure-a-bucket)

## Complete page transcription

### Why S3

Let's see what is S3 and why it is needed in the first place

Problem

1.Fixed disk size

EC2

Disk

EBS (200GB)

### Create and configure a bucket

<details><summary>Screenshot 1: accessible text</summary>

<pre>
Create bucket Info 
Buckets are containers for data stored in S3. 
General configuration 
AWS Region 
Asia Pacific (Mumbai) ap-south-1 
Bucket type 
Info 
General purpose 
Directory 
Recommended for most use cases and access patterns. General purpose buckets are the original S3 bucket type. They allow a mix of 
Recommended for low-latency use cases 
storage classes that redundantly store objects across multiple Availability Zones. 
processing of data within a single Availa 
Bucket namespace 
Choose the namespace where you want to create your bucket. Learn more La 
Global namespace 
By default, S3 creates general purpose buckets in the global namespace. 
Account Regional namespace (recommended) 
General purpose buckets created in your account Regional namespace are unique to your account. These buckets can never be created by another AWS account. 
Bucket name 
Info 
conceptsbyshrayansh-demo-files 
Bucket names must be 3 to 63 characters and unique within the global namespace. Bucket names must also begin and end with a letter or number. Valid characters are a-z, 0-9, periods (.), and 
Copy settings from existing bucket - optional 
Only the bucket settings in the following configuration are copied. 
Choose bucket 
Format: s3://bucket/prefix
</pre>
</details>

<details><summary>Screenshot 2: accessible text</summary>

<pre>
Object Ownership Info 
Control ownership of objects written to this bucket from other AWS accounts and the use of access control lists (ACLs). Object ownership determines who can specify access to objects. 
Object Ownership 
O ACLs disabled (recommended) 
O ACLs enabled 
All objects in this bucket are owned by this account. Access to this bucket and its 
Objects in this bucket can be owned by other AWS accounts. Access to this bucket 
objects is specified using only policies. 
and its objects can be specified using ACLs. 
Object Ownership 
Bucket owner enforced 
Block Public Access settings for this bucket 
Public access is granted to buckets and objects through access control lists (ACLs), bucket policies, access point policies, or all. In order to ensure that public access to this bucket and its o 
apply only to this bucket and its access points. AWS recommends that you turn on Block all public access, but before applying any of these settings, ensure that your applications will wo 
public access to this bucket or objects within, you can customize the individual settings below to suit your specific storage use cases. Learn more 
Block all public access 
&gt; 
Turning this setting on is the same as turning on all four settings below. Each of the following settings are independent of one another. 
Block public access to buckets and objects granted through new access control lists (ACLs) 
S3 will block public access permissions applied to newly added buckets or objects, and prevent the creation of new public access ACLs for existing buckets and objects. This setting doesn&#x27;t change any existir 
Block public access to buckets and objects granted through any access control lists (ACLs) 
S3 will ignore all ACLs that grant public access to buckets and objects. 
Block public access to buckets and objects granted through new public bucket or access point policies 
S3 will block new bucket and access point policies that grant public access to buckets and objects. This setting doesn&#x27;t change any existing policies that allow public access to S3 resources. 
Block public and cross-account access to buckets and objects through any public bucket or access point policies 
S3 will ignore public and cross-account access for buckets or access points with policies that grant public access to buckets and objects.
</pre>
</details>

<details><summary>Screenshot 3: accessible text</summary>

<pre>
Bucket Versioning 
Versioning is a means of keeping multiple variants of an object in the same bucket. You can use versioning to preserve, retrieve, and restore every version of ev 
both unintended user actions and application failures. Learn more La 
Bucket Versioning 
Disable 
Enable 
Tags - optional 
You can use bucket tags to analyze, manage and specify permissions for a bucket. Learn more La 
You can use s3:ListTagsForResource, s3:TagResource, and s3:UntagResource APIs to manage tags on S3 general purpose buckets for access control in ad 
please provide permissions to s3:ListTagsForResource, s3:TagResource, and s3:UntagResource actions. Learn more 
No tags associated with this bucket. 
Add new tag 
You can add up to 50 tags.
</pre>
</details>

<details><summary>Screenshot 4: accessible text</summary>

<pre>
Default encryption Info 
Server-side encryption is automatically applied to new objects stored in this bucket. 
Encryption type |Info 
Secure your objects with two separate layers of encryption. For details on pricing, see DSSE-KMS pricing on the Storage tab of the Amazon S3 pricing page. L2 
Server-side encryption with Amazon S3 managed keys (SSE-S3) 
Server-side encryption with AWS Key Management Service keys (SSE-KMS) 
Dual-layer server-side encryption with AWS Key Management Service keys (DSSE-KMS) 
Bucket Key 
Using an S3 Bucket Key for SSE-KMS reduces encryption costs by lowering calls to AWS KMS. S3 Bucket Keys aren&#x27;t supported for DSSE-KMS. Learn more La 
Disable 
Enable 
Advanced settings 
After creating the bucket, you can upload files and folders to the bucket, and configure additional bucket settings. 
Cancel 
Create bucket
</pre>
</details>

[AWS index](./index.md) · [← EBS (Elastic Block Store)](./EBS%20%28Elastic%20Block%20Store%29.md) · [S3 - Part2 (Springboot <---> S3 integration) →](./S3%20-%20Part2%20%28Springboot%20and%20S3%20integration%29.md)

*Screenshots are represented by their OneNote accessible text. The original bitmap images are not embedded; image text may contain recognition errors.*
