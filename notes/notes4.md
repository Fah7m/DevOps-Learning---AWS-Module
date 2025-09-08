Networking in AWS 
---

<img width="1891" height="1040" alt="image" src="https://github.com/user-attachments/assets/62720ebf-3431-44e1-8274-bb2aa58f0b1e" />


In AWS we gave VPC's, Subnets and Internet gateways. 

The VPC or otherwise known as Virtual Private Cloud, is a network that is setup on AWS so that you can launch AWS resources (EC2, databases, LB) and control all the networking with IP ranges, subnets, routing and security

A subnet is a smaller network (sub network) inside your VPC as when you create a VPC, you can pick a CIDR block e.g. 10,0,0/16 which gives you a big pool of IPs. The subnets can divide that pool into smaller ranges e.g. 10.0.1.0/24 for one subnet and 10.0.2.0/24 for another. By doing this it lets you organise and isolate resources within your VPC.

A internet gateway is how your network can communicate with the internet. It's attached to the VPC (one per VPC) and it allows traffic between your VPC and the public internet. 

**The process looks like:**

<img width="561" height="308" alt="image" src="https://github.com/user-attachments/assets/76331fbc-d51c-4c31-9476-93ea210da249" />


In a subnet, AWS automatically reserves 5 IP addresses in a range or a subnet CIDR e.g. /28 gives 16 IP addresses. 5 of them are automatically reserved for AWS for:

The first IP is reserved for the Network address

.1 is reserved by AWS for the VPC router

.2 is reserved for Amazon Provided DNS - The IP that is used for the DNS server in a VPC

.3 is reserved for AWS for future use cases 

Finally the last IP .255 is reserved which is generally known as the Network Broadcast Address but AWS doesn't support that so the IP is reserved


**Bastion hosts**

A Bastion Host is like a bridge to the private instances. It's an instance that sits in a public subnet meaning it can be accessed from the internet. From there you can SSH into a bastion and then ssh into private instances.

Bastion is created in a public subnet and once you connect to it, you can then access the private subnet where EC2 instances are located. The SG of bastion host need to be configured to allow inbound ssh from a restricted silo and the SG of the private EC2 instances needs to allow inbound ssh from the bastion host. 


**NAT Gateway**

A Network Address Translation (NAT) is a managed AWS service which allows instances in a private subnet to connect to the internet but blocks any incoming traffic from the internet. It lets the private instances reach the outside world for any updates or access external APIs without exposing them directly to the internet. 

Since this a managed service in AWS, you don't handle any heavy lifting as that is all done by AWS so this is zero maintanance. NAT is also AZ specific as they are tied to a certain AZ and they use an elastic IP. Also, a instance in the same subnet can use a NAT and it can only be used if it's in another subnet within the same VPC.

NAT also needs internet Gateways to connect to the outside world. A normal setup for it is a private subnet that goes from the internet gateway to the internet. Finally, NAT can also auto scale without so if traffic grows, the NAT will scale out to handle it. 

<img width="463" height="463" alt="image" src="https://github.com/user-attachments/assets/20740515-6ab3-49a0-ba37-b8afb7890d4d" />
**Architecture that shows only one NAT gateway in a AZ - this is risky and there should be two as if the NAT gateway AZ goes down, then this will affect all the resources in other AZ so there should be atleast two different AZs**


**NAT Gateway vs NAT Instance**
<img width="940" height="413" alt="image" src="https://github.com/user-attachments/assets/dbb7e19e-2861-4158-8b40-2761588080d1" />

Gateway is a set it and forget solution where as Instance needs a lot of management and overseeing.


**Network Access Control List**

NACLs is like a big filter at the subnet level that checks whether traffic should be allowed or denied and they do this based on set of rules that are defined. Each subnet can have only one NACL so assigning one NACL will apply it to everything in that subnet. NACLs dont automatically allow returned traffic as this has to be defined for both Inbound and Outbound rules.

NACLs work ina  priority list meaning that lower numbered rules take higher precedence. This also meaning that once a rule matches a traffic request its automatically applied and no further rules are evaluated e.g, if rule number 100 allows 10.0.0.10/32 and then rule 200 denies that, then it will only follow rule as that is higher in the priority list. 

The last rule in a NACL is called the catchall which is an asterisks which typically denies any traffic that doesn't match any of the rules. 


**SG vs NACL**

SG's operate at the instance level, they control traffic to and from EC2 isntances where as NACLs operate at the subnet level and they control traffic at the boundary of the subnet which affects all resources inside the subnet.

The way they work when it comes to each traffic type

**Inbound Traffic** 
1. IGW checks if a route to teh subnet exists
2. NACL is the first checkpoint at the subnet level - stateless: Both inbound and outbound rules must explicitly allow
3. SG is the second checkpoint at the Instance level - stateful: if inbound traffic is allowed, return traffic is automatically allowed
4. Traffic reaches EC2 isntance

**Outbound Traffic**
1. SG checks outbound rules
2. NACL checks outbound rules
3. IGW sends it to the internet if allowed in route table

Both the NACL and SG criterias need to be met in order to pass


**VPC Peering**

VPC peering is a way to private connect two VPCs using AWS's internal network. These VPCs behave as if they are part of the same network allows them to communicate with another but without using the internet. 

Both the VPC cannot have any overlapping CIDRs - no overlapping IPs or else AWS wont let them to create a peering connection. 

Route tables would need to be updated once a peering connection is create so that traffic can flow between the instances in both VPCs

VPC peering can be done between two separate AWS accounts or even across different regions. Also, referencing SGs across accounts as long as they are within the same region. 


**VPC Endpoint**

VPC endpoints allow you to connect to AWS services like S3, SNS etc using a private network so that data traffic is kept secure inside the AWS network. VPC endpoint is powereed by AWS PrivateLink. 

This is done so that your not exposing your VPC to the internet just to access AWS services and the setup is redunant and sacles horizontally, meaning it's super reliable and can handle increasin load without needing intervention. No need for NAT or IGW to access AWS services even in the private instances as the VPC endpoint does the job.

Issues that you may face with this is that somtimes you can run into hiccups and these can be addressed by checking the DNS resolution settings in the VPC. As well as checking the route tables to make sure traffic is being routes correctly to the VPC endpoint . 


**Endpoint types**

***Interface Endpoint***
Endpoints provision what is done behind the scenes and they create A Elastic Network Interface which is a private IP addressed that serves as the entry point for EC2 instances to connect to the AWS services. It is basically a way keeping everything on a private network instead of going out over the internet. 

A security group will have to be attached since it's using a Elastic Network Interface in order to manage traffic going from and to the private instaces. 

<img width="728" height="520" alt="image" src="https://github.com/user-attachments/assets/af00736e-2dbf-437f-a0fb-0b4b6076ca3f" />


***Gateway Endpoint***
Gateway Endpoints dont need to provision an ENI but instead it creates a gateway in your route table that sends traffic for certain AWS service directly to them - only S3 and DynamoDB is supported.

There are no SGs required for this type of endpoint which simplifies the setup and it is also free unline Interface Endpoint

<img width="720" height="520" alt="image" src="https://github.com/user-attachments/assets/88c6246e-c5e9-4470-8f7a-70a931cbd661" />



IPv6 in VPC
---
Since IPv4 cannot be disabled, something called dual stack mode can be run where IPv6 and IPv4 operates alongside each other. This means the resources use IPv4 and IPv6 at the same time. 

EC2 instances will have both IPv4 internal communications within the VPC and a public IPv6 which interrouteable. Meaning the instances can communicate with the internet using either IPv4 or IPv6 and this is made possible through the internet Gateway which supports both IP protocols.

***Troubleshooting IPv6***

Unable to launch an EC2 instance in your subnet - this is because there is no available IPv4 in your subnet so a new IPv4 CIDR will need to be created in the subnet.

Egress only INternet Gateways are specifically designed for IPv6 traffic - basically NAT gateways for ipv6 addressed. 

THey let the instances communicate out to the internet over IPv6 while blocking any incoming connections from the outside world. Instances in your VPC can use this Egress gateway to send traffic out to the internet over IPv6 but the internet is blocked from initiating connections back to your instances.

Its important to update route tables so that you can direct the outbound traffic from a private subnet through the Egress Only Gateway, if not then the instances won't be able to communicate with the outside world.


**Routing in IPv6**

IPV6 routing works within a VPC that operates in a dual stack mode, meaning that both IPv4 and IPv6 addresses are enabled. 

<img width="1264" height="872" alt="image" src="https://github.com/user-attachments/assets/ff1d1a4b-7f20-48c9-bee1-91b04cdcd3e7" />

In this diagram we see a VPC which is dual stacked and has two subnets, private and public. 

For the public subnet we cna see it has IPv4 and IPv6 enabled and the EC2 instance in the public subnet is using both a private IPv4 address and a Public Elastic IP. Also, it has  apublic IPv6 address which means the instance can communicate wit the internet using both IPv4 and IPv6. 

For the private subnet, we can see it does not have direct internet access. THe instances here have a private IPv4 and they route traffic through a NAT gateway locared in the public subnet. The NAT gateway is used for IPv4 outbound internet access and the INternet gateway is used for IPv6 outbound access.

For IPv6 traffic, instances can route their requests directly through the internet gateway for both public and private unlike IPv4 where a NAT gateway is required for private instances to access the internet. 

Then we have the route tables. The public subnet route table directs traffic for the VPC's internal IPv4 and IPv6 CIDR blocks locally, while all other IPv4 and v6 traffic to the internet is sent to the internet gateway.

In the private subnet, the route table sends IPv4 traffic to the NAT gateway and the IPv6 traffic goes to the internet gateway which allows the private instances to communicate with the internet for both protocols.


VPC Summary
---
CIDR - THis is essentially the IP range you define for your VPC for both IPv4 and v6. it sets the boundaries for your IP address allocation

VPC - Virtual Private Cloud is where you isolate a virtual network in AWS and where the ip ranges are defined for the resources to use. 

Subnets - Breaking down of a VPC into smaller chunks and each subnet is tied to an AZ - different CIDRs can be assigned to each subnet

IGW - This is how the VPC gets internet access for both IPv4 and IPv6 and it's created at the VPC level. - provides routing to and from the internet.

Route Tables - THese need to be configured to ensure traffic flows properly. They can be used to add routes from your subnets to internet gateways, VPC peering and VPC endpoints. 

Bastion Host - This is a secure bridge which is a private EC2 instance that allows you to access EC2 instances in a private instance. 

NAT Instance- Older way of giving internet access to Ec2 instances in a private subnet however there is a lot of configuration that comes into play with these.

NAT Gateway - AWS managed version of NAT instance. It's scalable and it provides internet access to priv EC2 instances like disabling the source destination.

NACL - Network Access Control List operate at the subnet level and are stateless which means each request is evaluated independently. They control outbound and inbound traffic flows.

SG - Security Groups are stateful firewalls that operate at the EC2 instance level which means once you allow traffic in or out, the response traffic is automatically allowed without needing another rule.

VPC peering - A way to connect two VPC using AWS's internal networking however it's non transitive which means VPC A and B are peered and B and C are peered however A and C can't communicate without a separate peering connection. (Transit gateways for that)

VPC Endpoints - THese allow for private access to AWS services such as S3 DynamoDB without using public internet. 

VPC endpoint services (PrivateLink) - This is a service for connnecting one VPC to another like from a Service provider VPC to customer VPC. These do not need VPC peering or NAT gateway or even public network connection and it only needs to be used with Network Load Balancers or ENI.

Egress Only IGW - Like a NAT gateway but for IPv6 targets only

Transite Gateway - Unlike VPC peering, transite GW providate transitive connectivity across VPC, VPNs and direct connection. It's like a hub that allows multiple networks to talk to each other without needing direct peering relationships. 
