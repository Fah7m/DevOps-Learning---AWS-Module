Objective
---
Deploy an Application Load Balancer (ALB) with two EC2 instances behind it. Ensure security group (SG) configurations allow only ALB access to EC2 instances while preventing direct access.

Architecture Logic
---
Below is a image of the architecture logic of how it should look for both the EC2 instances in different AZ's to route traffic using the Application Load Balancer.

<img width="579" height="485" alt="image" src="https://github.com/user-attachments/assets/10a62adb-fe7d-4b5c-bcda-3d57cfbbc150" />


The setup
---

I first created the VPC that has 2 public subnets in 2 Availability Zones. Within the VPC, I also added my route table and Internet Gateway so the instances within the Public Subnets can get access to the internet.

<img width="1338" height="369" alt="image" src="https://github.com/user-attachments/assets/909c4e12-5579-4b62-ab73-242df1cac8dc" />

From there I then Created the two instances and added them to their two separate subnets in different AZ's. I attached the neccessary Security Group rules to each of these instances to only allow HTTP and SSH. 

I also added a user data script in each EC2 instance so that on boot up, the yum package could get updates and each instance would display instance-specific information on a web page.

<img width="1641" height="430" alt="image" src="https://github.com/user-attachments/assets/bffb48a7-41c1-44d6-b34c-600ebc64548d" />

<img width="761" height="166" alt="image" src="https://github.com/user-attachments/assets/72e11aa0-1e24-42d5-938a-b9b5028bf14a" />


I then went on to create a Target Group for both the EC2 instances so that the Application Load Balancer could then send traffic to them in a load-balanced way.

Also, within the Target Group, I added health checks so that the TG can decide whether an instance is healthy and receives traffic.

<img width="1594" height="285" alt="image" src="https://github.com/user-attachments/assets/390cbfdb-263f-462d-a05f-0adf72a4ba4f" />
**The EC2 instances registered to the target group with health status shown**


Finally, I created the Application Load Balancer which points to the target group to direct traffic to the instances. 

<img width="1617" height="367" alt="image" src="https://github.com/user-attachments/assets/97f6a5f5-09d1-4c8a-bc6d-981bf849ac37" />


**Could Add**

I could've added a Auto Scaling Groupand attach it to my Application Load Balancer so that it can spin up EC2 instances and remove EC2 instances depending on traffic or Instance health checks.

Also, a HTTPS listener could be added to the Load Balancer and a SSL cert could be attached to the ALB listener so that traffic is validated and communication is encrypted..

Result of ALB
---
By using dig against the DNS for the load balancer we created, I can see that there are two IP addresses under the Answer Section for dig meaning we have the two Public EC2 instance IP's showing and these are the instances being used to load balance against in the ALB

<img width="795" height="410" alt="image" src="https://github.com/user-attachments/assets/49103f20-97a2-419d-afe9-37d6d3e73207" />
