# DevOps-Learning---AWS-Module



Key notes
---

Regions = a Geographical locations e.g. eu-west-1 or west-2 - within each region is multiple **AZ** or otherwise known as Availability Zones

AZ = One or more data centers inside a region which is designed for redundancy and fault tolerance - usually 3 per region or some cases 2

Edge Location = Not inside an AZ - they are separate smaller data centers all over the world used for low latency services like CloudFront (CDN) Route 53 (DNS) Shield and WAF


<img width="1474" height="578" alt="image" src="https://github.com/user-attachments/assets/32d5e060-b16e-4113-a9d2-033b0a0aa833" />



Network Architecture
---
<img width="1891" height="1040" alt="image" src="https://github.com/user-attachments/assets/5cae6089-f858-4806-aa45-a98801e473f5" />

**My Answer**
So first we have the two Subnets, one private and one public. The private subnet has two ec2 instances and a route table that points to the NAT gateway in the public subnet. This allows for the private EC2s to connect to the internet without revealing it to the internet or revealing the private IP. 

Next we have security Groups which basically control what comes in and out for the ec2 instances (both private and Public). Next we have the outer shell which is the availability zone, meaning this is all hosted in one AZ such as us-east-1a. 

Next we have a arrow going from the public ec2 instance to the router. This lets that public ec2 instance connect to the internet via the route table and the internet gateway. 

We then have two VPNs setup which allow only certain IPs to connect to the instances within the VPC. Also, within the region we have a couple more AWS services such as CLoudWatch and a S3 bucket. Cloudwatch will monitor anything with the instances and the S3 bucket will act as a database. 

We also have VPC peering connection which allows VPC's to communicate with each other?

**ChatGpt response to my answer:**

1. Subnets and Routing

✅ Correct: There’s a public subnet (with a NAT Gateway and a public EC2) and a private subnet (with two private EC2s).

🔍 Clarification:

The private subnet’s route table points to the NAT Gateway in the public subnet.

This lets private instances access the internet (for updates, etc.) but not receive inbound traffic from the internet.

The public subnet’s route table points to the Internet Gateway to allow inbound/outbound internet access for resources with public IPs.

🛡️ 2. Security Controls

✅ Correct:

Security Groups act as stateful firewalls for instances.

They control inbound/outbound traffic at the ENI (Elastic Network Interface) level.

🔍 Addition:

NACLs (Network ACLs) (shown at subnet boundaries) provide an additional stateless firewall at the subnet level.

They’re often used for broader allow/deny rules, while Security Groups are more fine-grained.

🌍 3. Availability Zone & Region

✅ Correct-ish:

The dashed blue box labeled “Availability Zone” means this diagram is showing one AZ within a Region (the green dashed box).

A real VPC spans multiple AZs for redundancy, but here they only drew one AZ for simplicity.

🌐 4. Public EC2 and Router

✅ Correct: The public EC2 instance connects to the Internet Gateway via the route table and router.

🔍 Clarification:

The “router” icon represents the VPC’s built-in logical router that connects all subnets, gateways, and peering links.

The route tables define how this router forwards traffic.

🔒 5. VPN and Direct Connect

🔍 Clarification:

There are two VPN icons:

VPN Gateway (AWS-managed) connecting your VPC to an on-premises network.

A customer VPN client connection for secure access from a specific IP or office network.

Direct Connect (DX) provides a dedicated physical line between your data center and AWS. It’s lower latency and higher bandwidth than VPN.

🏗️ 6. Other AWS Services

✅ CloudWatch: Correct — for monitoring logs, metrics, and alarms for EC2, NAT, VPC Flow Logs, etc.

🔍 Correction:

The S3 bucket is not a database; it’s an object storage service. You’d use it to store data, backups, logs, or static files, not relational queries.

🔍 DynamoDB: Seen on the right — this is AWS’s NoSQL database service, separate from S3.

🔗 7. VPC Peering & Transit Gateway

✅ Peering: Correct — VPC Peering connects two VPCs so they can route traffic privately without traversing the internet.

🔍 Transit Gateway:

Acts like a hub to connect multiple VPCs, VPNs, and Direct Connect links at scale, instead of doing complex peering meshes.

📜 8. Flow Logs & Endpoints

🔍 VPC Flow Logs:

Capture IP traffic metadata (source, destination, port, action) for troubleshooting or security auditing.

🔍 VPC Endpoints:

Let resources in your VPC connect privately to AWS services (like S3, DynamoDB) without using the internet or NAT Gateway.

🧠 Cleaned-Up Summary of Diagram

Here’s the flow in a nutshell:

A VPC spans the whole Region (green dashed box).

Inside, you have subnets (public & private) within one AZ (blue dashed box).

Public subnet:

NAT Gateway for outbound-only internet access from private subnet.

Public EC2 that’s accessible via Internet Gateway.

Private subnet:

EC2s with no direct internet access, route to NAT for updates.

Security layers: Security Groups (instance-level, stateful) + NACLs (subnet-level, stateless).

Connectivity options: VPN Gateway, Direct Connect, Transit Gateway, VPC Peering.

Monitoring & storage: CloudWatch, S3, DynamoDB.

Private AWS access: VPC Endpoints allow private S3/DynamoDB access.

Flow Logs record network traffic metadata for analysis.
