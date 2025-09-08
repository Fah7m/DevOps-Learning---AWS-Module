Route53 (DNS)
---

**Route53 Hosted Zones**

This is like a container as it holds all the DNS records for a certain domain and it's subdomains. This tells Route53 how to route the traffic.

Public hosted zones are used for routing traffic on the public internet e.g. if you have a domain like application1.mypublicdomain.com, you'd create a public hosted zone to define how traffic for that domain 
and it's subdomains are handled

Private Hosted Zones are used within VPC which makes it perfect if you want to manage internal traffic within one or more VPCs e.g. you might have a domain like application1.company.internal that only routes traffic within
a private network making sure that everything is hidden from the outside world.

DNS - The phonebook of the internet which translate the human friendly hostnames into machine IP addresses. Without DNS, we would type the IP of each website in order to access it instead of its actual name.

It follows a hierarchical naming structure meaning it starts at the top level domain, then it works its way down to domain name, then subdomain names. 

<img width="1717" height="1048" alt="image" src="https://github.com/user-attachments/assets/21be3b2d-392d-4dbf-9c81-8cedf177bbf3" />

**Record Types**

A - Maps a hostname to a IPv4 address e.g. example.com might map to 192.0.2.1

AAAA - Maps to a IPv6 address instead of IPv4

CNAME - Canonical name points one hostname to another hostname. It's like giving someone a nickname. You can only use this for subdomains and not the root domain like example.com, only works for www.example.com

NS - Nameserver record tell the internet which nameservers to use for the hosted zone e.g. your domain. It's like pointing to a traffic director of your domain and these are needed to control where traffic is routed for 
your domain.

**TTL**

TTL is how long a DNS records is cached before it's refreshed and there are two options, High TTL and Low TTL.

With high TTL, it's good for reducing costs however users won't be able to see changes as records are cached for a long time until TTL expires - so if it's set to 24hrs then it will take a long time

Low TTL could be set to like 6 seconds so changes to DNS records propagate faster. THis is good if you're moving servers or updating IP addresses frequently. The bad thing is that this is more costy 
as there is more traffic hitting route53.

**CNAME vs Alias**

AWS resources like LB or CloudFront expose an AWS hostname so instead of sending users to a long complicated AWS hostname, you will want something nice and custom.

CNAME as mentioned before points to a hostname like app.mydomain.com to another hostname like aka.anything.com however, CNAMEs are only used for non root domains like something.mydomain.com. 

Alias records in AWS are special as they point to a hostname like app.mydomain.com to a root domain. Alias records are also free of charge and AWS provides health checks for them. 

Takeway here is to Use Alias if your dealing with AWS resources as CNAME is more limited but useful for non-root domain needs.

Alias records can also be used for the root of your domain like amazon.com unlike CNAME. Alias records come in A or AAAA type for IPV4 and 6 depending on the resources that it supports. 

TTL can't be set for Alias records. 

**Alias Record Targets**

Load balancers - Common when you want to map a friendly domain name to your LB

CloudFront - a custom domain name to serve content via CLoudFront

API Gateway - If you're hosting APIs using API GW then you can map your custom domain with alias records

Elastic Beanstalk Env - If a app is running in Beanstalk then alias records can help easily connect your domain to it.

S3 - The same would go for S3 if your hosting websites on here then alias records would work great for pointing your domain to the bucket

VPC interface Endpoints - Private servers running in your VPC, alias records can be used to access them more easily

Global Accelerator - if you want to improve performance, you can use alias records with AWS's global accelerator.


**Routing policies** (REVIEW THIS SECTION)

Route53 doesn't route traffic it just helps DNS server to respond to quieries with the correct answer

Simple - This is straightforward and it just gives back the same IP address every time someone queries a domain

Weighted - If you have 2 servers and want 70% of traffic to go to server A and 30% to server B, weighted routing does that

Failover - If main server goes down, Route53 can detect that and route traffic to a backup server instead.

Latency based - Route53 will route users to the server that responds the fastest.

Geolocation - you can route traffic based on where the user is located so if a user is in Europe, then you can serve them different conect depending on location

Geoproximity - similar to geolocation but this allows you to route how much traffic goes to a resource based on a biased value. You can expand or shrink the size of a region that gets traffic using a bias value

IP based routing - Instead of using geolocation or latency, we focus on the users IP address to determine where to route traffic. You can provide the route through a list of CIDR blocks which is a range of IP addresses

Mutli value - When you have multiple routes and you want route53 to return more than one resource in response to a query, multi value is used. Route53 can check multiple IP addresses or resources from a single DNS query. 
