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
- 
