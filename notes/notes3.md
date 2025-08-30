AWS Containers
---
The Container Services in AWS:
ECS - Elastic Container Service is AWS's own container orchestration platform and it is a fully managed service which allows for docker containers without having to install and manage orchestration software.

EKS - Elastic Kubernetes Service is a way to use Kubernetes within AWS without managing the control plane.

Fargate - This is a serverless platform or service container platform which works with ECS and EKS. Fargate removes the need to manage servers or EC2 instances and you just define the task and fargat does the provision of
the infrastructure automatically.

ECR - Elastic Container Registry is similar to Docker hub where you can store, manage and retrieve docker images. 

**Launch types**
WHen containers are deployed with ECS, you can choose how ECS will run the containers with 3 launch types

EC2 - The underlying infrastructure is is managed by you but ECS handles all the container magic. - Good if you want more control over the instances like customising them for performance or security needs

Fargate - This is serverless so you dont worry about managing EC2 instances and there's no infrastructure to manage. 

External - handled by you for the full infrastructure.


**ECS Task Role** and **ECS Instance Profile**
The ECS Instance profile is for EC2 launch type only as the ECS agent running on each EC2 instance needs permissions to interact with other AWS Services so it uses the ECS Instace Profile to handle that.

SOme more tasks it does with instance profile is API calls to ECS to update the cluster on whats happening with the containers such as sending logs to CloudWatch so you can see whats going on with the containers.
In short, ECS Instance Profile makes sure the ECS agent can do its job by giving it access to other AWS Services securely.

the **ECS Task Role** defines what permissions the task has while it's running e.g. different contains might need access to different AWS services such as Task A will need to read from S3 or write logs to CloudWatch and
Task B might need to write and read data to Dynamo DB. By using separate roles for each task, you can lockdown what each container is allowed to do. 

By giving these roles to the contains, we allow for them to securely interact with AWS services without having to give them too much power as we wont want each container to have unrestricted access to all AWS Services.

