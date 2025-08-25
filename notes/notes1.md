IAM
---

**IAM Credentials Report** is a report that lists all your accoutns users and staus of their various credentials.

**IAM Access Advisor** shows the service persmissions granted toa  user and when those services were last accessed - good for revising policies


EC2
---
**EC2** = the VM that you're renting from AWS

**EBS** = The storing of data on virtual drives for your EC2

**ELB** = Distributing load across machines (ec2)

**ASG** = Allows to scale services using an Auto Scaling Groups (ASG)

**EC2 User data** is the process of launching commands when the machine starts. These commands can entail things like: - This is accessed when creating the EC2 instance in the advanced section
-Installing updates
-Installing Software
-Downloading common files from the internet
-Anything you can think of


**EC2 Instance Versions**

On demand - Used for shoterm or unpredictable workloads where you want to predictable pricing and no longer term commitments. - Paid by the second and a use case would be a testing a new app or running temp project

Reserved Instances - These can be set for 1 or 3 years and the longer you get it the cheaper it becomes. These are used when if you will be running a steady workload for a longer period, the reserved instances will then save you money depending on which you go for - 1 or 3 and 3 will be even cheaper

Convertible Reserved Instances- The 2nd type of reserved instance which allows you to change the instance type over time but you still get the benefit of long-term contracts.

Savings plans - These also come in 1 and 3 years but they provide a discount for committing to a certain amount of usage over 1 or 3 years but in this case you have more flexibility on how to use it across difference instance types and regions - if your looking for long term saving without the restriction of sticking to one instance type, this is where you use this.

Spot instances - Allow you to bid for unused EC2 capacity at a much lower price. THis is cost effective but AWS can take back the instance when they need capacity so its less reliable. - Use case could be for workloads that you don't mind being interrupted such as Batch processing or data analysis

Dedicated hosts - Used for more control over your server. This allows you to book an entire physical server and is neccessary for compliance or software licensing that requires a certain machine.

Dedicated Instances - These ensure no other AWS customer will share the hardware with you and provide more isolation. - useful when you need isolated hardware for security or compiance reasons

Capacity Reservations - USed if you need to guarantee that capacity is available to you in a certain AZ for a certain duration, that can be reserved ahead of time with this instance type - Use case would be for critical workloads that need to run in a certain AZ especially during High demand periods.


Security Groups 
---
SG's control how traffic flows into and out of EC2 instances - THey are like virtual firewalls as they devcide whats allowed in. 

They allow all outbound traffic by default but can be removed in the console when setting up the EC2 instance

SG's are locked down to a certain region or VPC combination - this means you can't use a Security Group from one region or VPC in another as it stays locked to its original location. e.g. if its created in eu-west-1, you can't use that same SG in eu-west-2 for another EC2 instance hosted in that region.

<img width="1805" height="920" alt="image" src="https://github.com/user-attachments/assets/ecb998cb-95cb-4f18-81ab-0e951b48808e" />

