---
title: "EBS (Elastic Block Store)"
tags:
  - aws
  - storage
---

# EBS (Elastic Block Store)

[AWS index](./index.md) · [← Application Load Balancer](./Application%20Load%20Balancer.md) · [S3 - Part1 (Architecture) →](./S3%20-%20Part1%20%28Architecture%29.md)

*OneNote page: Monday, 7 September 2026 5:18 PM*

## At a glance

- The screenshots create an EBS volume, attach it to EC2, format it and mount it.
- The command output is retained as accessible screenshot text.

## On this page

- [EBS workflow](#ebs-workflow)
- [Create and attach a volume](#create-and-attach-a-volume)
- [Format and mount the volume](#format-and-mount-the-volume)
- [Verify storage](#verify-storage)

## Complete page transcription

### EBS workflow

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

### Create and attach a volume

<details><summary>Screenshot 2: accessible text</summary>

<pre>
Create volume 
Info 
Create an Amazon EBS volume to attach to any EC2 instance in the same Availability Zone. 
Volume settings 
Volume type | Info 
General Purpose SSD (gp3) 
General Purpose SSD (gp3) 
General Purpose SSD (gp2) 
Provisioned IOPS SSD (io1) 
Provisioned IOPS SSD (io2) 
Cold HDD (sc1) 
Throughput Optimized HDD (st1) 
Magnetic (standard)
</pre>
</details>

<details><summary>Screenshot 3: accessible text</summary>

<pre>
Volumes (1/3) Info 
Last updated 
less than a minute ago 
La Recycle Bin 
Actions 
Create volume 
Saved filter sets 
Modify volume 
Choose filter set 
Q Search 
1 
&gt; 
Create snapshot 
Name 
Volume ID 
Type 
IOPS 
D V 
Source volu 
D 
D 
Size 
D 
Create snapshot lifecycle policy 
vol-0e0be39d497cec901 
gp3 
100 GiB 
3000 
Delete volume 
- 
vol-Oaeace614cbf77292 
gpƷ 
17 GiB 
3000 
Attach volume 
- 
vol-0548498ce0bc05e5a 
gpƷ 
8 GiB 
3000 
Detach volume 
26f ... 
Force detach volume 
Manage auto-enabled I/O 
Volume ID: vol-0aeace614cbf77292 
Copy volume - new 
&gt; 
Manage tags 
Details 
Status checks 
Monitoring 
Tags 
Resilience testing
</pre>
</details>

<details><summary>Screenshot 4: accessible text</summary>

<pre>
Attach volume 
Info 
Attach a volume to an instance to use it as you would a regular physical hard disk drive. 
Basic details 
Volume ID 
vol-Oaeace614cbf77292 
Availability Zone 
aps1-az1 (ap-south-1a) 
Instance 
Info 
i-042ea3e9d1047621a 
(my-ec2) (running) 
Only instances in the same Availability Zone as the selected volume are displayed. 
Device name 
Info 
/dev/sdf 
Recommended device names for Linux: /dev/xvda for root volume. /dev/sd[f-p] for data volumes.
</pre>
</details>

### Format and mount the volume

<details><summary>Screenshot 5: accessible text</summary>

<pre>
[ec2-user@ip-172-31-41-183 ~]$ lsblk 
NAME 
MAJ : MIN RM SIZE RO TYPE MOUNTPOINTS 
nvme0n1 
259:0 
8G 
0 disk 
-nvme0n1p1 
259 :1 
0 
8G 
0 
part / 
-- nvme0n1p127 259 :2 
0 
1M 
0 
part 
L-nvme0n1p128 259 : 3 
10M 
0 part / boot/efi 
nvme2n1 
259 :5 
0 
17G 
0 disk 
[ ec2-user@ip-172-31-41-183 ~]$
</pre>
</details>

<details><summary>Screenshot 6: accessible text</summary>

<pre>
[ec2-user@ip-172-31-41-183 ~]$ sudo file -s /dev/nvme2n1 
/ dev / nvme2n1 : data 
[ec2-user@ip-172-31-41-183 ~]$
</pre>
</details>

<details><summary>Screenshot 7: accessible text</summary>

<pre>
[ ec2-user@ip-172-31-41-183 ~] $ sudo mkfs -t xfs /dev/nvme2n1 
meta-data=/ dev/ nvme2n1 
isize=512 
agcount=16, agsize=278528 blks 
sectsz=512 
attr=2, projid32bit=1 
crc=1 
finobt=1, sparse=1, rmapbt=0 
E 
reflink=1 
bigtime=1 inobtcount=1 nrext64=0 
E 
exchange=0 
data 
= 
bsize=4096 
blocks=4456448, imaxpct=25 
= 
sunit=1 
swidth=1 blks 
naming 
=version 2 
bsize=4096 
ascii-ci=0, ftype=1, parent=0 
log 
=internal log 
bsize=4096 
blocks=16384, version=2 
= 
sectsz=512 
sunit=1 blks, lazy-count=1 
realtime =none 
extsz=4096 
blocks=0, rtextents=0 
[ ec2-user@ip-172-31-41-183 ~]$ sudo file -s /dev/nvme2n1 
/ dev/nvme2n1: SGI XFS filesystem data (blksz 4096, inosz 512, v2 dirs) 
[ec2-user@ip-172-31-41-183 ~]$
</pre>
</details>

### Verify storage

<details><summary>Screenshot 8: accessible text</summary>

<pre>
[ ec2-user@ip-172-31-41-183 ~]$ lsblk 
NAME 
MAJ : MIN RM SIZE RO TYPE MOUNTPOINTS 
nvme0n1 
259:0 
O 
8G 
0 disk 
-nvme0n1p1 
259:1 
O 
8G 
0 
-nvme0n1p127 259:2 
part / 
0 
1M 
0 part 
L-nvme0n1p128 259 : 3 
0 
10M 
0 part /boot/efi 
nvme2n1 
259 : 5 
0 
17G 
0 disk /data 
[ec2-user@ip-172-31-41-183 ~]$
</pre>
</details>

<details><summary>Screenshot 9: accessible text</summary>

<pre>
[[ec2-user@ip-172-31-41-183 ~]$ echo &quot;Hello&quot; | sudo tee /data/demo.txt 
Hello 
[[ec2-user@ip-172-31-41-183 ~]$ echo &quot;Hello&quot; | sudo tee demo.txt 
Hello
</pre>
</details>

<details><summary>Screenshot 10: accessible text</summary>

<pre>
[ [ec2-user@ip-172-31-41-183 ~]$ df -h 
Filesystem 
Size 
Used Avail Use% Mounted on 
devtmpfs 
420M 
0 
420M 
0% / dev 
tmpfs 
457M 
0 
457M 
0% / dev / shm 
tmpfs 
183M 
448K 
183M 
1% / run 
efivarfs 
128K 
2.7K 
121K 
3% / sys/ firmware/efi /efivars 
/ dev/ nvme0n1p1 
8.0G 
2.6G 
5.5G 
32% / 
tmpfs 
457M 
32K 
457M 
1% / tmp 
/ dev/ nvme0n1p128 
10M 
1.3M 
8.7M 
13% /boot/efi 
/ dev/ nvme2n1 
17G 
154M 
17G 
1% / data 
tmpfs 
92M 
0 
92M 
0% / run/ user / 1000 
[[ec2-user@ip-172-31-41-183 ~]$ df -h demo.txt 
Filesystem 
Size 
Used Avail Use% Mounted on 
/ dev/nvme0n1p1 
8. 0G 
2.6G 5.5G 
32% / 
[[ec2-user@ip-172-31-41-183 ~]$ df -h /data/demo.txt 
Filesystem 
Size 
Used Avail Use% Mounted on 
/ dev/ nvme2n1 
17G 
154M 
17G 
1% / data 
[ec2-user@ip-172-31-41-183 ~]$
</pre>
</details>

[AWS index](./index.md) · [← Application Load Balancer](./Application%20Load%20Balancer.md) · [S3 - Part1 (Architecture) →](./S3%20-%20Part1%20%28Architecture%29.md)

*Screenshots are represented by their OneNote accessible text. The original bitmap images are not embedded; image text may contain recognition errors.*
