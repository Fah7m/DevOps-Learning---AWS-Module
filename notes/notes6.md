Cloudfront
---

CloudFront is a content delivery network on AWS that is designed to boost performance by caching your content closer to your users at **Edge locations**

Normally users will reach all the way back to your original server like S3 bucket or EC2 load balancers however for CloudFront, the content is cached right at the edge which results in faster load times, better user experience
and fewer resources being used by the origin server.

CloudFront integrates with AWS Sheild and AWS WAF to help protect your resources from DDOS attacks and other threats. AWS has around 216+ Points of Presence which simply put is a caching facility that Cache and deliver content
quickly to users who are geographically close. 

How it works:
User requests image.jpg via CloudFront
CloudFront checks the nearest POP (Edge location)
If cached the image gets served instantly
If not cached it fetches it from origin server, caches, then serves to user
Subsequent users get it from cache and not origin server.

The origins are where CloudFront gets the content it delivers to users and one of the basic ones are S3 buckets. These are good for distributing files like images, videos or any static content. 

With CloudFront, you get an extra layer of security using **Orgiin Access Control** which basically ensure only CloudFront can access the S3 bucket content and no direct S3 access from the web. - CF can be used as ingress
to upload files to S3

Custom Origins are used when working with HTTP based backends and CF can distribute content from ALBs and EC2 instances a application is being run in. A static Website Hosting will be needed in the bucket or any HTTP backend. 

<img width="916" height="535" alt="image" src="https://github.com/user-attachments/assets/46cdc989-cd36-4228-a996-88884eb8e6ce" />
**Illustration of how it would look with an OAC**

