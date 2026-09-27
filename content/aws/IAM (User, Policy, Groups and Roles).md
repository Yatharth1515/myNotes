---
title: "IAM (User, Policy, Groups and Roles)"
tags:
  - aws
  - iam
---

# IAM (User, Policy, Groups and Roles)

[AWS index](./index.md) · [AWS Networking - Part1 →](./AWS%20Networking%20-%20Part1.md)

## On this page

- [IAM and access decisions](#iam-identity-and-access-management)
- [IAM entities](#iam-entities)
- [IAM user](#1-iam-user)
- [IAM policy](#2-iam-policy)
- [IAM group](#3-iam-group)
- [IAM role](#4-iam-role)

Wednesday, 27 May 2026  ·  11:05 AM

## IAM (Identity and Access Management)

Every API call in AWS ask the same question:

Identity X wants to perform Action Y on Resource Z. Allow or Deny?

| WHO (Identity) | WHAT (service:API Operation) | WHICH (Resource) |
| --- | --- | --- |
| User, etc. wants | S3:GetObject; S3:PutObject; DynamoDB:PutItem; etc. | on bucket-prod/videos/* |

Like:

User "Shrayansh" wants to invoke S3 GetObject API on bucket-prod/videos/*. Should be allow or deny?

very Important:

In AWS, every API call is DENIED by-default.

So, If we want someone to access resource, then we have to explicitly grant the permission.

And this is where IAM comes into the picture.

Till now the account setup which we have done, looks something like this:

1 account has resources like:

Root User

With: 
Username

Password

S3,

EC2,

DynamoDB,

Lambda etc.

has full access

Root User has unlimited power, it even can delete the account too.

That’s why using the Root User credentials across a team is extremely risky because everyone gets unrestricted access with no proper isolation or accountability.

Team

Developer_1

Developer_2

DevOps_1

DevOps_2

Finance Person

Need permissions to debug app

and restart EC2 instances but

should not be allowed to delete

S3 bucket or DB.

Need to provide read-only access

to billing and cost-management

information.

Need permissions to provision and manage infrastructure

such as EC2 instances, Load Balancers etc.

So, a single Root User is not sufficient for a real organization.

We need multiple identities, each with precise permissions, that can be managed independently.

and that is exactly what IAM provides.

## IAM Entities:

USER · POLICY · GROUP · ROLE

## 1. IAM User

- User represents either:

- Human

- or long lived program

- Each User has:

- ARN (Amazon Resource Name), unique identifier for the resource.

- Credentials:

- Username / Password for Console login

- Access Key ID + Secret Key (for SDK)

- Permissions

Lets Create User:

Go to IAM -> Users -> Click Create User

<details><summary>Screenshot 1 — transcribed text</summary>

<pre>
IAM &gt; IAM users 
Identity and Access 
&lt; 
Management (IAM) 
IAM users (O) Info 
G 
Delete 
Create user 
An IAM user is an identity with long-term credentials that is used to interact with AWS in an account. 
Q Search IAM 
Q Search 
1 &gt; 
Dashboard 
User name 
Path 
Group: V 
Last activity 
MFA 
Password age 
Console last sign-in 
Access key ID 
Active key age 
Acce: 
D 
D 
D 
D 
D 
D 
D 
Access Management 
Roles 
No resources to display 
Policies 
IAM users
</pre>
</details>

Provide Username -> Click Next

<details><summary>Screenshot 2 — transcribed text</summary>

<pre>
IAM &gt; IAM users 
Create user 
Step 1 
Specify user details 
Specify user details 
Step 2 
Set permissions 
User details 
tep 3 
Review and create 
User name 
Shrayansh 
The user name can have up to 64 characters. Valid characters: A-Z, a-z, 0-9, and + = , . @ _ - (hyphen) 
Provide user access to the AWS Management Console - optional 
In addition to console access, users with SignInLocalDevelopmentAccess permissions can use the same console credentials for programmatic access 
without the need for access keys. 
If you are creating programmatic access through access keys or service-specific credentials for AWS CodeCommit or Amazon Keyspaces, you can generate them after you create this IAM user. 
Learn more L 
Cancel 
Next
</pre>
</details>

Simply click Next (Group, Policy, Permission boundary will cover later in IAM)

<details><summary>Screenshot 3 — transcribed text</summary>

<pre>
IAM &gt; IAM users &gt; Create user 
III 
Step 
Specify user details 
Set permissions 
Add user to an existing group or create a new one. Using groups is a best-practice way to manage user&#x27;s permissions by job functions. Learn more L 
Step 2 
Set permissions 
Permissions options 
Step 3 
Review and create 
O Add user to group 
Copy permissions 
Attach policies directly 
Add user to an existing group, or create a new group. We 
Copy all group memberships, attached managed policies, and inline 
Attach a managed policy directly to a user. As a best practice, we 
recommend using groups to manage user permissions by job 
policies from an existing user. 
recommend attaching policies to a group instead. Then, add the 
function. 
user to the appropriate group. 
Get started with groups 
Create group 
Create a group and select policies to attach to the group. We recommend using groups to manage user permissions by job function, AWS service access, or custom permissions. 
Learn more L 
Set permissions boundary - optional 
Cancel 
Previous 
Next
</pre>
</details>

User is created

<details><summary>Screenshot 4 — transcribed text</summary>

<pre>
IAM &gt; IAM users &gt; Shrayansh 
Shrayansh Info 
Delete 
V 
Identity and Access 
Management (IAM) 
Q Search IAM 
Summary 
ARN 
Console access 
Access key 1 
Dashboard 
arn:aws:iam:643537035131:user/Shrayansh 
Disabled 
Create access key 
Access Management 
Created 
Last console sign-in 
May 27, 2026, 14:41 (UTC+05:30) 
Roles 
Policies 
IAM users 
Permissions 
Groups 
Tags 
Security credentials 
Last Accessed 
IAM user groups 
Identity providers 
Remove 
Add permissions 
Account settings 
Permissions policies (0) 
Root access management 
Permissions are defined by policies attached to the user directly or through groups. 
Temporary delegation requests 
Filter by Type 
Q Search 
All types 
&lt; 1 &gt; { 
Access reports 
- 
Access Analyzer 
Policy name [ 
Type 
Attached via 
1 
Resource analysis 
Unused access 
No resources to display 
Analyzer settings 
Credential report 
Organization activity 
Permissions boundary (not set) 
Service control policies 
Resource control policies 
Generate policy based on CloudTrail events 
You can generate a new policy based on the access activity for this user, then customize, create, and attach it to this role. AWS uses your CloudTrail events to identify the services and actions used and generate a policy. Learn more LA 
Related consoles 
Generate policy 
IAM Identity Center [ 
No requests to generate a policy in the past 7 days. 
AWS Organizations LA
</pre>
</details>

Unique identifier for this IAM User. Almost every AWS resource - Users, S3 buckets, Lambda functions etc. has its own ARN.

How this newly created User will login?

Console Username/Password

API Key + Secret Key

Click on Security Credentials

<details><summary>Screenshot 5 — transcribed text</summary>

<pre>
IAM &gt; IAM users &gt; Shrayansh 
Shrayansh Info 
Delete 
V 
Identity and Access 
Management (IAM) 
Q Search IAM 
Summary 
ARN 
Console access 
Access key 1 
Dashboard 
arn:aws:iam:643537035131:user/Shrayansh 
Disabled 
Create access key 
Access Management 
Created 
Last console sign-in 
May 27, 2026, 14:41 (UTC+05:30) 
Roles 
Policies 
IAM users 
Permissions 
Groups 
Tags 
Security credentials 
Last Accessed 
IAM user groups 
Identity providers 
Remove 
Add permissions 
Account settings 
Permissions policies (0) 
Root access management 
Permissions are defined by policies attached to the user directly or through groups. 
Temporary delegation requests 
Filter by Type 
Q Search 
All types 
&lt; 1 &gt; { 
Access reports 
- 
Access Analyzer 
Policy name [ 
Type 
Attached via 
1 
Resource analysis 
Unused access 
No resources to display 
Analyzer settings 
Credential report 
Organization activity 
Permissions boundary (not set) 
Service control policies 
Resource control policies 
Generate policy based on CloudTrail events 
You can generate a new policy based on the access activity for this user, then customize, create, and attach it to this role. AWS uses your CloudTrail events to identify the services and actions used and generate a policy. Learn more LA 
Related consoles 
Generate policy 
IAM Identity Center [ 
No requests to generate a policy in the past 7 days. 
AWS Organizations LA
</pre>
</details>

Click on Create Access Key

Click on Enable Console access

<details><summary>Screenshot 6 — transcribed text</summary>

<pre>
Shrayansh Info 
Delete 
Summary 
ARN 
Console access 
Access key 1 
arn:aws:iam:643537035131:user/Shrayansh 
Disabled 
Create access key 
Created 
Last console sign-in 
May 27, 2026, 14:41 (UTC+05:30) 
Permissions 
Groups 
Tags 
Security credentials 
Last Accessed 
Console sign-in 
Enable console access 
Console sign-in link 
Console password 
[ https://643537035131.signin.aws.amazon.com/console 
Not enabled 
Multi-factor authentication (MFA) (0) 
Remove 
Resync 
Assign MFA device 
Use MFA to increase the security of your AWS environment. Signing in with MFA requires an authentication code from an MFA device. Each user can have a maximum of 8 MFA devices assigned. Learn more L 
Type 
Identifier 
Certifications 
Created on 
No MFA devices. Assign an MFA device to improve the security of your AWS environment 
Assign MFA device 
Access keys (0) 
Create access key 
Use access keys to send programmatic calls to AWS from the AWS CLI, AWS Tools for PowerShell, AWS SDKs, or direct AWS API calls. You can have a maximum of two access keys (active or inactive) at a time. Learn more 
No access keys. As a best practice, avoid using long-term credentials like access keys. Instead, use tools which provide short term credentials. Learn more La 
Create access key
</pre>
</details>

<details><summary>Screenshot 7 — transcribed text</summary>

<pre>
Shrayansh Info 
Delete 
Summary 
ARN 
Console access 
Access key 1 
arn:aws:iam:643537035131:user/Shrayansh 
Disabled 
Create access key 
Created 
Last console sign-in 
May 27, 2026, 14:41 (UTC+05:30) 
Permissions 
Groups 
Tags 
Security credentials 
Last Accessed 
Console sign-in 
Enable console access 
Console sign-in link 
Console password 
[ https://643537035131.signin.aws.amazon.com/console 
Not enabled 
Multi-factor authentication (MFA) (0) 
Remove 
Resync 
Assign MFA device 
Use MFA to increase the security of your AWS environment. Signing in with MFA requires an authentication code from an MFA device. Each user can have a maximum of 8 MFA devices assigned. Learn more L 
Type 
Identifier 
Certifications 
Created on 
No MFA devices. Assign an MFA device to improve the security of your AWS environment 
Assign MFA device 
Access keys (0) 
Create access key 
Use access keys to send programmatic calls to AWS from the AWS CLI, AWS Tools for PowerShell, AWS SDKs, or direct AWS API calls. You can have a maximum of two access keys (active or inactive) at a time. Learn more 
No access keys. As a best practice, avoid using long-term credentials like access keys. Instead, use tools which provide short term credentials. Learn more La 
Create access key
</pre>
</details>

Set your Password

Select your reason for creating Access Key

<details><summary>Screenshot 8 — transcribed text</summary>

<pre>
Shrayansh Info 
Delete 
Summary 
ARN 
Console access 
Access key 1 
arn:aws:iam:643537035131:user/Shrayansh 
Disabled 
Create access key 
Created 
Enable console access 
X 
May 27, 2026, 14:41 (UTC+05:30) 
Enable console access for Shrayansh. 
Permissions 
Groups 
Tags 
Console password 
Secu 
Autogenerated password 
O Custom password 
Console sign-in 
.......... 
Enable console access 
Console sign-in link 
· Must be at least 8 characters long 
https://643537035131.signin.aws.amazon.com/ 
. Must include at least three of the following mix of character types: uppercase letters 
(A-Z), lowercase letters (a-z), numbers (0-9), and symbols ! @ # $ % ^ &amp; * ( ) _ + - 
(hyphen) = [] {} |&#x27; 
Show password 
Multi-factor authentication (MFA) (0) 
Remove 
Resync 
Assign MFA device 
Use MFA to increase the security of your AWS enviror 
User must create new password at next sign-in 
Users automatically get the IAMUserChangePassword LA policy to allow them to change their 
have a maximum of 8 MFA devices assigned. Learn more 
Type 
own password. 
Created on 
Cancel 
Enable console access 
AWS environment 
Assign MFA device 
Access keys (0) 
Create access key 
Use access keys to send programmatic calls to AWS from the AWS CLI, AWS Tools for PowerShell, AWS SDKs, or direct AWS API calls. You can have a maximum of two access keys (active or inactive) at a time. Learn more L 
No access keys. As a best practice, avoid using long-term credentials like access keys. Instead, use tools which provide short term credentials. Learn more La 
Create access key
</pre>
</details>

<details><summary>Screenshot 9 — transcribed text</summary>

<pre>
IAM &gt; IAM users 
&gt; Shrayansh &gt; Create access key 
Step 1 
Access key best practices &amp; 
Access key best practices &amp; alternatives Info 
alternatives 
Avoid using long-term credentials like access keys to improve your security. Consider the following use cases and alternatives. 
Step 2 - optional 
Set description tag 
Use case 
Step : 
O 
Command Line Interface (CLI) 
Retrieve access keys 
You plan to use this access key to enable the AWS CLI to access your AWS account. 
Local code 
You plan to use this access key to enable application code in a local development environment to access your 
AWS account. 
O Application running on an AWS compute service 
You plan to use this access key to enable application code running on an AWS compute service like Amazon EC2, 
Amazon ECS, or AWS Lambda to access your AWS account. 
Third-party service 
You plan to use this access key to enable access for a third-party application or service that monitors or manages 
your AWS resources. 
Application running outside AWS 
You plan to use this access key to authenticate workloads running in your data center or other infrastructure 
outside of AWS that needs to access your AWS resources. 
Other 
Your use case is not listed here. 
A Alternative recommended 
Assign an IAM role to compute resources like EC2 instances or Lambda functions to automatically supply temporary credentials to enable access. 
Learn more La 
Confirmation 
I understand the above recommendation and want to proceed to create an access key. 
Cancel 
Next
</pre>
</details>

Based on your reason, it shows better alternative. 
 
like instead of creating long lived access id + secret key, it is suggesting us to use ROLE.

Save the URL, Username and Password for future.

Save the Access Key and Secret Access key

<details><summary>Screenshot 10 — transcribed text</summary>

<pre>
Console access enabled. 
X 
Shrayansh Info 
Delete 
Summary 
ARN 
Console access 
Access key 1 
arn:aws:iam:643537035131:user/Shrayansh 
Console password 
X 
Create access key 
Created 
May 27, 2026, 14:41 (UTC+05:30) 
You have successfully enabled the user&#x27;s new password. 
This is the only time you can view this password. After you close this 
window, if the password is lost, you must create a new one. 
Permissions 
Groups 
Tag: 
Secu 
Console sign-in URL 
Console sign-in 
[ https://643537035131.signin.aws.amazon.com/console 
Manage console access 
Console sign-in link 
User name 
https://643537035131.signin.aws.amazon.com/ 
Shrayansh 
-27 14:57 GMT+5:30) 
Console password 
*************** Show 
Download .csv file 
Close 
Multi-factor authentication (MFA) (0) 
Remove 
Resync 
Assign MFA device 
Use MFA to increase the security of your AWS environment. Signing in with MFA requires an authentication code from an MFA device. Each user can have a maximum of 8 MFA devices assigned. Learn more L 
Type 
Identifier 
Certifications 
Created on 
No MFA devices. Assign an MFA device to improve the security of your AWS environment 
Assign MFA device 
Acesse have (0) 
Cranto access Low
</pre>
</details>

<details><summary>Screenshot 11 — transcribed text</summary>

<pre>
Step 1 
Access key best practices &amp; 
Retrieve access keys Info 
alternatives 
Step 2 - optional 
Access key 
Set description tag 
If you lose or forget your secret access key, you cannot retrieve it. Instead, create a new access key and make the old key inactive. 
Step 3 
Access key 
Secret access key 
Retrieve access keys 
AKIA**************** 
*************** 
Show 
Access key best practices 
. Never store your access key in plain text, in a code repository, or in code. 
. Disable or delete access key when no longer needed. 
. Enable least-privilege permissions. 
. Rotate access keys regularly. 
For more details about managing access keys, see the best practices for managing AWS access keys. 
Download .csv file 
Done
</pre>
</details>

Lets use API key and Secret key from code to access AWS resources.

Lets Login with the newly created User credentials:

Config class

```java
package com.concepts;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import software.amazon.awssdk.auth.credentials.AwsBasicCredentials;
import software.amazon.awssdk.auth.credentials.StaticCredentialsProvider;
import software.amazon.awssdk.regions.Region;
import software.amazon.awssdk.services.s3.S3Client;
@Configuration
public class AwsConfig {
@Value("${aws.access-key}")
private String accessKey;
@Value("${aws.secret-key}")
private String secretKey;
@Value("${aws.region}")
private String region;
@Bean
public S3Client s3Client() {
AwsBasicCredentials credentials =
AwsBasicCredentials.create(accessKey, secretKey);
return S3Client.builder()
.region(Region.of(region))
.credentialsProvider(
StaticCredentialsProvider.create(credentials)
)
.build();
}
}
```

Use the Console sign-in URL, which was returned during User creation

<details><summary>Screenshot 12 — transcribed text</summary>

<pre>
IAM user sign in ® 
Account ID or alias (Don&#x27;t have?) 
643537035131 
Remember this account 
IAM username 
Shrayansh 
Password 
......... 
Show Password 
Having trouble? 
Sign in 
Sign in using root user email 
Create a new AWS account
</pre>
</details>

Reading access key id and secret id from properties, generally it should be stored in vault, this is just for testing purpose.

Controller class

Provide the credentials, and login successfully

```java
package com.concepts;
Import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import software.amazon.awssdk.services.s3.S3Client;
@RestController
@RequestMapping("/s3")
public class S3Controller {
@Autowired
private S3Client s3Client;
@GetMapping("/buckets")
public String listBuckets() {
try {
s3Client.listBuckets();
return "SUCCESS";
} catch (Exception e) {
return e.getMessage(); }
}
}
```

<details><summary>Screenshot 13 — transcribed text</summary>

<pre>
... 
ap-south-1.console.aws.amazon.com/console/home?region=ap-south-1# 
Incognito (2) 
V 
aws 
Account ID: 6435-3703-5131 
Search 
[Option+S] @ 
? 
Asia Pacific (Mumbai) v 
Shrayansh 
Console home Info 
Reset to default layout 
+ Add widgets 
... 
... 
: Recently visited Info 
:: Applications (0) Info 
Create application 
Region: Asia Pacific (Mumbai) 
IAM 
Billing and Cost Management 
Select Region 
ap-south-1 (Current Region)\ 
Q Find applications 
S3 
&lt; 1 &gt; 
Name 
Description 
Region 
Originati. 
D 
&gt; &gt; Access denied to servicecatalog:ListApplications 
Diagnose with Amazon Q 
View all services 
Go to myApplications 
: Welcome to AWS 
₿ Cost and usage Info 
: 
... 
:: AWS Health Info 
Getting started with 
Current month 
Cost breakdown 
AWS LA 
Access denied 
Access denied 
------- 
Find out the fundamentals 
and valuable information to 
Forecast month end 
get the most out of AWS. 
Access denied 
--------- 
Training and 
Savings opportunities
</pre>
</details>

Trying to access list of S3 buckets

If, we try to access S3 buckets, we get ACCESS DENIED

<details><summary>Screenshot 14 — transcribed text</summary>

<pre>
Amazon S3 
&gt; 
Buckets 
Amazon S3 
&lt; 
Buckets 
Buckets 
General purpose buckets 
All AWS Regions 
Directory buckets 
General purpose buckets 
Directory buckets 
0 
Table buckets 
General purpose buckets (0) Info 
Copy ARN 
Empty 
Delete 
Create bucket 
Vector buckets 
Buckets are containers for data stored in S3. 
1 
&gt; 
V 
Files 
Q Find buckets by name 
D 
File systems New 
Name 
AWS Region 
Creation date 
Access management and 
security 
You don&#x27;t have permissions to list buckets 
Diagnose with Amazon Q 
After you or your AWS administrator has updated your permissions to allow the 
Access Points 
s3:ListAllMyBuckets action, refresh this page. Learn more about Identity and access 
Access Points for FSX 
management in Amazon S3 La 
Access Grants 
IAM Access Analyzer 
Storage management and
</pre>
</details>

application.properties

```properties
aws.access-key=ABCD…
aws.secret-key=MCtOz…
aws.region=ap-south-1
```

Postman call:

Here you will notice that, everywhere we see "Access Denied".

As I mentioned earlier, AWS API call is DENIED by-default.

So till now we have not granted any permission to this newly created user, that’s why we see Access denied everywhere.

<details><summary>Screenshot 15 — transcribed text</summary>

<pre>
GET 
V 
http://localhost:8080/s3/buckets 
Send 
V 
Params 
Authorization 
Headers (8) 
Body . 
Pre-request Script 
Tests 
Settings 
Cookies 
Query Params 
Key 
Value 
Bulk Edit 
Key 
Value 
Body 
Cookies 
Headers (5) 
Test Results 
Status: 200 OK Time: 327 ms Size: 536 B 
Save Response v 
Pretty 
Raw 
Preview 
Visualize 
Text 
V 
1 
User: arn: aws: iam: : 643537035131: user/Shrayansh is not authorized to perform: s3: ListAllMyBuckets because no 
identity-based policy allows the s3: ListAllMyBuckets action (Service: S3, Status Code: 403, Request ID: 
JCXV4MVPGV30FKBV, Extended Request ID: /h3pF4Js63NI3Rdwtb07AGF6v6qBq+nSnOVkDxsckch8yHnIINTOJd 
+mkOgrz2ViWVEKX2zLDsGr741gLnPRIv9/WLDji1KX) (SDK Attempt Count: 1)
</pre>
</details>

## 2. IAM Policy

Once AWS knows, WHO you are (Authentication via Username/Password or Access Key + Secret key)

Next it ask is "Are you allowed to do, what you are trying to do"?

Policy helps to answer this:

Its of 2 types:

Resource Based Policies

Identity Based Policies

Both is same, but perceptive is different

S3  
bucket-prod

USER-1

Identity Based Policies : Policy is attached with IAM entity(User/Group/Role)

S3  
bucket-prod

USER-1

attached

Policy

```json
{
"Version": "2012-10-17",
"Statement": [
{
"Effect": "Allow",
"Action": "s3:GetObject",
"Resource": "arn:aws:s3:::bucket-prod/*"
}
]
}
```

Its policy language version, keep it same

Array of rules

Action: service : API operation 
the WHAT part, what identity want to do? 
 
we can also have list too:

"Action":[

"s3:GetObject",

"s3:ListBucket"

]

Resource: ARN 
"WHICH RESOURCE" part, on which resource identity want to perform the above action.

we can also have list too:

"Resource":[

"arn:aws:s3:::bucket-a/*",

"arn:aws:s3:::bucket-b/*"

]

Effect : Allow / Deny 
Explicit Allow or Deny

Resource Based Policies : Policy is attached with Resource

S3  
bucket-prod

USER-1

Since policy is now attached to Resource, resource must specify all 3:

- Who is allowed to access me

- What operation they can perform

- On Which Resource

Policy

```json
{
"Version": "2012-10-17",
"Statement": [
{
"Effect": "Allow",
"Principal": { "AWS": "arn:aws:iam::1234567:user/Shrayansh" },
"Action": "s3:GetObject",
"Resource": "arn:aws:s3:::bucket-prod/*"
}
]
}
```

Principal answers, WHO part:

- User

- Role

- An account

- AWS service etc.

Try to access

S3  
bucket-prod

USER-1

Check with IAM engine,

User-1 is trying to perform X action

on this Y resource, should I allow it or not?

IAM

(Check all policies and respond)

Lets Create Identity Based Policy:

Go to IAM -> Policies -> Create Policy

<details><summary>Screenshot 16 — transcribed text</summary>

<pre>
IAM &gt; Policies 
Identity and Access 
Policies (1490) Info 
Actions V 
Delete 
Create policy 
V 
Management (IAM) 
A policy is an object in AWS that defines permissions. 
Filter by Type 
Q Search IAM 
Q Search 
All types 
&lt; 1 2 3 4 5 6 7 ... 75 &gt; 
Dashboard 
- 
- 
Policy name 
| Type 
Used as 
Description 
Access Management 
AccessAnalyzerServiceRolePolicy 
AWS managed 
None 
Allow Access Analyzer to analyze resou ... 
Roles 
AWS managed 
None 
For use with accounts created through ... 
C 
Policies 
AccountManagementFromVercel 
IAM users 
None 
Provides full access to AWS services an ... 
C 
AdministratorAccess 
AWS managed - job function 
IAM user groups 
AdministratorAccess-Amplify 
AWS managed 
None 
Grants account administrative permissi ... 
Identity providers 
Account settings 
AdministratorAccess-AWSElasticBeanstalk 
AWS managed 
None 
Grants account administrative permissi ... 
Root access management 
AIDevOpsAgentAccessPolicy 
AWS managed 
None 
Provides permissions required by the A.
</pre>
</details>

Click on JSON

<details><summary>Screenshot 17 — transcribed text</summary>

<pre>
IAM &gt; Policies &gt; Create policy 
III 
Step 1 
Specify permissions 
Specify permissions Info 
Add permissions by selecting services, actions, resources, and conditions. Build permission statements using the JSON editor. 
Step 2 
Review and create 
Policy editor 
Visual 
JSON 
Actions 
Select a service 
Specify what actions can be performed on specific resources in a service. 
Service 
Choose a service 
+ Add more permissions 
Cancel 
Next
</pre>
</details>

Add Policy document and Click Next

<details><summary>Screenshot 18 — transcribed text</summary>

<pre>
IAM &gt; Policies &gt; Create policy 
III 
Step 1 
O 
Specify permissions 
Specify permissions Info 
Add permissions by selecting services, actions, resources, and conditions. Build permission statements using the JSON editor. 
Step 2 
Review and create 
Policy editor 
Visual 
JSON 
Actions 
1 &#x27; 
2 
&quot;Version&quot;: &quot;2012-10-17&quot;, 
Edit statement 
3 &quot; &quot;Statement&quot;: [ 
4 &quot; 
5 
&quot;Effect&quot;: &quot;Allow&quot;, 
2 
&quot;Action&quot;: &quot;s3 :* &quot;, 
7 
&quot;Resource&quot;: &quot;*&quot; 
B 
Select a statement 
10 
Select an existing statement in the policy or 
add a new statement. 
+ Add new statement 
10:2 JSON 
+ Add new statement 
6056 of 6144 characters remaining 
Security: 0 
Errors: 0 A Warnings: 0 
Q Suggestions: 0 
Cance 
Next
</pre>
</details>

Action ->  S3:*  
means, S3 service and all its API (* wild card used) 
 
Resource -> *  
means, all S3 resources( all buckets, all objects, all regions )

Give proper policy Name -> Click Next

<details><summary>Screenshot 19 — transcribed text</summary>

<pre>
IAM &gt; Policies &gt; Create policy 
Step1 
Specify permissions 
Review and create Info 
Review the permissions, specify details, and tags. 
Step 2 
Review and create 
Policy details 
Policy name 
Enter a meaningful name to identify this policy. 
AllowAllS3Actions 
1 
Maximum 128 characters. Use alphanumeric and &#x27;+=,.@ -_ &#x27; characters. 
Description - optional 
Add a short explanation for this policy. 
Maximum 1,000 characters. Use alphanumeric and &#x27;+=,.@ -_ &#x27; characters. 
Permissions defined in this policy Info 
Edit 
Permissions defined in this policy document specify which actions are allowed or denied. To define permissions for an IAM identity (user, user group, or role), attach a policy to it 
[Q Search 
Allow (1 of 469 services) 
Show remaining 468 services 
- 
D 
Service 
Access level 
Resource 
Request condition 
S3 
Full access 
All resources 
None 
Add tags - optional Info 
Tags are key-value pairs that you can add to AWS resources to help identify, organize, or search for resources. 
No tags associated with the resource. 
Add new tag 
You can add up to 50 more tags. 
Cancel 
Previous 
Create policy
</pre>
</details>

Now our policy is created.

<details><summary>Screenshot 20 — transcribed text</summary>

<pre>
Policy AllowAllS3Actions created. 
View policy 
× 
Policies (1491) Info 
Actions V 
Delete 
Create policy 
A policy is an object in AWS that defines permissions. 
Filter by Type 
Q ALLOWALL 
X 
All types 
1 match 
&lt; 1 &gt; 
- 
D 
Policy name 
Type 
Used as 
AllowAllS3Actions 
Customer managed 
None
</pre>
</details>

Now lets attach these Policy to User

Go to IAM User -> Permissions tab

<details><summary>Screenshot 21 — transcribed text</summary>

<pre>
IAM &gt; IAM users &gt; Shrayansh 
Identity and Access 
&lt; 
Shrayansh Info 
Delete 
Management (IAM) 
Q Search IAM 
Summary 
ARN 
Console access 
Access key 1 
Dashboard 
arn:aws:iam:643537035131:user/Shrayansh 
A Enabled without MFA 
AKIA**************** - Active 
Used today. Created today. 
Access Management 
Roles 
Created 
Last console sign-in 
Access key 2 
May 27, 2026, 14:41 (UTC+05:30) 
Today 
Create access key 
Policies 
IAM users 
IAM user groups 
Permissions 
Groups 
Tags 
Security credentials 
Last Accessed 
Identity providers 
Account settings 
Permissions policies (0) 
Remove 
Add permissions 
Root access management 
Temporary delegation requests 
Permissions are defined by policies attached to the user directly or through groups. 
Filter by Type 
Access reports 
Q Search 
All types 
&lt; 1 &gt; 
Access Analyzer 
Resource analysis 
Policy name 
Type 
Attached via [ 
Unused access 
No resources to display 
Analyzer settings 
Credential report 
Organization activity 
Permissions boundary (not set) 
Service control policies 
Resource control policies 
Generate policy based on CloudTrail events 
Related consoles 
You can generate a new policy based on the access activity for this user, then customize, create, and attach it to this role. AWS uses your CloudTrail events to identify the services and actions used and generate a policy. Learn more La 
IAM Identity Center 
Generate policy 
AWS Organizations [ 
No requests to generate a policy in the past 7 days.
</pre>
</details>

Click Add permissions -> select Add Permissions

<details><summary>Screenshot 22 — transcribed text</summary>

<pre>
IAM &gt; IAM users &gt; Shrayansh 
V 
Identity and Access 
Shrayansh Info 
Delete 
Management (IAM) 
Q Search IAM 
Summary 
ARN 
Console access 
Access key 1 
Dashboard 
arn:aws:iam:643537035131:user/Shrayansh 
A Enabled without MFA 
AKIA**************** - Active 
Used today. Created today. 
Access Management 
Last console sign-in 
Access key 2 
Roles 
Created 
May 27, 2026, 14:41 (UTC+05:30) 
Today 
Create access key 
Policies 
IAM users 
IAM user groups 
Permissions 
Groups 
Tags 
Security credentials 
Last Accessed 
Identity providers 
Account settings 
Permissions policies (0) 
Remove 
Add permissions 
Root access management 
Permissions are defined by policies attached to the user directly or through groups. 
Add permissions 
Temporary delegation requests 
Filter by Type 
Create inline policy 
Access reports 
Q Search 
All types 
1 &gt; 
Access Analyzer 
Policy name [7 
Type 
Attached via L 
1 
Resource analysis 
Unused access 
No resources to display 
Analyzer settings 
Credential report 
Organization activity 
Permissions boundary (not set) 
Service control policies 
Resource control policies 
Generate policy based on CloudTrail events 
Related consoles 
You can generate a new policy based on the access activity for this user, then customize, create, and attach it to this role. AWS uses your CloudTrail events to identify the services and actions used and generate a policy. Learn more La 
IAM Identity Center La 
Generate policy 
AWS Organizations [ 
No requests to generate a policy in the past 7 days.
</pre>
</details>

Choose Attach policies directly -> search for our Custom policy -> select it

<details><summary>Screenshot 23 — transcribed text</summary>

<pre>
= IAM &gt; IAM users &gt; Shrayansh &gt; Add permissions 
Step 
Add permissions 
Add permissions 
Add user to an existing group or create a new one. Using groups is a best-practice way to manage user&#x27;s permissions by job functions. Learn more La 
Step 2 
Review 
Permissions options 
Add user to group 
Copy permissions 
Attach policies directly 
Add user to an existing group, or create a new group. We recommend using groups to manage 
Copy all group memberships, attached managed policies, inline policies, and any existing 
Attach a managed policy directly to a user. As a best practice, we recommend attaching policies to 
user permissions by job function. 
permissions boundaries from an existing user. 
a group instead. Then, add the user to the appropriate group. 
Permissions policies (1/1491) 
Filter by Type 
Q AllowAll 
X 
All types 
match 
&lt; 1 &gt; 
Policy name [^ 
Type 
Attached entities 
D 
AllowAllS3Actions 
Customer managed 
0 
Cancel 
Next
</pre>
</details>

Click Add the Permissions

<details><summary>Screenshot 24 — transcribed text</summary>

<pre>
IAM &gt; IAM users &gt; Shrayansh &gt; Add permissions 
Step 1 
Add permissions 
Review 
The following policies will be attached to this user. Learn more 
Step 2 
Review 
User details 
User name 
Shrayansh 
Permissions summary (1) 
&lt; 1 &gt; 
Used as 
D 
Name [ 
Type 
AllowAllS3Actions 
Customer managed 
Permissions policy 
ancel 
Previous 
Add permissions
</pre>
</details>

Now, Our IAM User "Shrayansh" earlier getting Access Denied

<details><summary>Screenshot 25 — transcribed text</summary>

<pre>
Account ID: 6435-3703-5131 &quot; 
aws 
Q Search 
&quot;Option+S] 
Asia Pacific (Mumbai) v 
Shrayansh 
Amazon S3 &gt; Buckets 
Amazon S3 
Buckets 
· Buckets 
General purpose buckets 
All AWS Regions 
Directory buckets 
General purpose buckets 
Directory buckets 
Table buckets 
General purpose buckets (0) Info 
Copy ARN 
Empty 
Delete 
Create bucket 
Account snapshot Info 
View dashboard 
Vector buckets 
Buckets are containers for data stored in S3. 
Updated daily 
V Files 
Q Find buckets by name 
&lt; 1 
&gt; 
Storage Lens provides visibility into storage usage and 
activity trends. 
D 
File systems New 
Name 
AWS Region 
Creation date 
D 
Access management and 
security 
You don&#x27;t have permissions to list buckets 
Diagnose with Amazon C 
External access summary Info 
After you or your AWS administrator has updated your permissions to allow the 
Access Points 
s3:ListAllMyBuckets action, refresh this page. Learn more about Identity and access 
Updated daily 
External access findings help you identify bucket 
Access Points for FSX 
management in Amazon S3 L 
permissions that allow public access or access from other 
Access Grants 
AWS accounts. 
IAM Access Analyzer 
Storage management and 
insights
</pre>
</details>

After policy attached to User "Shrayansh" and Refresh of the page, access granted

<details><summary>Screenshot 26 — transcribed text</summary>

<pre>
... 
ap-south-1.console.aws.amazon.com/s3/buckets?region=ap-south-1 
&#x27;2 Incognito (2) 
1 
Account ID: 6435-3703-5131 &quot; 
aws 
Search 
[Option+S] @ 
? 
Asia Pacific (Mumbai) \ 
Shrayansh 
Amazon S3 &gt; Buckets 
V 
Amazon S3 
Buckets 
Buckets 
General purpose buckets 
All AWS Regions 
Directory buckets 
General purpose buckets 
Directory buckets 
General purpose buckets (0) Info 
Copy ARN 
Empty 
Delete 
Create bucket 
Account snapshot Info 
Table buckets 
View dashboard 
Vector buckets 
Buckets are containers for data stored in S3. 
Updated daily 
Storage Lens provides visibility into storage usage and 
Files 
Q Find buckets by name 
&lt; 1 
&gt; 
activity trends. 
File systems New 
D 
Name 
AWS Region 
Creation date 
Access management and 
No buckets 
External access summary Info 
security 
You don&#x27;t have any buckets. 
Access Points 
Updated daily 
Create bucket 
External access findings help you identify bucket 
Access Points for FSX 
permissions that allow public access or access from other 
Access Grants 
AWS accounts. 
IAM Access Analyzer
</pre>
</details>

Similarly from Postman:

After policy attached:

Previously:

<details><summary>Screenshot 27 — transcribed text</summary>

<pre>
HTTP 
http://localhost:8080/s3/buckets 
GET 
V 
http://localhost:8080/s3/buckets 
Params 
Authorization 
Headers (8) 
Body . 
Pre-request Script 
Tests 
Settings 
Query Params 
Key 
Value 
Key 
Value 
Body 
Cookies Headers (5) 
Test Results 
Status: 200 OK T 
Pretty 
Raw 
Preview 
Visualize 
Text 
V 
1 
SUCCESS
</pre>
</details>

<details><summary>Screenshot 28 — transcribed text</summary>

<pre>
GET 
V 
http://localhost:8080/s3/buckets 
Send 
V 
Params 
Authorization 
Headers (8) 
Body . 
Pre-request Script 
Tests 
Settings 
Cookies 
Query Params 
Key 
Value 
Bulk Edit 
Key 
Value 
Body 
Cookies 
Headers (5) 
Test Results 
Status: 200 OK Time: 327 ms Size: 536 B 
Save Response v 
Pretty 
Raw 
Preview 
Visualize 
Text 
V 
1 
User: arn: aws: iam: : 643537035131: user/Shrayansh is not authorized to perform: s3: ListAllMyBuckets because no 
identity-based policy allows the s3: ListAllMyBuckets action (Service: S3, Status Code: 403, Request ID: 
JCXV4MVPGV30FKBV, Extended Request ID: /h3pF4Js63NI3Rdwtb07AGF6v6qBq+nSnOVkDxsckch8yHnIINTOJd 
+mkOgrz2ViWVEKX2zLDsGr741gLnPRIv9/WLDji1KX) (SDK Attempt Count: 1)
</pre>
</details>

Lets Create Resource Based Policy:

First lets delete the Policy which we attached to User .

<details><summary>Screenshot 29 — transcribed text</summary>

<pre>
Shrayansh Info 
Delete 
Summary 
ARN 
Console access 
Access key 1 
arn:aws:iam:643537035131:user/Shrayansh 
A Enabled without MFA 
AKIA**************** - Active 
Used today. 9 hours old. 
Created 
Last console sign-in 
Access key 2 
May 27, 2026, 14:41 (UTC+05:30) 
7 hours ago 
Create access key 
Permissions 
Groups 
Tags 
Security credentials 
Last Accessed 
Permissions policies (1/1) 
Remove 
Add permissions 
Permissions are defined by policies attached to the use 
Remove policy for user? 
X 
Q Search 
Remove policy AllowAllS3Actions? 
&lt; 1 &gt; 
- 
Policy name [ 
Cancel 
Remove policy 
Attached via LZ 
AllowAllSƷActions 
Customer managed 
Directly 
Permissions boundary (not set) 
Generate policy based on CloudTrail events 
You can generate a new policy based on the access activity for this user, then customize, create, and attach it to this role. AWS uses your CloudTrail events to identify the services and actions used and generate a policy. Learn more 
Generate policy 
No requests to generate a policy in the past 7 days.
</pre>
</details>

Now in Resource based policy -> we have to first create Resource -> Go to S3 ->  Click Create bucket

<details><summary>Screenshot 30 — transcribed text</summary>

<pre>
aws 
[Option+S] @ 
aws-learning (643537035131) 
Q Search 
Asia Pacific (Mumbai) 
aws-learning 
Amazon S3 
Storage 
Amazon S3 
Create a bucket 
Store and retrieve any amount of data 
Every object in S3 is stored in a bucket. To upload 
files and folders to S3, you&#x27;ll need to create a bucket 
from anywhere 
where the objects will be stored. 
Create bucket 
Amazon S3 is an object storage service that offers industry-leading scalability, data availability, security, and 
performance.
</pre>
</details>

Create Bucket -> Provide Bucket Name -> Rest all keep the Same

<details><summary>Screenshot 31 — transcribed text</summary>

<pre>
General configuration 
AWS Region 
Asia Pacific (Mumbai) ap-south-1 
Bucket type |Info 
O 
General purpose 
Recommended for most use cases and access patterns. General purpose buckets are the original S3 bucket type. They allow a mix of 
storage classes that redundantly store objects across multiple Availability Zones. 
Bucket namespace 
Choose the namespace where you want to create your bucket. Learn more IZ 
Global namespace 
By default, S3 creates general purpose buckets in the global namespace. 
Account Regional namespace (recommended) 
General purpose buckets created in your account Regional namespace are unique to your account. These buckets can never be created by an 
Bucket name 
Info 
sj-payment-prod 
Bucket names must be 3 to 63 characters and unique within the global namespace. Bucket names must also begin and end with a letter or numb 
Copy settings from existing bucket - optional 
Only the bucket settings in the following configuration are copied. 
Choose bucket 
Format: s3://bucket/prefix
</pre>
</details>

Open the newly created Bucket (the resource)

<details><summary>Screenshot 32 — transcribed text</summary>

<pre>
Buckets 
General purpose buckets 
All AWS Regions 
Directory buckets 
General purpose buckets (1) Info 
Copy ARN 
Empty 
Delete 
Create bucket 
Buckets are containers for data stored in S3. 
Q Find buckets by name 
1 
Name 
AWS Region 
Creation date 
D 
sj-payment-prod 
Asia Pacific (Mumbai) ap-south-1 
May 28, 2026, 00:11:29 (UTC+05:30)
</pre>
</details>

Click Permissions -> then Click Bucket Policy Edit

<details><summary>Screenshot 33 — transcribed text</summary>

<pre>
Amazon S3 &gt; Buckets &gt; sj-payment-prod 
sj-payment-prod Info 
Objects 
Metadata 
Properties 
Permissions 
Metrics 
Management 
File systems - new 
Access Points 
Permissions overview 
Access finding 
Access findings are provided by IAM external access analyzers. Learn more about How IAM analyzer findings work La 
View analyzer for ap-south-1 
Block public access (bucket settings) 
Edit 
Public access is granted to buckets and objects through access control lists (ACLs), bucket policies, access point policies, or all. In order to ensure that public access to all your S3 buckets and objects is blocked, turn on Block all public access. These settings apply only to this bucket and its 
access points. AWS recommends that you turn on Block all public access, but before applying any of these settings, ensure that your applications will work correctly without public access. If you require some level of public access to your buckets or objects within, you can customize the 
individual settings below to suit your specific storage use cases. Learn more L 
Block all public access 
Or 
&gt;Individual Block Public Access settings for this bucket 
Bucket policy 
Edit 
Delete 
The bucket policy, written in JSON, provides access to the objects stored in the bucket. Bucket policies don&#x27;t apply to objects owned by other accounts. Learn more La 
Public access is blocked because Block Public Access settings are turned on for this bucket 
To determine which settings are turned on, check your Block Public Access settings for this bucket. Learn more about using Amazon S3 Block Public Access La
</pre>
</details>

Add the policy json document and Save

<details><summary>Screenshot 34 — transcribed text</summary>

<pre>
Bucket policy 
The bucket policy, written in JSON, provides access to the objects stored in the bucket. Bucket policies 
Bucket ARN 
arn:aws:s3 ::: sj-payment-prod 
Policy 
1 
{ 
2 
&quot;Version&quot;: &quot;2012-10-17&quot;, 
3 
&quot;Statement&quot;: 
4 
5 
3 
&quot;Effect&quot;: &quot;Allow&quot;, 
6 
&quot;Principal&quot;: { 
7 
&quot;AWS&quot;: &quot;arn:aws: iam: : 643537035131:user/Shrayansh&quot; 
8 
9 
&quot;Action&quot;: &quot;s3 :* &quot;, 
10 
&quot;Resource&quot;: [ 
11 
&quot;arn: aws: s3: : : sj-payment-prod&quot;, 
12 
&quot;arn: aws : s3: : : sj-payment-prod/*&quot; 
13 
14 
} 
15 
16
</pre>
</details>

Permission for All S3 API operation

On bucket "sj-payment-prod" and  
On all objects within "sj-payment-prod"

Now lets test:

Controller class:

```java
package com.concepts;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import software.amazon.awssdk.services.s3.S3Client;
@RestController
@RequestMapping("/s3")
public class S3Controller {
@Autowired
private S3Client s3Client;
@GetMapping("/bucket-location")
public String bucketLocation() {
try {
String bucketName = "sj-payment-prod";
return s3Client.getBucketLocation(
request -> request.bucket(bucketName)
).locationConstraintAsString();
} catch (Exception e) {
return e.getMessage();
}
}
}
```

Just trying to access the Region name of specific bucket (specific resource)

Postman:

<details><summary>Screenshot 35 — transcribed text</summary>

<pre>
GET 
V 
http://localhost:8080/s3/bucket-location 
Params 
Authorization 
Headers (8) 
Body . 
Pre-request Script 
Tests 
Query Params 
Key 
Key 
Body Cookies 
Headers (5) 
Test Results 
Pretty 
Raw 
Preview 
Visualize 
IN 
Text 
V 
1 
ap-south-1
</pre>
</details>

But Notice that in UI: we are seeing Access denied when we are clicking on S3 Bucket list

<details><summary>Screenshot 36 — transcribed text</summary>

<pre>
2% ap-south-1.console.aws.amazon.com/s3/buckets?region=ap-south-1 
&#x27;2 Incognito (2) 
... 
1 
Account ID: 6435-3703-5131 &quot; 
aws 
Q Search 
[Option+S] @ 
Asia Pacific (Mumbai) v 
Shrayansh 
Amazon S3 &gt; Buckets 
Amazon S3 
Buckets 
· Buckets 
General purpose buckets 
All AWS Regions 
Directory buckets 
General purpose buckets 
Directory buckets 
Table buckets 
General purpose buckets (0) Info 
Copy ARN 
Empty 
Delete 
Create bucket 
Account snapshot Info 
View dashboard 
Vector buckets 
Buckets are containers for data stored in S3. 
Updated daily 
Files 
Q Find buckets by name 
&lt; 1 
&gt; 
Storage Lens provides visibility into storage usage and 
activity trends. 
File systems New 
AWS Region 
Creation date 
D 
Name 
Access management and 
security 
You don&#x27;t have permissions to list buckets 
Diagnose with Amazon Q 
External access summary Info 
Access Points 
After you or your AWS administrator has updated your permissions to allow the 
s3:ListAllMyBuckets action, refresh this page. Learn more about Identity and access 
Updated daily 
management in Amazon S3 L 
External access findings help you identify bucket 
Access Points for FSX 
permissions that allow public access or access from other 
Access Grants 
AWS accounts. 
IAM Access Analyzer 
Storage management and
</pre>
</details>

Its because, at this page (s3/buckets), AWS try to load all the buckets, not 1 specific resource (bucket) and our policy is attached with only 1 specific resource only.

That’s why with API, we can easily test by performing operation on specific resources, but through UI, we don’t have much choice, so to test on UI, we might need to add Identity Based policy (for Action: S3:ListAllMyBuckets

The simplified flow:

Call Arrives

Is there any Explicit DENY in applicable policy?

NO

YES

EXPLICT DENY

YES

NO

Is there any ALLOW in applicable policy?

ALLOW

IMPLICT DENY

Example:

Case 1 : Explicit Deny

Resource Policy

Identity Policy

```json
{
"Effect": "Deny",
"Principal": {
"AWS": "arn:aws:iam::643537035131:user/Shrayansh"
},
"Action": "s3:GetObject",
"Resource":"arn:aws:s3:::sj-payment-prod/*"
}
```

```json
{
"Effect": "Allow",
"Action": "s3:*",
"Resource": "*"
}
```

This policy says:

- All S3 Operations

- On all S3 buckets and its objects are ALLOWED

This policy says:

- S3 GetObject Operation is DENIED for all objects inside sj-payment-prod bucket

Now, lets say API call "GetObject(test.txt) on bucket sj-payment-prod comes.

As per 1 policy:

- Identity policy : this operations is Allowed

But as per 2nd policy:

- Resource policy: this operation is DENIED

What to do?

Check the flow:

GetObject(test.txt)

Call Arrives

Is there any Explicit DENY in applicable policy?

YES

DENY

AWS club all the applicable polices for the flow:  
 
Explicit DENY > Explicit Allow > Implicit Deny (default)

Case 2: Implicit Deny

There is no Policy exist itself

GetObject(test.txt)

Call Arrives

Is there any Explicit DENY in applicable policy?

NO

NO

Is there any ALLOW in applicable policy?

IMPLICT DENY

Case 3: Allow

Identity Policy

```json
{
"Effect": "Allow",
"Action": "s3:*",
"Resource": "*"
}
```

GetObject Call Arrives

Is there any Explicit DENY in applicable policy?

NO

YES

Is there any ALLOW in applicable policy?

ALLOW

## 3. IAM Group

- Group is just a container for the User.

- Its not an Identity, means we can not sign in as Group or generate key for the group.

One main advantage of Group is:

- it can hold Policies that apply to every user in it.

Ex: if we have 50 Users, we don’t need to attach policy individually. We can create a Group and assign policy to Group (only once), and While creation of User, simply add User to specific Group.

Group: BackendEngineer

Policies Attached:

- S3:*

- DynamoDB:*

Each user belongs to this group inherit the policies

User-N

User-2

User-1

Go to IAM -> User Groups -> Create Group

<details><summary>Screenshot 37 — transcribed text</summary>

<pre>
IAM &gt; IAM user groups 
Delete 
Identity and Access 
‹ 
IAM user groups (O) Info 
Create group 
Management (IAM) 
A user group is a collection of IAM users. Use groups to specify permissions for a collection of users. 
&lt; 1 &gt; 
Q Search IAM 
Q Search 
D 
Group name 
Users 
Permissions 
Creation time 
D 
Dashboard 
Access Management 
No resources to display 
Roles 
Policies 
IAM users 
IAM user groups 
Identity providers 
Account settings
</pre>
</details>

Provide Group Name -> Option to Add Users to group -> Attach Policies to Group -> Click Create Group

<details><summary>Screenshot 38 — transcribed text</summary>

<pre>
IAM &gt; IAM user groups &gt; Create user group 
Identity and Access 
&lt; 
Create user group 
Management (IAM) 
Q Search IAM 
Name the group 
User group name 
Dashboard 
Enter a meaningful name to identify this group. 
Access Management 
DevelopersGroup 
Maximum 128 characters. Use alphanumeric and &#x27;+=,.@ -_ &#x27; characters. 
Roles 
Policies 
IAM users 
Add users to the group - Optional (1) Info 
IAM user groups 
An IAM user is an entity that you create in AWS to represent the person or application that uses it to interact with AWS 
Identity providers 
&lt; 1 &gt; @ 
Account settings 
Q Search 
D 
User name 
Group: V 
D 
Root access management 
Last activity 
Creation time 
Temporary delegation requests 
O 
Shrayansh 
10 hours ago 
21 hours ago 
Access reports 
Access Analyzer 
Resource analysis 
Attach permissions policies - Optional (1/1150) Info 
Unused access 
You can attach up to 10 policies to this user group. All the users in this group will have permissions that are defined in the selected policies. 
Analyzer settings 
Filter by Type 
Credential report 
Q ALLOWALL 
X 
All types 
I match 
&lt; 1 &gt; 
Organization activity 
Used as 
D 
Service control policies 
Policy name 
Type 
Description 
Resource control policies 
AllowAllS3Actions 
Customer managed 
None 
Cancel 
Create user group 
Related consoles 
IAM Identity Center [ 
AWS Organizations
</pre>
</details>

If USER is not attached while Group Creation -> We can Go to IAM Users -> Select the USER -> Groups -> Add User to Group

<details><summary>Screenshot 39 — transcribed text</summary>

<pre>
User added to group &lt;b&gt;DevelopersGroup&lt;/b&gt; 
X 
Shrayansh Info 
Delete 
Summary 
ARN 
Console access 
Access key&quot; 
arn:aws:iam:643537035131:user/Shrayansh 
A Enabled without MFA 
AKIA**************** - Active 
Used today. 20 hours old. 
Created 
Last console sign-in 
Access key 2 
May 27, 2026, 14:41 (UTC+05:30) 
19 hours ago 
Create access key 
Permissions 
Groups (1) 
Tag 
Security credentials 
Last Accessed 
User groups membership 
Remove 
Add user to groups 
A user group is a collection of IAM users. Use groups to specify permissions for a collection of users. A user can be a member of up to 10 groups at a time. 
Group name 
Attached policies [ 
D 
1 
DevelopersGroup 
AllowAllS3Actions
</pre>
</details>

## 4. IAM Role

IAM Role provides temporary identity.

Instead of storing long-lived AWS Access and Secret Keys (which can create security risks), Users/Services can ASSUME a Role and get temporary credentials.

Example:

INTERN has READ only permission on S3.

SDE-3 has Read, Write permission on S3.

But

For 1 hour we need to provide INTERN all permissions with SDE-3 has

So how we can give?

Via ROLE.

- Create Role: SeniorSDERole

- Attach all the permission which SeniorSDE should have with this SeniorSDERole.

- Maintain the list of Identities which can Use this Role. Otherwise anyone can just use this Role, so we need some kind of whitelisting.

Role

SeniorSDERole

INTERN

Part-1

Part-2

Permissions Policy

Trust Policy

- What all operations you can do, after you get this role

- Who is allowed to get this Role

```json
{
"Version": "2012-10-17",
"Statement": [
{
"Effect": "Allow",
"Principal": {
"AWS": "arn:aws:iam::643537035131:user/Shrayansh"
},
"Action": "sts:AssumeRole"
}
]
}
```

```json
{
"Version": "2012-10-17",
"Statement": [
{
"Effect": "Allow",
"Action": "s3:*",
"Resource": "*"
}
]
}
```

All S3 endpoints are allowed on All S3 resources (buckets, objects etc.)

User Shrayansh belong to account 643537035131, is Allowed to invoked STS(Security Token Service) AssumeRole operation.

Role

SeniorSDERole

Trust Policy

INTERN

Permission Policy

attached

Policy

```json
{
"Version": "2012-10-17",
"Statement": [
{
"Effect": "Allow",
"Action": "sts:AssumeRole",
"Resource": "arn:aws:iam::643537035131:role/SeniorSDERole"
}
]
}
```

Allow invocation of AssumeRole operation of STS service on given Role

Got to IAM Roles -> Click Create Role

<details><summary>Screenshot 40 — transcribed text</summary>

<pre>
IAM 
&gt; Roles 
Identity and Access 
‹ 
Roles (3) Info 
Delete 
Create role 
Management (IAM) 
An IAM role is an identity you can create that has specific permissions with credentials that are valid for short durations. Roles can be assumed by entities that you trust. 
Q Search IAM 
Q Search 
&lt; 1 &gt; 
D 
- 
Dashboard 
Role name 
Trusted entities 
Last activity 
Access Management 
AWSServiceRoleForResourceExplorer 
AWS Service: resource-explorer-2 (Se 2 hours ago 
Roles 
AWSServiceRoleForSupport 
AWS Service: support (Service-Linker 
Policies 
AWSServiceRoleForTrustedAdvisor 
AWS Service: trustedadvisor (Service 
IAM users 
IAM user groups 
Identity providers 
Roles Anywhere Info 
Manage 
Account settings 
Authenticate your non AWS workloads and securely provide access to AWS services.
</pre>
</details>

Click on Custom Trust Policy -> add the trust policy

<details><summary>Screenshot 41 — transcribed text</summary>

<pre>
IAM &gt; Roles &gt; Create role 
Step&quot; 
Select trusted entity 
Select trusted entity Info 
Step 2 
Add permissions 
Trusted entity type 
Step 3 
Name, review, and create 
AWS service 
AWS account 
Web identity 
Allow AWS services like EC2, Lambda, or 
Allow entities in other AWS accounts 
Allows users federated by the specified 
others to perform actions in this account. 
belonging to you or a 3rd party to perform 
external web identity provider to assume 
actions in this account. 
this role to perform actions in this account. 
SAML 2.0 federation 
Custom trust policy 
Allow users federated with SAML 2.0 from 
Create a custom trust policy to enable 
a corporate directory to perform actions in 
others to perform actions in this account. 
this account. 
Custom trust policy 
Create a custom trust policy to enable others to perform actions in this account. 
1 &#x27; { 
2 
&quot;Version&quot;: &quot;2012-10-17&quot;, 
Edit statement 
3 &quot; 
&#x27;Statement&quot;: [ 
4 &quot; 
5 
&quot;Effect&quot;: &quot;Allow&quot;, 
6 
&quot;Principal&quot;: { 
7 
&quot;AWS&quot;: &quot;arn: aws: iam: : 643537035131:user/Shrayansh&quot; 
8 
}, 
9 
&quot;Action&quot;: &quot;sts: AssumeRole&quot; 
Select a statement 
10 
3 
11 
] 
Select an existing statement in the policy or 
12 
add a new statement. 
13 
+ Add new statement
</pre>
</details>

<details><summary>Screenshot 42 — transcribed text</summary>

<pre>
Policy editor 
1 . { 
2 
&quot;Version&quot;: &quot;2012-10-17&quot;, 
3 
&quot;Statement&quot;: [ 
4 
5 
&quot;Effect&quot;: &quot;Allow&quot;, 
6 
&quot;Action&quot;: &quot;s3 :* &quot;, 
7 
&quot;Resource&quot; : 
8 
9 
10 
}
</pre>
</details>

Now attach Permissions Policy

<details><summary>Screenshot 43 — transcribed text</summary>

<pre>
= IAM &gt; Roles &gt; Create role 
Step 1 
Select trusted entity 
Add permissions Info 
Step 2 
Add permissions 
Permissions policies (1/1150) Info 
Step 3 
Choose one or more policies to attach to your new role. 
Name, review, and create 
Filter by Type 
Q AllowAll 
X 
All types 
1 match 
&lt; 1 &gt; 
Policy name [ 
Type 
Description 
+ 
AllowAllS3Actions 
Customer managed 
Set permissions boundary - optional 
Cancel 
Previous 
Next
</pre>
</details>

Provide Role Name and Review Trust Policy and Permissions attached

<details><summary>Screenshot 44 — transcribed text</summary>

<pre>
IAM &gt; Roles &gt; Create role 
Step 2 
Add permissions 
Role details 
Step 3 
Role name 
Name, review, and create 
Enter a meaningful name to identify this role. 
SeniorSDERole 
Maximum 64 characters. Use alphanumeric and &#x27;+=,.@ -_ &#x27; characters. 
Description 
Add a short explanation for this role. 
Maximum 1000 characters. Use letters (A-Z and a-z), numbers (0-9), tabs, new lines, or any of the following characters: _ +=,. @-/\[0]!#$%^*();&quot;&gt; 
Step 1: Select trusted entities 
Edit 
Trust policy 
1 - { 
2 
&quot;Version&quot;: &quot;2012-10-17&quot;, 
3 . 
&quot;Statement&quot;: [ 
4 - 
5 
&quot;Effect&quot;: &quot;Allow&quot;. 
6 
&#x27;Principal&quot;: { 
7 
&quot;AWS&quot;: &quot;arn: aws: iam: : 643537035131:user/Shrayansh&quot; 
8 
&quot;Action&quot;: &quot;sts: AssumeRole&quot; 
} , 
10 
} 
12 
Step 2: Add permissions 
Edit 
Permissions policy summary 
Attached as 
D 
Policy name [ 
D 
Type 
AllowAllS3Actions 
Customer managed 
Permissions policy
</pre>
</details>

Now attach policy with USER

Go to IAM User -> Permissions -> Inline Policy

<details><summary>Screenshot 45 — transcribed text</summary>

<pre>
1 inline policy removed 
X 
Shrayansh Info 
Delete 
Summary 
ARN 
Console access 
Access key 1 
arn:aws:iam :: 643537035131:user/Shrayansh 
A Enabled without MFA 
AKIA**************** - Active 
Used today. Created yesterday. 
Created 
Last console sign-in 
Access key 2 
May 27, 2026, 14:41 (UTC+05:30) 
Today 
Create access key 
Permissions 
Groups 
Tag: 
Security credentials 
Last Accessed 
Permissions policies (0) 
Remove 
Add permissions 
Permissions are defined by policies attached to the user directly or through groups. 
Add permissions 
Filter by Type 
Create inline policy 
Q Search 
All types 
&lt; 1 
Policy name [2 
Type 
Attached via 
No resources to display
</pre>
</details>

Add Policy document

<details><summary>Screenshot 46 — transcribed text</summary>

<pre>
IAM &gt; IAM users &gt; Shrayansh &gt; Create policy 
III 
Step 1 
Specify permissions 
Specify permissions Info 
Step 2 
Add permissions by selecting services, actions, resources, and conditions. Build permission statements using the JSON editor. 
Review and create 
Policy editor 
Visual 
JSON 
Actions 
1 &#x27; 
IN m + m on ong 
2 
&quot;Version&quot;: &quot;2012-10-17&quot;, 
Edit statement 
Remove 
3 
&quot;Statement&quot;: [ 
4 &quot; 
5 
&quot;Effect&quot;: &quot;Allow&quot; 
Add actions 
------- 
6 
&quot;Action&quot;: &quot;sts : AssumeRole&quot; 
&quot;Resource&quot; : &quot;arn: aws : iam: : 643537035131 : role/SeniorSDERole&#x27; 
Choose a service 
Q Search services 
10 
Included 
STS
</pre>
</details>

Give policy Name -> Create Policy

<details><summary>Screenshot 47 — transcribed text</summary>

<pre>
IAM &gt; IAM users &gt; Shrayansh &gt; Create policy 
III 
Step 1 
Specify permissions 
Review and create Info 
Step 2 
Review the permissions, specify details, and tags. 
Review and create 
Policy details 
Policy name 
Enter a meaningful name to identify this policy. 
AssumePolicyForSeniorSDERole 
Maximum 128 characters. Use alphanumeric and &#x27;+=,.@ -_ &#x27; characters. 
Permissions defined in this policy Info 
Edit 
Permissions defined in this policy document specify which actions are allowed or denied. To define permissions for an IAM identity (user, user group, or role), attach a policy to it 
Q Search 
Allow (1 of 469 services) 
Show remaining 468 services 
Service 
Access level 
Resource 
Request condition 
D 
1 
STS 
Limited: Write 
RoleName| string like 
|SeniorSDERole 
None 
Cancel 
Previous 
Create policy
</pre>
</details>

Done

<details><summary>Screenshot 48 — transcribed text</summary>

<pre>
IAM &gt; IAM users &gt; Shrayansh 
Identity and Access 
X 
V 
Policy AssumePolicyForSeniorSDERole created. 
Management (IAM) 
Shrayansh Info 
Delete 
Q Search IAM 
Dashboard 
Summary 
Access Management 
ARN 
Console access 
Access key 1 
arn:aws:iam:643537035131:user/Shrayansh 
A Enabled without MFA 
AKIA**************** - Active 
Roles 
Used today. Created yesterday. 
Policies 
IAM users 
Created 
Last console sign-in 
Access key 2 
May 27, 2026, 14:41 (UTC+05:30) 
Today 
Create access key 
IAM user groups 
Identity providers 
Account settings 
Permissions 
Groups 
Tags 
Security credentials 
Last Accessed 
Root access management 
Temporary delegation requests 
Permissions policies (1) 
Remove 
Add permissions 
Access reports 
Permissions are defined by policies attached to the user directly or through groups. 
Access Analyzer 
Filter by Type 
Resource analysis 
Q Search 
All types 
&lt; 1 &gt; 
Unused access 
- 
Policy name [^ 
1 
Analyzer settings 
Type 
Attached via L 
Credential report 
AssumePolicyForSeniorSDERole 
Customer inline 
Inline 
Organization activity 
Service control policies 
Resource control policies 
Permissions boundary (not set)
</pre>
</details>

Now, Login as "Shrayansh" User which has this Role assigned and try to access S3

<details><summary>Screenshot 49 — transcribed text</summary>

<pre>
Account ID: 6435-3703-5131 &quot; 
aws 
Q Search 
[Option+S] 
Asia Pacific (Mumbai) v 
hrayansh 
Amazon S3 &gt; Buckets 
V 
Amazon S3 
Buckets 
Buckets 
General purpose buckets 
All AWS Regions 
Directory buckets 
General purpose buckets 
Directory buckets 
General purpose buckets (0) Info 
Copy ARN 
Empty 
Delete 
Create bucket 
Account snapshot Info 
Table buckets 
View dashboard 
Vector buckets 
Buckets are containers for data stored in S3. 
Updated daily 
Files 
Q Find buckets by name 
&lt; 1 &gt; 
Storage Lens provides visibility into storage usage and 
activity trends. 
File systems New 
Creation date 
D 
AWS Region 
D 
Name 
Access management and 
security 
You don&#x27;t have permissions to list buckets 
Diagnose with Amazon Q 
External access summary Info 
After you or your AWS administrator has updated your permissions to allow the 
Access Points 
s3: ListAllMyBuckets action, refresh this page. Learn more about Identity and access 
Updated daily 
Access Points for FSX 
management in Amazon S3 L 
External access findings help you identify bucket 
permissions that allow public access or access from other 
Access Grants 
AWS accounts. 
IAM Access Analyzer 
Storage management and 
insights 
Storage Lens 
Batch Operations
</pre>
</details>

Still Access Denied.

Its because, Shrayansh user has the ROLE, but it need to assume now (in other word, its need to invoke AssumeRole endpoint and then only it will get those permissions for limited period of time).

In UI how to invoke?

Go to your Role -> copy Link to switch roles in console

<details><summary>Screenshot 50 — transcribed text</summary>

<pre>
Q Search 
Global v 
aws-learning (643537035131) 
aws 
Option+S] 
aws-learning 
IAM &gt; Roles &gt; 
SeniorSDERole 
Identity and Access 
&lt; 
SeniorSDERole Info 
Delete 
Management (IAM) 
Summary 
Edit 
Q Search IAM 
Creation date 
ARN 
Link to switch roles in console 
Dashboard 
May 28, 2026, 16:05 (UTC+05:30) 
arn:aws:iam:643537035131:role/SeniorSDERole 
https://signin.aws.amazon.com/switchrole? 
roleName=SeniorSDERole&amp;account=643537035131 
Access Management 
Roles 
Last activity 
Maximum session duration 
Policies 
1 hour 
IAM users 
IAM user groups 
Permissions 
Trust relationships 
Tags 
Last Accessed 
Revoke sessions 
Identity providers 
Account settings 
Root access management 
Permissions policies (1) Info 
Simulate La 
Remove 
Add permissions 
Temporary delegation requests 
You can attach up to 10 managed policies. 
Filter by Type 
Access reports 
Q Search 
All types 
&lt; 1 &gt; 
Access Analyzer 
Resource analysis 
Policy name [ 
1 
Type 
Attached entities 
D 
Unused access 
AllowAllS3Actions 
Customer managed 
1 
Analyzer settings 
Credential report 
Organization activity 
Permissions boundary (not set) 
Service control policies
</pre>
</details>

Now, go to the browser where "Shrayansh" user is login and paste this link

Click on Switch Role

<details><summary>Screenshot 51 — transcribed text</summary>

<pre>
· signin.aws.amazon.com/switchrole?roleName=SeniorSDERole&amp;account=643537035131 
0 
aws 
Switch Role 
Switching roles enables you to manage resources across Amazon Web Services accounts using a single user. When you 
switch roles, you temporarily take on the permissions assigned to the new role. When you exit the role, you give up 
those permissions and get your original permissions back. Learn more [ 
Account ID 
The 12-digit account number or the alias of the account in which the role exists. 
643537035131 
IAM role name 
The name of the role that you want to assume which can be found at the end of the role&#x27;s ARN. For example, provide the TestRole role 
name from the following role ARN: arn:aws:iam:123456789012:role/TestRole. 
SeniorSDERole 
Display name - optional 
This name will appear in the console navigation bar when active. Choose a name to help identify the permission set assigned to the role. 
SeniorSDERole @ 643537035131 
Display color - optional 
The selected color displays in the console navigation when this role is active 
None 
Cancel 
Switch Role
</pre>
</details>

And Now "Shrayansh" User has assumed SeniorSDERole for limited time

<details><summary>Screenshot 52 — transcribed text</summary>

<pre>
bl Incognito (2) 
.... 
€ 
eu-north-1.console.aws.amazon.com/s3/home?region=eu-north-1# 
1 
Account ID: 6435-3703-5131 
aws 
Q Search 
[Option+S] @ 
Europe (Stockholm) 
SeniorSDERole @ 643537035131 
Amazon S3 
Currently active as 
SeniorSDERole 
V 
Amazon S3 
Buckets 
Account ID 
6435-3703-5131 
Buckets 
General purpose buckets 
All AWS Regions 
Directory buckets 
Account name 
General purpose buckets 
Access denied 
Directory buckets 
Account color 
Table buckets 
General purpose buckets (1) Info 
Copy ARN 
Empty 
Delete 
Create bucket 
Account snaps 
Access denied 
-------- 
Vector buckets 
Buckets are containers for data stored in S3. 
Updated daily 
Files 
Q Find buckets by name 
&lt; 1 
&gt; 
Storage Lens provide 
Account 
activity trends. 
Organization 
D 
File systems New 
Name 
AWS Region 
Creation date 
Service Quotas 
O 
Access management and 
sj-payment-prod 
Asia Pacific (Mumbai) ap-south-1 
May 28, 2026, 00:11:29 (UTC+05:30) 
Billing and Cost Management 
security 
External acces 
Access Points 
Updated daily 
Access Points for FSX 
External access findi 
Signed in as 
permissions that allo 
Shrayansh 
Access Grants 
AWS accounts. 
Account ID 
IAM Access Analyzer 
6435-3703-5131 
Storage management and 
Switch back 
insights
</pre>
</details>

S3 working fine

*The embedded screenshots are represented by their OneNote accessible text. Access key IDs appearing in screenshots have been masked for publication.*

[AWS index](./index.md) · [AWS Networking - Part1 →](./AWS%20Networking%20-%20Part1.md)
