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

**Network Load Balancer** - This LB operates on Layer 4 and is build for TCP and HTTP traffic. It is designed for high-performance scenarios where you need very low latency which makes it ideal for situations where you may 
need to handle millions of requests per second such as real-time gaming or high frequency trading systems.

**Gateway Load Balancer** - GLB operates in Layer 3 network layer and works with the IP protocol. This is designed to deploy, scale and manage third-party network applications like firewalls and intrusion detection systems and
even traffic analyzers in your VPCs - suitable for complex network setups

