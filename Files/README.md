# Simplifying-application-contanerization-with-EC2-ECR-ECS-Fargate-and-Load-Balancing

If you are new to containers, this step-by-step tutorial will show you how to leverage ECS to deploy containerized applications with load balancing, high availability, and proper database integration.
Do you want to use AWS to launch containerized apps without having to deal with Kubernetes' intricacy? Scalability and high availability are provided by AWS Elastic Container Service (ECS), a fully managed solution that streamlines container orchestration.


# Features of AWS ECS (Elastic Container Service)

- It enables you to run and manage Docker containers on a cluster of EC2 instances or Fargate(Serverless compute).

- It distributes containers across multiple Availability Zones within a region to ensure high availability and fault tolerance.

- It integrates seamlessly with other AWS services ELB, VPC, IAM, AWS CloudWatch, and AWS CodePipeline, enabling you to build comprehensive and scalable applications.

- It Provides built-in auto-scaling capabilities.


# Understanding AWS ECS Architecture and Components

ECS architecture comprises key components working together for container deployment and management:

**Clusters**: An ECS cluster is a logical grouping of tasks or services.

**Tasks**: Fundamental unit of ECS, tasks run containerized applications on EC2 instances or Fargate tasks.

**Task definition**: It is a blueprint for your application. You define parameters like Docker image, CPU, memory, networking, and dependencies.

**Service**: Maintain desired task count in a cluster, scaling automatically. Ensures high availability by distributing tasks and restarting failed ones.

**Container Instances**: EC2 instances or Fargate tasks running Docker daemon and hosting ECS containers.

ECS allows you 3 options to run your container-based application.

**AWS Fargate** — AWS manages the underlying infra where your containers will be running. It is serverless.

**AWS EC2 instances** — You run containers on the EC2 instances that you manage.

**On-prem VM** — You run your containers on on-prem VMs.

# Prerequisities

- An app ready to containerize

- AWS IAM permissions to access ECR, ECS, Fargate and ALB.

- AWS CLI configured (AWS Configure)


# Step-by-step guide to containerizing an application with EC2, ECR, ECS, Fargate and Load Balancer

# STEP 1: Create an EC2 instance

a. Create an instance either through the console or CLI.

b. Connect to the instance.

# STEP 2: Install Docker
a. Use `curl -fsSL https://get.docker.com -o get-docker.sh`
`yum install docker -y`

b. Start the docker daemon using `systemctl start docker`

c. Confirm docker daemon is runnng using `systemctl status docker`

d. Create a Dockerfile


![Screenshot 2025-04-15 162823](https://github.com/user-attachments/assets/a15cb5cd-fdab-4459-be68-681dfb9fe1ce)

e. Type vi Index.html to create your html file. Save and exit.

f. Build the Docker image
`docker build -t citycare_app .`

g. Containerize the docker image using `docker run -d -p 80:80 --name dawn citycare_app`

# STEP 3: Push image to Amazon ECR
a. Configure the AWS credntials using aws configure.

b. Create an ECR repository using `aws ecr create-repository --repository-name citycare_app`

c. Authenticate Docker to ECR using ` aws ecr get-login-password --region ca-central-1 | docker login --username AWS --password-stdin 241533137938.dkr.ecr.ca-central-1.amazonaws.com`

d. Tag and Push the image
`docker tag citycare_app:latest 241533137938.dkr.ecr.ca-central-1.amazonaws.com/citycare_app:latest
`
`docker push 241533137938.dkr.ecr.ca-central-1.amazonaws.com/citycare_app:latest`

# STEP 4: Set up Load Balancer (ALB)
1. Create an Application load balancer 

a. Give the ALB a name.

b. Select the VPC, Security Group and subnets

2. Create a target group

a. Give a name to the target group.

b. Protocol = HTTP, Port = 80

c. VPC: Choose your VPC

3. Create a listener rule

a. Default action = target group arn

b. Protocol = HTTP, Port = 80

# Note that the deployment for load balancer and service takes some time to be fully set up.

# STEP 5: Set up ECS cluster with Fargate

a. Create Cluster

b. After successfully creating the Cluster, go ahead and create Service.

c. Choose the Task definition family and proceed to give the Service a name. 

d. Choose "Use load balancing". Use existing load balancer and existing listener.

e. Create a new target group

f. Create the Service. 

g. Define Task Definition 

h. Under Container-1, give the container and name and paste the URL of the image.

# STEP 6: Deploy Service to ECS with load Balancer

a. Give the Service a name.

b. Choose the task definition

c. Desired count should be 2

d. For Network configuration, choose your VPC, subnets and security group. Enable assign public IP.

e. Under the load balancer, choose your target group, give a container name and container port. 
For the project, I used port 80.


# Summary
- I was able to dockerize an app.

- Pushed the app to Amazon ECR.

- Deployed it on ECS Fargate.

- Exposed it via a Load Balancer.

- Was able to access my app publicly.


# Result
![alt text](<Screenshot 2025-04-23 203259.png>)


