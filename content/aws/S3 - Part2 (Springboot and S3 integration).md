---
title: "S3 - Part2 (Springboot <---> S3 integration)"
tags:
  - aws
  - storage
---

# S3 - Part2 (Springboot <---> S3 integration)

[AWS index](./index.md) · [← S3 - Part1 (Architecture)](./S3%20-%20Part1%20%28Architecture%29.md)

*OneNote page: Friday, 18 September 2026 1:37 PM*

## At a glance

- The page demonstrates S3 access from a Spring Boot application.
- It begins with access keys, then shows IAM policy and role based access with API tests.

## On this page

- [Spring Boot and S3](#spring-boot-and-s3)
- [Bucket setup](#bucket-setup)
- [IAM user and policies](#iam-user-and-policies)
- [API test](#api-test)
- [IAM role](#iam-role)
- [Role based test](#role-based-test)

## Complete page transcription

### Spring Boot and S3

Now will see, how we can integrate our Springboot Application with S3.

Approach1: Access Key / Token based authentication

<details><summary>Screenshot 1: accessible text</summary>

<pre>
#17 
AWS 
S3 (Simple Storage Sa 
Part1 - Architecture
</pre>
</details>

### Bucket setup

<details><summary>Screenshot 2: accessible text</summary>

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

<details><summary>Screenshot 3: accessible text</summary>

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

<details><summary>Screenshot 4: accessible text</summary>

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

<details><summary>Screenshot 5: accessible text</summary>

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

### IAM user and policies

<details><summary>Screenshot 6: accessible text</summary>

<pre>
#4 
AWS 
IAM USER 
.. 
--- 
.: 
EXPLAINED WITH DEMO
</pre>
</details>

<details><summary>Screenshot 7: accessible text</summary>

<pre>
#5 
AWS 
IAM 
POLICIES
</pre>
</details>

### API test

<details><summary>Screenshot 8: accessible text</summary>

<pre>
HTTP 
http://localhost:8081/api/orders/with-key 
Save 
&gt; 
POST 
V 
http://localhost:8080/s3/upload 
Send 
Params 
Authorization 
Headers (8) 
Body . 
Pre-request Script 
Tests 
Settings 
Cookies 
none 
form-data 
x-www-form-urlencoded 
raw 
binary 
Key 
Value 
000 
Bulk Edit 
file 
s3part1thumb.jpg X 
A 
Key 
Value 
Body 
Cookies 
Headers (5) 
Test Results 
Status: 200 OK Time: 2.09 s Size: 190 B 
Save Response v 
Pretty 
Raw 
Preview 
Visualize 
Text 
V 
1 
Uploaded: s3part1thumb.jpg
</pre>
</details>

<details><summary>Screenshot 9: accessible text</summary>

<pre>
conceptsbyshrayansh-demo Info 
Objects 
Metadata 
Properties 
Permissions 
Metrics 
Management 
File systems 
Access Points 
Objects (1) 
Copy S3 URI 
Copy URL 
~ Download 
Open La 
Delete 
Actions 
Create folder 
7 Upload 
Objects are the fundamental entities stored in Amazon S3. You can use Amazon S3 inventory La to get a list of all objects in your bucket. For others to access your objects, you&#x27;ll need to 
explicitly grant them permissions. Learn more La 
Q Find objects by prefix 
1 
Last modified 
Size 
Storage class 
D 
D 
Name 
Type 
D 
s3part1thumb.jpg 
jpg 
September 18, 2026, 22:23:08 
(UTC+05:30) 
1.5 MB 
Standard
</pre>
</details>

### IAM role

<details><summary>Screenshot 10: accessible text</summary>

<pre>
#6 
AWS 
IAM ROLE 
Trust Policy 
Permission Policy 
Assume Role
</pre>
</details>

### Role based test

<details><summary>Screenshot 11: accessible text</summary>

<pre>
HTTP 
http://localhost:8081/api/orders/with-key 
Save 
&lt;/&gt; 
Send 
&gt; 
POST 
V 
http://13.206.221.46:8081/s3/upload 
Params 
Authorization 
Headers (8) 
Body . 
Pre-request Script 
Tests 
Settings 
Cookies 
none 
form-data 
x-www-form-urlencoded 
raw 
binary 
Key 
Value 
000 
Bulk Edit 
file 
windowshortthumb.png X 
A 
Key 
Value 
Body 
Cookies 
Headers (5) 
Test Results 
Status: 200 OK Time: 1426 ms Size: 194 B 
Save Response v 
Pretty 
Raw 
Preview 
Visualize 
Text 
V 
Q 
1 
Uploaded: windowshortthumb.png
</pre>
</details>

<details><summary>Screenshot 12: accessible text</summary>

<pre>
conceptsbyshrayansh-demo Info 
Objects 
Metadata 
Properties 
Permissions 
Metrics 
Management 
File systems 
Access Points 
Objects (1) 
Copy S3 URI 
Copy URL 
&amp; Download 
Objects are the fundamental entities stored in Amazon S3. You can use Amazon S3 inventory La to get a list of all objects in your bucket. For others 
more La 
Q Find objects by prefix 
Name 
Type 
Last modified 
Siz 
D 
windowshortthumb.png 
png 
September 19, 2026, 17:33:26 
(UTC+05:30)
</pre>
</details>

[AWS index](./index.md) · [← S3 - Part1 (Architecture)](./S3%20-%20Part1%20%28Architecture%29.md)

*Screenshots are represented by their OneNote accessible text. The original bitmap images are not embedded; image text may contain recognition errors.*
