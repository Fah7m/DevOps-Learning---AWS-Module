Objective
---
Create a custom VPC with both public and private subnets, configure internet access, and deploy EC2 instances with secure segmentation.

First I created a VPC and two subnets (Private and Public).

Public
---
Within the Public Subnet I have setup a EC2 instance that acts as a jumpbox or Bastion Host - Basically a bridge to connect on to the Private EC2 instance

I have also configured a Security Group for the EC2 instance to make sure the correct traffic is routed.

**Inbound rules in the SG**
<img width="1635" height="289" alt="image" src="https://github.com/user-attachments/assets/c0b9118d-ff3b-4fb0-9f0b-5284883f739b" />

**Outbound rules in the SG**
<img width="1633" height="257" alt="image" src="https://github.com/user-attachments/assets/c62fee10-6cdd-4202-8d2d-d2c1b91523f9" />


Private
---
Within the Private Subnet I have setup a Private EC2 instance that only has access to the internet via Egress Internet Gateway utilising IPv6.

I have also configured the Private Security Groups to allow specific Inbound and Outbound traffic.

The only Inbound I have allowed for my Private EC2 instance is for the Public EC2 instance to be able to only connect on to it.

**Inbound Rules**
<img width="1627" height="232" alt="image" src="https://github.com/user-attachments/assets/51f97a2d-8b66-43a4-81a5-860c137389a7" />
This rule has a custom IP set which is the IP of the public subnet - This means that the EC2 instance(s) within the Public subnet can ssh on to the Priv EC2 so no direct access

**Outbound Rules**
<img width="1639" height="214" alt="image" src="https://github.com/user-attachments/assets/f8aa8081-22d9-44ed-89b4-4bb9c04cc6ec" />


Connecting via Bastion
---
To connect to my Private EC2 instance I first copied my privatekey that it shared to connect on to both Public and Private Instances - I copied this and created a key file withint my Public EC2 instance (Bastion host) by doing the following commands

```
[ec2-user@ip-15-0-1-240 ~]$ touch aws_ec2_instance_key
[ec2-user@ip-15-0-1-240 ~]$ vi aws_ec2_instance_key
[ec2-user@ip-15-0-1-240 ~]$ chmod 400 aws_ec2_instance_key
```

Then I ssh'd straight on to my private instance by using that private key I copied over from my local computer and copied the Private IP of my Private Instance via the console to login
```
[ec2-user@ip-15-0-1-240 ~]$ ssh -i "aws_ec2_instance_key" ec2-user@15.0.142.22
```

To even first connect on to my Public EC2 (Bastion Host) I copied over the privatekey from my local on to my home directory in Ubuntu WSL and did the following.
```
fahim@DESKTOP-CNID5Q9:~$ cp /mnt/c/Users/Fahim/Downloads/pubec2.pem ~
fahim@DESKTOP-CNID5Q9:~$ chmod 400 "pubec2.pem"
fahim@DESKTOP-CNID5Q9:~$ ssh -i "pubec2.pem" ec2-user@ec2-35-177-32-249.eu-west-2.compute.amazonaws.com
```


Cloudwatch Dashboard
---
I also configured a CloudWwatch dashboard for both EC2 instances to monitor usage

<img width="1790" height="724" alt="image" src="https://github.com/user-attachments/assets/5018d5c3-c097-46ca-979a-87d367915c7b" />

***This dashboard could be setup much better so it is easily readable*** - To review


<img width="1364" height="320" alt="image" src="https://github.com/user-attachments/assets/d1b6866a-09ee-401d-b259-50384606ba59" />
