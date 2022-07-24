# Fargate-ECS-Loadbalancer

## Why loadbalacer is necessary
Elastic Load Balancing automatically distributes your incoming traffic across multiple targets, such as EC2 instances, containers, and IP addresses, in one or more Availability Zones. It monitors the health of its registered targets, and routes traffic only to the healthy targets. Elastic Load Balancing scales your load balancer as your incoming traffic changes over time. It can automatically scale to the vast majority of workloads. <br>

Reffer this doc:- https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/

## What is What is Amazon Elastic Container Service (ECS)
Amazon Elastic Container Service (Amazon ECS) is a highly scalable and fast container management service. You can use it to run, stop, and manage containers on a cluster. With Amazon ECS, your containers are defined in a task definition that you use to run an individual task or task within a service. In this context, a service is a configuration that you can use to run and maintain a specified number of tasks simultaneously in a cluster. You can run your tasks and services on a serverless infrastructure that's managed by AWS Fargate. Alternatively, for more control over your infrastructure, you can run your tasks and services on a cluster of Amazon EC2 instances that you manage. <br>

Reffer this doc:- https://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html

## What is AWS Fargate
AWS Fargate is a technology that you can use with Amazon ECS to run containers without having to manage servers or clusters of Amazon EC2 instances. With Fargate, you no longer have to provision, configure, or scale clusters of virtual machines to run containers. This removes the need to choose server types, decide when to scale your clusters, or optimize cluster packing.

Reffer this doc:- https://docs.aws.amazon.com/AmazonECS/latest/userguide/what-is-fargate.html

## What are Containers
Containers provide a standard way to package your application's code, configurations, and dependencies into a single object. Containers share an operating system installed on the server and run as resource-isolated processes, ensuring quick, reliable, and consistent deployments, regardless of environment.

Reffer this doc:- https://aws.amazon.com/getting-started/deep-dive-containers/#:~:text=Containers%20provide%20a%20standard%20way,consistent%20deployments%2C%20regardless%20of%20environment.

## what is Clusters
An Amazon ECS cluster is a logical grouping of tasks or services. You can use clusters to isolate your applications. When your tasks are run on Fargate, your cluster resources are also managed by Fargate.

Reffer this doc:- https://docs.aws.amazon.com/AmazonECS/latest/userguide/clusters.html


# Steps to be followed:- 
## Step1 Create a ec2 machine
- Create ec2 machine 
<img width="1142" alt="image" src="https://user-images.githubusercontent.com/63963025/179385839-f6cdcb41-10da-4227-ab3c-409111e9af85.png">

Reffer to this doc steps to create ec2 machine :- https://docs.aws.amazon.com/efs/latest/ug/gs-step-one-create-ec2-resources.html

## Step2 Connect the instance via ssh 
![image](https://user-images.githubusercontent.com/63963025/179386226-79cce9b3-8de9-44e3-bbed-7161bfaa1add.png)

- <b>Note:- This only work with aws and ubuntu machine we have to use putty or Dameware SSH client , etc </b> 
- sudo su 
- mkdir fargate-code 
- cd fargate-code 
- vi app.py 
- [Code app.py](*)
  
  ![image](https://user-images.githubusercontent.com/63963025/179386795-5b780acf-4b63-4ddb-b85d-886ac516f13b.png)

- Create <b>Dockerfile</b> 

![image](https://user-images.githubusercontent.com/63963025/180633226-5e21ffcf-ded0-4ad3-8e27-458358491bca.png)

- [Code Dockerfile](*)

- Create <b> requirement.txt </b> 

![image](https://user-images.githubusercontent.com/63963025/180633261-a20dfb3a-76ec-4167-a757-a33da7839400.png)

- [Code requirement.txt](*) 
 
- Create folder <b>templates</b> and add the <b>index.html</b> content 
 
![image](https://user-images.githubusercontent.com/63963025/180633506-1aaba8b8-8c69-43e8-b506-d1956ee9396d.png)

- tree

![image](https://user-images.githubusercontent.com/63963025/180633563-b9a0ad3d-1109-4804-80a5-b9986ca4fed1.png)

- [Code index.html](*)

![image](https://user-images.githubusercontent.com/63963025/180633621-1c315e74-1a01-446a-b9d5-c292a05b60cf.png)

## Step3 Create Dockerimage 

- <b>Note:-Install Docker in Amazon Linux 2 if not install:- https://docs.aws.amazon.com/AmazonECR/latest/userguide/getting-started-cli.html </b> 
- yum update -y 
- yum install docker -y 
- systemctl start docker
- Now lets create image 
- docker build -t fargate:v1 . 
![image](https://user-images.githubusercontent.com/63963025/180634141-c6f4e9fb-7a3b-45d8-9392-1a569825b7c2.png)
- docker image (verify image is been created) 
![image](https://user-images.githubusercontent.com/63963025/180634169-f182ff5d-683a-4509-b429-8033567a8b98.png)
- Before uploading image to ECR lets test the image its working or not 
- docker run -dit -p8080:8081 fargate:v1 
- docker ps 
- curl <intenralip_ec2>:8080
![image](https://user-images.githubusercontent.com/63963025/180634238-67f23800-c3b8-4f51-90b5-e5eba701206a.png)

## Step4 Push image to ECR( Elastic Container Registry)
- Search for ECR 
![image](https://user-images.githubusercontent.com/63963025/180634736-7110e2f0-8d5a-4b3a-918f-c1745a1250f3.png)

- Get started and create ECR repository
 ![image](https://user-images.githubusercontent.com/63963025/180634750-e2c2e778-6cf1-4f59-9f9e-3f4739c09dad.png)

- Create Private Repo (good to create private repo because we dont want to expose our image publically specially in production environment) 
![image](https://user-images.githubusercontent.com/63963025/180634834-712fb215-bc70-4191-92d0-96d4b64fa529.png)

![image](https://user-images.githubusercontent.com/63963025/180634965-73a77986-20bb-4898-bcdf-8ff3c8497ccb.png)

- Now go to IAM 

![image](https://user-images.githubusercontent.com/63963025/180635002-296aa88b-5176-4718-93b7-13326a0e0553.png)

- Go to user create user 
- Add username
- Select Access key - Programmatic access
![image](https://user-images.githubusercontent.com/63963025/180635112-9eaa5846-d5d7-4758-832c-866e96bed742.png)

- Attach existing policies directly:- <b> AmazonElasticContainerRegistryPublicFullAccess </b>



