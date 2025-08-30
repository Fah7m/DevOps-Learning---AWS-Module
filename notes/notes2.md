Load Balancing and Scalability
---

Scalability means that an application or system can handle greater loads by adapting to the circumstance and the two types of this are:
**Vertical Scalability** - Scaling Up increasing the power of a single machine by adding better CPU, RAM and storage. Hardware has a limit so you can keep Scaling up on one machine as it can also become more expensive but
its simple to implement. 

Upgrading from t2.micro to t2.large is an example of this and this is common in non distributed systems like databases. - RDS and ElastiCache are services that can scale vertical

**Horizontal Scalability** - Scaling Out by adding more machines to handle the load instead of making one machine stronger. This is more complex as you will need load balancing and data replication but there is
no limit to how far you can scale. If one server fails, others keep running so its fault tolerance andcost effective long term.

Similar to a call centre meaning, when a lot of calls come in you get more people on phones and when it's quiet you can have one person on the phones. This logic can be applied the same for EC2 instances with load balancers.


**High Availability** goes hand in hand with horizontal scaling as to be highly availabe, you need multiple instances in different availability zones. The purple of HA is to survive data center loss so if one AZ goes down, 
your application should keep running in another AZ with no downtime as traffic is routed to another instance in another AZ.

Load balancer
---

**Load Balancer Security groups**  come into play when working with EC2 instances with load balancers. Two tier load balancers are a good way of validating traffic and ensuring only legitimate traffic goes to the £C2 instance. 

<img width="1606" height="495" alt="image" src="https://github.com/user-attachments/assets/02d0d517-bb48-4da9-b10c-99a545c5b223" />

In that screenshot we can see we have the first LB Security Group that accepts both HTTP and HTTPS traffic from any IP but HTTPS pulls through encrypted traffic using SSL/TLS. 

With the 2nd Application LB Security Group, we restrict access so that only the load balancer can communicate directly with the EC2 instance as you can see from the source. This is the only traffic they accept is from the 
load balancer which acts as a protective barrier.

**Load Balancer benefits**
<img width="562" height="299" alt="image" src="https://github.com/user-attachments/assets/d1649672-fd11-412b-9144-b38023ea06d3" />


**Health Checks** are important because they enable the load balancer to know if the EC2 instance is healthy by sending a request to a certain port and route e.g. a load balancer will send a request to port 200 and /health
route to check if the EC2 is functioning and ready to recieve traffic. 

**AWS Load Balancer Types:**

**Classic load balancer** - This is the standard load balancer thats very old and supports basic HTTP, HTTPS, TCP and SSL traffic

**Application Load Balancer** - This LB is designed for HTTP, HTTPS, and WebSocket traffic which makes it the widely used LB for modern Web apps. This LB operates in the Layer 7 which makes it smart as it can route decisions based on the content of the request e.g. URL's, Headers, query parameters and cookies. 

ALB extra features:
-Route based on URL

-Route to different target groups

-Route via query strings and header-based routing which allows you to have more control over what traffic to route to where e.g. mobile devices etc.

-Port mapping which allows to dynamically redirect traffic to containers running on different ports

-ALB can do path based routing in cases wheere you want users, posts and comments to be routed to different places eg. if URL has /api then traffic can be sent to a target group for backend API servers (ECS tasks) or
if URL has /images then the target group users will be for a S3 static hosting via cloudfront

-A single load balancer can manage multiple applications thanks to the routing features


**Network Load Balancer** - This LB operates on Layer 4 and is built for TCP and HTTP traffic. It is designed for high-performance scenarios where you need very low latency which makes it ideal for situations where you may 
need to handle millions of requests per second such as real-time gaming or high frequency trading systems.


**Gateway Load Balancer** - GLB operates in Layer 3 network layer and works with the IP protocol. This is designed to deploy, scale and manage third-party network applications like firewalls and intrusion detection systems and even traffic analyzers in your VPCs - suitable for complex network setups


Target groups
---
Target groups is a grouping of endpoints that an AWS load balancer sends traffic to. The target in which traffic can be routed to can be the following:
-EC2 Instance (ID of instance or IP)
-Containers (ECS Tasks)
-Lambda functions (
-Private IP address (For on prem or hybrid cloud setups)

ALB's can route traffic to multiple target groups which means one single ALB can handle several services or applications - one target group can handle user requests and another for search services.

Health checks are performed at the target group level and this is where ALB will check the statusof resources in a target groups such as EC2, ECS or Lambda functions.


X-Forwarded headers
---
A Load Balancer can sometimes ignore the Client's IP address if they are setup for the IP to be hidden which will give the client the LB's IP address and not their actual IP address.

With X-Forwarded headers, you can obtain the clients real IP and with these you can force more rules such as trusting headers only if the source is your own LB or reject requests missing expected headers

<img width="1084" height="461" alt="image" src="https://github.com/user-attachments/assets/45912324-923a-4199-9d30-6ce15213bc9f" />


Sticky Sessions
---
This is essentially the process of allowing a user to use the same instance instead of switching between others with a LB. This comes to play when a user has ongoing user data (e.g. a basket full of items) in one instance
and switching to another isntance with a load balancer can cause them to load that data. 

Sticky Sessions can be implemented in Classic LBs, Network LBs and Application LBs but this can cause traffic imbalance as having too many sticky sessions on one instance can overload it. This will have an affect on performance as traffic will not be spread evenly when a sticky session is activated. 

Sticky Sessions use cookies from users in order to know which instance to keep that user on so they don't lose their data. With the use of cookie-based control, you can fine tune how long a client stays stuck on the instance by adjusting the coookie expiration settings. 


SSL Certs in LB
---
Load balancers are the middleman that makes sure traffic between users and instances is encrypted and this is done by using a X.509 certificate which is basically a SSL and TLS cert. AWS makes managing certs easy with ACM
(Aws Certificate Manager). ACM is basically a one stop shop or creating, renewing and managing certificates on AWS.

Setting up a HTTPS listener on a LB is how we ensure traffic is encrypted. A default SSL/TLS Cert needs to be specified which will be used if no other rules are matched.

A SNI, server name indication is what basically lets your client to connect to a certain hostname. The load balancer will server up the right certification for that hostname so they can connect to it.

The flow:
-User connects via HTTPS which is encrypted over the internet
-The load balancer handles that connection
-Once inside the VPC, the load balancer forwards the request with HTTP - Encryption isn't needed for private traffic

**SNI** solves the problem of hosting multiple websites on the same server but with different SSL certificates by allowing multiple Certs to be loaded on to the same web server. When the client makes a first connection to the web server, as part of the SSL handshake it sends the hostname of the website and if it can't find the certificate for that website, then it uses the default certificate we put in place. 

SNI only works for ALB and NLB (New gen) and also Cloudfront.


Auto Scaling Group
---
ASG's adjust the number of running instances based on the load so if the traffic increases, then more instances are brought up by the ASG and the same is done if traffic decreases. 

A minimum number of instances running can be set for ASG's and same for a maximum number of instances so that you don't spin up too many. Also, if an instance becomes unhealthy or gets terminated, the Auto Scaling Group will recreate it. 

ASG launch templates make sure that when new instances are spun up, it has the correct configuration such as:
-AMI + instance type which will ensure the launched instance has the same setup
-EC2 User Data which can include scripts that run when instance starts such startup tasks that install packages or config of software
-EBS Volumes for persistent storage for the instances
-Security Groups to control inb and outbound traffic for the instances
-SSH Key Pair to securely connect to the instance
-IAM Roles for EC2 instances so that the correct permissions are in place
-Network and Subnet info to specify which VPN and subnet the instances should be created
-Load Balancer info to handle traffic
-Finally the ASG attributes which defines how many isntances can be launched at min and maximum  as well as scaling policies which decides how the ASG should scale in and out based on traffic and performance

Scaling based on CloudWatch alarm is possible as the alarm monitor metrics such as CPU and custom metrics. With this information, we can create scale out policies based on the CloudWatch Alarm and slace in policies.

**Scaling Policies** can control how and when the ASG reacts to changes in traffic and there are three main Dynamic scaling policy types:

Target tracking scaling policy - Easiest to setup and it basically tells AWS to main a certain average load e.g. keep CPU usage at 40% and AWS will add or remove instances to meet that target. 

Simple/Step scaling - This policy is based on CloudWatch alarms and when a alarm is triggered like CPU usage going above 70%, then AWS will add instances based on that alarm.

Scheduled Scaling - This one is like planning ahead if you know what types traffic peaks or dips e.g. a sale is on so you set it to scale at Monday 10am to prepare for traffic spikes. 
