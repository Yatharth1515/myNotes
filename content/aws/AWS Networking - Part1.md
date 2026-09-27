---
title: "AWS Networking - Part1"
tags:
  - aws
  - networking
---

# AWS Networking - Part1

[AWS index](./index.md) · [← IAM (User, Policy, Groups and Roles)](./IAM%20%28User%2C%20Policy%2C%20Groups%20and%20Roles%29.md) · [AWS Networking - Part2 →](./AWS%20Networking%20-%20Part2.md)

*OneNote page: Wednesday, 10 June 2026 4:27 PM*

## At a glance

- Networks connect hosts through switches and routers.
- IPv4, CIDR, private ranges and NAT explain the EC2 network defaults.
- The later diagrams trace packets within a subnet and out to the internet.

## On this page

- [EC2 network defaults](#ec2-network-defaults)
- [Networking fundamentals](#networking-fundamentals)
- [IP addresses](#ip-addresses)
- [Private IP ranges](#private-ip-ranges)
- [NAT and packet translation](#nat-and-packet-translation)
- [Connecting the concepts](#connecting-the-concepts)
- [CIDR and address allocation](#cidr-and-address-allocation)
- [Communication within a network](#communication-within-a-network)
- [Routers and other networks](#routers-and-other-networks)
- [Private hosts and the internet](#private-hosts-and-the-internet)

## Complete page transcription

### EC2 network defaults

When we try to Launch EC2 Instance, we will notice that most of the NETWORK details are set to default.

<details><summary>Screenshot 1: accessible text</summary>

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

<details><summary>Screenshot 2: accessible text</summary>

<pre>
Network settings Info 
Edit 
Network 
Info 
vpc-06a42df24a27c3d21 
Subnet 
Info 
No preference (Default subnet in any availability zone) 
Auto-assign public IP| 
Info 
Enable 
Firewall (security groups) 
Info 
A security group is a set of firewall rules that control the traffic for your instance. Add rules to allow specific traffic to reach your instance. 
O 
Create security group 
Select existing security group 
We&#x27;ll create a new security group called &#x27;launch-wizard-1&#x27; with the following rules: 
Allow SSH traffic from 
Helps you connect to your instance 
Anywhere 
0.0.0.0/0 
Allow HTTPS traffic from the internet 
To set up an endpoint, for example when creating a web server 
Allow HTTP traffic from the internet 
To set up an endpoint, for example when creating a web server 
A Rules with source of 0.0.0.0/0 allow all IP addresses to access your instance. We recommend setting 
X 
security group rules to allow access from known IP addresses only.
</pre>
</details>

Default VPC is selected

Default Subnet is selected

Ask to create security group

Can you read this format of IP address

My recommendation would be not to proceed further without understanding NETWORKING concepts first.

Because, by using default Network configurations and by only setting "security group" we can launch EC2 instances but it can introduce security risks and its not production-ready.

### Networking fundamentals

Networking Fundamentals before we start AWS Network

What is Network?

Group of hosts (computers) that can communicate with each other is called a network.

2 different networks talk each other using routers.

Router1

switch

switch

Router2

Router3

Network 1

Network 2

Lets say, from Network 1 packet needs to reach to Network 2:

No single router knows how to reach from Network 1 to Network 2. Each Router hold the information about next hop and this detail is stored in "Routing Table"

destination

Next hop

192.168.1.0

Router 2

Router1

switch

switch

destination

Next hop

192.168.1.0

Router 3

Router2

destination

Next hop

192.168.1.0

-

Router3

Network 1

Network 2

its directly connected, so

it will deliver

### IP addresses

IP Address:

IP Address is the address of a device on a network, used for communication between devices.

We see IP address everyday, below is an example of IPv4

192.168.1.1

IPv4 is a 32 bit number.

192        .      168        .        1        .          1

8 bits

8 bits

8 bits

8 bits

11000000  .  10101000  .  00000001  .  00000001

Each can be a number between 0 - 255 (2^8 = 256 possible values 0,1,2…255).

So, how many IPv4 address we can have:    
                                                           2^32

= 4,294,967,296

= approx. 4.3 billion addresses

There are tens of billions of devices which are connected to internet and need IP address.

So, yes we ran out of IPv4 address years ago and that’s why IPv6 exists.

which uses 128 bits

= 2^128

= 3.4 * 10^26 trillion addresses

So, no exhaustion problem at all. And good thing is AWS support IPv6 too.

But still, you will notice that most of the address (~99%) which we see day to day is IPv4 only.

So question is, if IPv4 addresses are exhausted years ago, then how internet still works with IPv4 addresses?

### Private IP ranges

Answer: because of Private IPs

Below are the 3 private range:

CIDR   
(Classless   
Inter-domain   
Routing)

Description

Lowest address

Highest address

Total Available IPs

10.0.0.0/8

/8 means, left most 8 bits are fixed,   
rest can vary.

00001010.00000000.00000000.0000000

(fixed)

10.0.0.0

00001010.00000000.00000000.00000000

10.255.255.255

00001010.11111111.11111111.11111111

2^(32-8) = 2^24

~ 16.7 Million IPs

172.16.0.0/12

/12 means, left most 12 bits are fixed,   
rest can vary.

10101100.00010000.00000000.0000000

(fixed)

172.16.0.0

10101100.00010000.00000000.0000000

172.31.255.255

10101100.00011111.11111111.11111111

2^(32-12) = 2^20

~ 1 Million IPs

192.168.0.0/16

/16 means, left most 16 bits are fixed,   
rest can vary.

11000000.10101000.00000000.0000000

(fixed)

192.168.0.0

11000000.10101000.00000000.0000000

192.168.255.255

11000000.10101000.11111111.11111111

2^(32-16) = 2^16

~ 65,536 IPs

What makes them Private?

No public router on the internet will forward packets to these addresses. The internet pretends these addresses does not exist.

Anyone can use them simultaneously without conflict.

Home A

Home B

Uses

192.168.1.0

Uses

192.168.1.0

Both hosts uses same private IP address without any conflict.

Now next obvious question comes to mind is:  How private host reach the internet?

### NAT and packet translation

through, NAT (Network Address Translation)

Consider NAT is a software which runs on router that modifies the IP address while they are in transit.

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

My home Network

Routing Table

Destination

Next Hop

8.8.8.8:443

Internet Gateway IP address

Packet:

Destination: 10.0.1.10

Destination port: 54321

Source: 8.8.8.8:443 (google)

Source port: 443

Do lookup its

Translation table and update destination IP and port

Translation (mapping) Table

Host1

Specialized Router

With NAT

52.10.20.30:40001

10.0.1.10:54321

Private IP address:

10.0.1.10

Packet:

Destination: 52.10.20.30

Destination port: 40001

Source : 8.8.8.8:443 (google)

Source port: 443

Internet Gateway   
(Specialized Router)

Internet

### Connecting the concepts

At this point of time, many time confusion comes to connect the dots between:

Network

Private IPv4

Switch

Router

Routing table

NAT (specialized router)

Internet gateway (specialized router)

Lets try to see whole flow connecting everything together:

### CIDR and address allocation

Allocate IP addresses for our Network

Requirement is max 500 devices (hosts) can be within this network.

CIDR I am choosing is: 192.168.0.0/16

11000000 . 10101000 . 00000000 . 0000000

(fixed)

(available for hosts)

Total no of IP address available is:   
   
=  2^(32-16) = 2^16

~ 65,536 IPs

But our requirement is only 500 devices, so we can further reduce the count:

192.168.0.0/23

11000000 . 10101000 . 0000000 0 . 0000000

(fixed)

(available for hosts)

Total no of IP address available is:   
   
=  2^(32-23) = 2^9

~ 512 IPs

The fixed part 11000000 . 10101000 . 00000000.00000000 identifies our Network. Means all devices within this network starts with this only.

The second part 11000000 . 10101000 . 00000000.00000000 identifies the particular host within this network.

1st address: 192.168.0.0 is reserved - known as NETWORK address

Last address: 192.168.1.255 is reserved - known as BROADCAST address

Rest all can be used by hosts.

Network:

CIDR: 192.168.0.0/23 (192.168.0.0 - 192.168.1.255)

### Communication within a network

Hosts within the Network

Each host which need IP address is allocated from the CIDR range.

Network:

CIDR: 192.168.0.0/23

192.168.0.1

192.168.0.2

192.168.0.3

Hosts within Network are connected via Switch

Network:

CIDR: 192.168.0.0/23

192.168.0.1

Switch

192.168.0.2

192.168.0.3

Remember "Switch" always work with MAC address not IP Address.

2.

1.

Each Hosts also maintains a local Routing Table,

Which holds the next hop information

I want to send packet to:

Destination IP: 192.168.0.3

Whether its within my network or

Outside network?

We can easily check in local too:   
"route -n get default"

Sender Host

Destination

Next Hop

192.168.0.0/23

LOCAL

5.

Packet:

Source IP: 192.168.0.1

Destination IP: 192.168.0.3

Source MAC: 00:11:22:AA:BB:CC

Destination MAC: AA:BB:CC:11:11:11

These tables are dynamic and filled through DHCP (Dynamic Host Configuration Protocol)

3.

Now sender host need MAC (physical hardware) address information of the given destination IP address.

So it check ARP (Address Resolution Protocol) Table

AA:BB:CC:11:11:11

4.

We can easily check in local too:   
"arp -a"

IP Address

MAC Address

192.168.0.3

AA:BB:CC:11:11:11

Switch

Destination Host

Directly pass the packet to the destination

Using MAC address

### Routers and other networks

Routers

Network:

CIDR: 192.168.0.0/23

Routers Mesh

192.168.0.4

Router A

Routing Table

Destination

Next hop

192.168.0.1

Router B

Router D

192.168.0.5

Routing Table

Switch

192.168.0.2

Destination

Next hop

192.168.0.7

Routing Table

Router C

Destination

Next hop

192.168.0.6

192.168.0.3

Routing Table

Destination

Next hop

2.

1.

Each Hosts also maintains a local Routing Table,

Which holds the next hop information

I want to send packet to:

Destination IP: 8.8.8.8

Whether its within my network or

Outside network?

Sender Host

Destination

Next Hop

192.168.0.0/23

LOCAL

0.0.0.0/0   
(any other address)

192.168.0.7   
(Default router)

5.

Packet:

Source IP: 192.168.0.1

Destination IP: 8.8.8.8

Source MAC: 00:11:22:AA:BB:CC

Destination MAC: FF:CC:BB:22:77:11

3.

Now sender host need MAC (physical hardware) address information of the given destination IP address.

So it check ARP (Address Resolution Protocol) Table

FF:CC:BB:22:77:11

4.

IP Address

MAC Address

192.168.0.3

AA:BB:CC:11:11:11

192.168.0.7

FF:CC:BB:22:77:11

Switch

Router

Directly pass the packet to the destination

Using MAC address

One doubt might come is, why we need Router Mesh:

Couple of reasons:

Fault Tolerance:

Because of Mesh, even if 1 router failed, packet will still continuously flow with other path.

Router A

Router A

Router A

Router B

Router D

Router B

Router B

Router D

Router D

Router C

Router C

Router C

Load Balancing:

If Router-D need to send huge influx of data packet to Router-B, it doesn't have to choke single path.

It can send say 50% via Router-A and 50% via Router-B

Router A

50% data packets

Router B

Router D

50% data packets

Router C

Long distance handling:

Say destination is present in different network which is very far.

And everything device has a limit, so possible that 1 router is not sufficient and we need multiple routers to forward the packet before it reach the destination.

### Private hosts and the internet

NAT

Removes sender private IP (192.168.0.1) and replaces it with its own Public IP.

Sends it out through the Internet Gateway to the web.

When the response comes back, the NAT remembers who originally asked for it and forwards it back down the chain.

Both are possible scenarios:

Routers Mesh in mid

Network:

CIDR: 192.168.0.0/23

Routers Mesh

192.168.0.4

Router A

Routing Table

Destination

Next hop

192.168.0.1

NAT

Router B

Router D

192.168.0.5

Switch

192.168.0.2

192.168.0.7

192.168.0.8

Private IP:

Public IP:

Routing Table

203.0.113.5

Destination

Next hop

Routing Table

Router C

Destination

Next hop

192.168.0.6

192.168.0.3

Routing Table

Destination

Next hop

No Routers Mesh in Mid:

Small setup, less traffic and no long physical distance,  in that scenarios, Switch can directly pass the packet to NAT router.

Network:

CIDR: 192.168.0.0/23

192.168.0.1

NAT

Switch

192.168.0.2

192.168.0.8

Private IP:

Public IP:

203.0.113.5

192.168.0.3

2.

1.

Each Hosts also maintains a local Routing Table,

Which holds the next hop information

I want to send packet to:

Destination IP: 8.8.8.8

Whether its within my network or

Outside network?

Sender Host

Destination

Next Hop

192.168.0.0/23

LOCAL

0.0.0.0/0   
(any other address)

192.168.0.8   
(NAT router)

5.

Packet:

Source IP: 192.168.0.1

Destination IP: 8.8.8.8

Source MAC: 00:11:22:AA:BB:CC

Destination MAC: FF:CC:BB:22:77:11

3.

Now sender host need MAC (physical hardware) address information of the given destination IP address.

So it check ARP (Address Resolution Protocol) Table

FF:CC:BB:22:77:11

4.

IP Address

MAC Address

192.168.0.3

AA:BB:CC:11:11:11

192.168.0.8

FF:CC:BB:22:77:11

Switch

NAT Router

Directly pass the packet to the destination

Using MAC address

Internet Gateway

Network:

CIDR: 192.168.0.0/23

192.168.0.1

Internet

Gateway

NAT

Internet

Switch

192.168.0.2

192.168.0.8

Private IP:

Public IP:

203.0.113.5

192.168.0.3

OR

Network:

CIDR: 192.168.0.0/23

Routers Mesh

192.168.0.4

Router A

Routing Table

Destination

Next hop

192.168.0.1

Internet

Gateway

NAT

Router B

Router D

Internet

192.168.0.5

Switch

192.168.0.2

192.168.0.7

192.168.0.8

Private IP:

Public IP:

Routing Table

203.0.113.5

Destination

Next hop

Routing Table

Router C

Destination

Next hop

192.168.0.6

Routing Table

192.168.0.3

Destination

Next hop

[AWS index](./index.md) · [← IAM (User, Policy, Groups and Roles)](./IAM%20%28User%2C%20Policy%2C%20Groups%20and%20Roles%29.md) · [AWS Networking - Part2 →](./AWS%20Networking%20-%20Part2.md)

*Screenshots are represented by their OneNote accessible text. The original bitmap images are not embedded; image text may contain recognition errors.*
