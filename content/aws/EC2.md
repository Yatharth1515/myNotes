---
title: "EC2"
tags:
  - aws
  - ec2
---

# EC2

[AWS index](./index.md) · [← Terraform - Part2](./Terraform%20-%20Part2.md) · [Auto Scaling Group →](./Auto%20Scaling%20Group.md)

*OneNote page: Thursday, 9 July 2026 10:40 PM*

## At a glance

- EC2 is a virtual machine running in an AWS data center.
- The walkthrough chooses an instance type, key pair, network and storage, then deploys a Spring Boot JAR.

## On this page

- [What EC2 is](#what-ec2-is)
- [Launch and configure an instance](#launch-and-configure-an-instance)
- [Connect and deploy a Spring Boot app](#connect-and-deploy-a-spring-boot-app)
- [Security group and verification](#security-group-and-verification)

## Complete page transcription

### What EC2 is

EC2 (Elastic Compute Cloud):

We can think of it as renting a virtual computer (virtual machine) in AWS data center.

Each EC2 behaves like a independent computer, where we can:

○Access remotely via SSH

○Install any software (Java, MySQL, etc.)

○Run our applications 24/7

○Pay only for what we use

### Launch and configure an instance

<details><summary>Screenshot 1: accessible text</summary>

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

<details><summary>Screenshot 2: accessible text</summary>

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

<details><summary>Screenshot 3: accessible text</summary>

<pre>
Key pair (login) Info 
You can use a key pair to securely connect to your instance. Ensure that you have access to the selected key pair before you launch the instance. 
Key pair name - required 
my-ec2-key 
C&#x27; Create new key pair
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

<details><summary>Screenshot 7: accessible text</summary>

<pre>
Instances (1/1) Info 
Connect 
Instance state 
Actions 
Launch instances 
Saved filter sets 
V 
Choose filter set v 
Q Find Instance by attribute or tag (case-sensitive) 
Name 
Instance ID 
Instance state 
Instance type 
Status check 
Availability Zone 
Public IPv4 DNS 
Public IPv4 ... 
Elastic IP 
D 
D 
D 
D 
myec2 
i-09a08201fe7abb9b2 
Running 
t3.micro 
3/3 checks passec 
ap-south-1a 
ec2-13-232-14-79.ap-s ... 
13.232.14.79 
i-09a08201fe7abb9b2 (myec2) 
V 
Details 
Status and alarms 
Monitoring 
Security 
Networking 
Storage 
Tags 
Instance summary Info 
Instance ID 
Public IPv4 address 
Private IPv4 addresses 
i-09a08201fe7abb9b2 
13.232.14.79 | open address 
172.31.37.181 
IPv6 address 
Instance state 
Public DNS 
Running 
ec2-13-232-14-79.ap-south-1.compute.amazonaws.com | open 
address
</pre>
</details>

### Connect and deploy a Spring Boot app

<details><summary>Screenshot 8: accessible text</summary>

<pre>
shrayanshjain@Shrayanshs-MacBook-Pro-2 Downloads % ssh - i ~/ . ssh/my-ec2-key.pem ec2-user@13.232.14.79 
The authenticity of host &#x27;13.232.14.79 (13.232.14.79) &#x27; can&#x27;t be established. 
ED25519 key fingerprint is SHA256 : bjQuViQd+wmaUF6PCs6pNlicTj3R1FlKdG8Lmb+kTV8. 
This key is not known by any other names. 
Are you sure you want to continue connecting (yes/no/ [fingerprint ] ) ? yes 
Warning: Permanently added &#x27;13. 232. 14.79&#x27; (ED25519) to the list of known hosts. 
# 
#### 
Amazon Linux 2023 
#####\ 
\###| 
\#/ 
https : / /aws . amazon . com/ linux/ amazon-linux-2023 
V~&#x27; 
/m/ 
[ec2-user@ip-172-31-37-181 ~]$
</pre>
</details>

<details><summary>Screenshot 9: accessible text</summary>

<pre>
Verifying 
: xml-common-0.6.3-56.amzn2023.0.2.noarch 
Installed: 
alsa-lib-1.2.7.2-1.amzn2023.0.3.x86_64 
cairo-1.18.0-4.amzn2023.0.3.x86_64 
dejavu-sans-fonts-2.37-16.amzn2023.0.2. noarch 
dejavu-serif-fonts-2.37-16.amzn2023.0.2.noarch 
dejavu-sans-mono-fonts-2.37-16.amzn2023.0.2. noarch 
fontconfig-2.13.94-2. amzn2023. 0.2. x86 64 
fonts-filesystem-1:2.0.5-12.amzn2023.0.2.noarch 
freetype-2.13.2-5.amzn2023.0.2. x86_64 
google-noto-fonts-common-20240401-1.amzn2023.0.2. noarch 
graphite2-1.3.14-7.amzn2023.0.3. x86 64 
google-noto-sans-vf - fonts-20240401-1.amzn2023.0.2. noarch 
harfbuzz-7.0.0-2.amzn2023. 0. 2. x86 64 
java-17 -amazon-corretto-devel - 1 : 17. 0. 19+10-1.amzn2023.1. x86_64 
java-17 - amazon- corretto-headless - 1 : 17. 0. 19+10-1. amzn2023. 1. x86_64 
javapackages-filesystem-6.0.0-7.amzn2023.0.6. noarch 
libX11-1.8.10-2.amzn2023. 0.1.x86_64 
Langpacks-core-font-en-3.0-21.amzn2023.0.4. noarch 
libX11-common-1.8.10-2.amzn2023.0.1.noarch 
libXau-1.0.11-6.amzn2023.0.1. x86_64 
libXext-1.3.6-1. amzn2023. 0.1. x86_64 
libXrender-0.9.11-6.amzn2023. 0.1. x86_64 
libbrotli-1.0.9-4.amzn2023.0.2.x86_64 
libjpeg-turbo-2.1.4-2. amzn2023. 0. 5. x86 64 
libpng-2: 1. 6. 37-10. amzn2023. 0 . 13. x86_64 
libxcb-1.17.0-1.amzn2023.0.1. x86 64 
pixman-0.43.4-1.amzn2023.0.4.x86_64 
xml-common-0.6.3-56.amzn2023.0.2.noarch 
Complete ! 
[ec2-user@ip-172-31-37-181 ~]$ java -version 
openjdk version &quot;17.0.19&quot; 2026-04-21 LTS 
OpenJDK Runtime Environment Corretto-17.0.19.10.1 (build 17.0.19+10-LTS) 
OpenJDK 64-Bit Server VM Corretto-17.0.19.10.1 (build 17.0.19+10-LTS, mixed mode, sharing) 
[ec2-user@ip-172-31-37-181 ~]$
</pre>
</details>

<details><summary>Screenshot 10: accessible text</summary>

<pre>
... 
Project v 
m pom.xml (demo) 
. . 
C DemoApplication.java 
democontroller.java X 
resources 
1 
package com. example. demo; 
A 2 V 2 ^ V 
&gt; 
test 
2 
V 
target 
3 
import org. springframework.web.bind.annotation. GetMapping; 
&gt; 
classes 
4 
import org. springframework. web. bind. annotation. RestController; 
&gt; 
generated-sources 
5 
m 
generated-test-sources 
6 
@RestController 
&gt; 
maven-archiver 
7 
public class democontroller 
8 
maven-status 
9 
@GetMapping ( &quot;/&quot;) 
surefire-reports 
10 
public String hello () { 
&gt; 
test-classes 
11 
return &quot;Hi all - from shrayansh&quot;; 
3 demo-0.0.1-SNAPSHOT.jar 
12 
demo-0.0.1-SNAPSHOT.jar.original 
13 
= . gitattributes 
14 
Terminal 
Local X 
[ INFO] 
[INFO] BUILD SUCCESS 
[ INFO] 
[INFO] Total time: 
2.356 S 
[INFO] Finished at: 07/12/2026 10:02:24 
[INFO] 
shrayanshjain@Shrayanshs-MacBook-Pro-2 demo %
</pre>
</details>

<details><summary>Screenshot 11: accessible text</summary>

<pre>
shrayanshjain@Shrayanshs-MacBook-Pro-2 Downloads % scp - i ~/ . ssh/my-ec2-key . pem ~/ Downloads/demo/target/demo-0. 0.1-SNAPSHOT. jar ec2-user@13.232.14.79:/home/ec2-user/ 
demo-0.0.1 - SNAPSHOT. jar 
100% 
19MB 
5.3MB / S 
00:03 
shrayanshjain@Shrayanshs-MacBook-Pro-2 Downloads %
</pre>
</details>

<details><summary>Screenshot 12: accessible text</summary>

<pre>
[ec2-user@ip-172-31-37-181 ~]$ java -jar demo-0.0.1-SNAPSHOT.jar 
=== 
: : Spring Boot : : 
(v4.1.0) 
2026-07-12T10:10: 15.721Z INFO 26675 
- - - [demo] [ 
main] com. example . demo . DemoApplication 
Starting DemoApplication v0.0.1-SNAPSHOT using Java 17.0.19 wi 
th PID 26675 (/home/ec2-user / demo-0. 0. 1-SNAPSHOT . jar started by ec2-user in /home/ec2-user) 
2026-07-12T10:10: 15.748Z INFO 26675 
[demo] 
main] com. example . demo . DemoApplication 
No active profile set, falling back to 1 default profile: &quot;def 
ault&quot; 
07/12/2026 10:10:17 
INFO 26675 
[demo ] 
main] 
o . s . boot . tomcat . TomcatWebServer 
Tomcat initialized with port 8080 (http) 
... 
2026-07-12T10 : 10 : 17. 501Z 
INFO 26675 
[ demo] 
main] 
07/12/2026 10:10:17 
o . apache . catalina . core . StandardService 
Starting service [Tomcat] 
INFO 
26675 
[ demo] 
main] 
o . apache . catalina . core . StandardEngine 
Starting Servlet engine: [Apache Tomcat/11. 0.22] 
2026-07-12T10 : 10 :17.550Z 
INFO 
26675 
[ demo ] 
main] 
b. w. c.s. WebApplicationContextInitializer 
Root WebApplicationContext: initialization completed in 1627 m 
2026-07-12T10 : 10 :18.131Z 
INFO 
26675 
[ demo ] 
main] 
o . s . boot . tomcat . TomcatWebServer 
Tomcat started on port 8080 (http) with context path &#x27;/&#x27; 
2026-07-12T10 : 10:18.155Z 
INFO 26675 
[ demo] 
main 
com . example . demo . DemoApplication 
Started DemoApplication in 3. 356 seconds (process running for 
4.236)
</pre>
</details>

<details><summary>Screenshot 13: accessible text</summary>

<pre>
shrayanshjain@Shrayanshs-MacBook-Pro-2 Downloads % curl http://13.232.14.79:8080/
</pre>
</details>

### Security group and verification

<details><summary>Screenshot 14: accessible text</summary>

<pre>
Name 
Instance type 
Public IPv4 DNS 
Public IPv4 ... 
Elastic IP 
D 
Availability Zone 
D 
D 
Instance ID 
Instance state 
Status check 
myec2 
i-09a08201fe7abb9b2 
Running QQ 
t3.micro 
3/3 checks passec 
ap-south-1a 
ec2-13-232-14-79.ap-s ... 
13.232.14.79 
- 
&gt; 
i-09a08201fe7abb9b2 (myec2) 
Q Filter rules 
&lt; 1 &gt; 
- 
Name 
Security group rule ID 
Port range 
Protocol 
Source 
Security groups 
Description 
- 
sgr-0e9c462cc710f081c 
80 
TCP 
0.0.0.0/0 
launch-wizard-2 La 
- 
sgr-059b977c6630c83ec 
TCP 
0.0.0.0/0 
launch-wizard-2 L 
- 
22
</pre>
</details>

<details><summary>Screenshot 15: accessible text</summary>

<pre>
Inbound rules (3) 
Manage tags 
Edit inbound rules 
V 
A 
- 
Q Search 
Port range 
Source 
D 
D 
Type 
Protocol 
D 
IP version 
D 
Security group rule ID V 
D 
Name 
sgr-0a47da9458754728e 
IPv4 
Custom TCP 
TCP 
8080 
0.0.0.0/0 
sgr-0e9c462cc710f081c 
IPV4 
HTTP 
TCP 
80 
0.0.0.0/0 
sgr-059b977c6630c83ec 
IPv4 
SSH 
TCP 
22 
0.0.0.0/0
</pre>
</details>

<details><summary>Screenshot 16: accessible text</summary>

<pre>
shrayanshjain@Shrayanshs -MacBook-Pro-2 Downloads % curl http: / /13.232. 14.79: 8080/ 
Hi all - from shrayansh% 
shrayanshjain@Shrayanshs-MacBook-Pro-2 Downloads %
</pre>
</details>

<details><summary>Screenshot 17: accessible text</summary>

<pre>
V 
13.232.14.79:8080 
X 
+ 
A 
Not Secure 
13.232.14.79:8080 
Hi all - from shrayansh
</pre>
</details>

[AWS index](./index.md) · [← Terraform - Part2](./Terraform%20-%20Part2.md) · [Auto Scaling Group →](./Auto%20Scaling%20Group.md)

*Screenshots are represented by their OneNote accessible text. The original bitmap images are not embedded; image text may contain recognition errors.*
