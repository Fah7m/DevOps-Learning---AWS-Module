# DevOps-Learning---AWS-Module



Key notes
---

Regions = a Geographical locations e.g. eu-west-1 or west-2 - within each region is multiple **AZ** or otherwise known as Availability Zones

AZ = One or more data centers inside a region which is designed for redundancy and fault tolerance - usually 3 per region or some cases 2

Edge Location = Not inside an AZ - they are separate smaller data centers all over the world used for low latency services like CloudFront (CDN) Route 53 (DNS) Shield and WAF


**IAM roles** = Identities in AWS that define set of permissions through policies - Flow is like the below:

Create IAM role
Attach policy e.g. AmazonS3ReadOnlyAccess 
Assing the polcy for a role like Lambda function
When the function runs it automatically has those premissions - no hardcoding credentials so it can gain access to that service.


