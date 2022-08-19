# Fargate-ECS-Loadbalancer

## Why loadbalacer is necessary
Elastic Load Balancing automatically distributes your incoming traffic across multiple targets, such as EC2 instances, containers, and IP addresses, in one or more Availability Zones. It monitors the health of its registered targets, and routes traffic only to the healthy targets. Elastic Load Balancing scales your load balancer as your incoming traffic changes over time. It can automatically scale to the vast majority of workloads. <br>

Reffer this doc:- https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/

## Architecture diagram:
![FaragteLB drawio](https://user-images.githubusercontent.com/63963025/184656219-310117e3-869b-4877-b0e1-49782a11610b.png)


## What is Amazon Elastic Container Service (ECS)
Amazon Elastic Container Service (Amazon ECS) is a highly scalable and fast container management service. You can use it to run, stop, and manage containers on a cluster. With Amazon ECS, your containers are defined in a task definition that you use to run an individual task or task within a service. In this context, a service is a configuration that you can use to run and maintain a specified number of tasks simultaneously in a cluster. You can run your tasks and services on a serverless infrastructure that's managed by AWS Fargate. Alternatively, for more control over your infrastructure, you can run your tasks and services on a cluster of Amazon EC2 instances that you manage. <br>

Reffer this doc:- https://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html

## What is AWS Fargate
AWS Fargate is a technology that you can use with Amazon ECS to run containers without having to manage servers or clusters of Amazon EC2 instances. With Fargate, you no longer have to provision, configure, or scale clusters of virtual machines to run containers. This removes the need to choose server types, decide when to scale your clusters, or optimize cluster packing.

Reffer this doc:- https://docs.aws.amazon.com/AmazonECS/latest/userguide/what-is-fargate.html

## What are Containers
Containers provide a standard way to package your application's code, configurations, and dependencies into a single object. Containers share an operating system installed on the server and run as resource-isolated processes, ensuring quick, reliable, and consistent deployments, regardless of environment.

Reffer this doc:- https://aws.amazon.com/getting-started/deep-dive-containers/#:~:text=Containers%20provide%20a%20standard%20way,consistent%20deployments%2C%20regardless%20of%20environment.

## What is Clusters
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
- [Code app.py](https://raw.githubusercontent.com/rushabhmahale/Fargate-ECS-Loadbalancer/master/app.py)
  
  ![image](https://user-images.githubusercontent.com/63963025/179386795-5b780acf-4b63-4ddb-b85d-886ac516f13b.png)

- Create <b>Dockerfile</b> 

![image](https://user-images.githubusercontent.com/63963025/180633226-5e21ffcf-ded0-4ad3-8e27-458358491bca.png)

- [Code Dockerfile](https://raw.githubusercontent.com/rushabhmahale/Fargate-ECS-Loadbalancer/master/Dockerfile)

- Create <b> requirement.txt </b> 

![image](https://user-images.githubusercontent.com/63963025/180633261-a20dfb3a-76ec-4167-a757-a33da7839400.png)

- [Code requirement.txt](https://raw.githubusercontent.com/rushabhmahale/Fargate-ECS-Loadbalancer/master/requirements.txt) 
 
- Create folder <b>templates</b> and add the <b>index.html</b> content 
 
![image](https://user-images.githubusercontent.com/63963025/180633506-1aaba8b8-8c69-43e8-b506-d1956ee9396d.png)

- tree

![image](https://user-images.githubusercontent.com/63963025/180633563-b9a0ad3d-1109-4804-80a5-b9986ca4fed1.png)

- [Code index.html](https://raw.githubusercontent.com/rushabhmahale/Fargate-ECS-Loadbalancer/master/templates/index.html)

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
![image](https://user-images.githubusercontent.com/63963025/181880264-cc757171-4224-4db2-a075-e195f1a884ff.png)

![image](https://user-images.githubusercontent.com/63963025/181882306-01828fb0-5ecc-4a01-a2be-dafc566126b8.png)


- Now go to IAM 

![image](https://user-images.githubusercontent.com/63963025/180635002-296aa88b-5176-4718-93b7-13326a0e0553.png)

- Go to user create user 
- Add username
- Select Access key - Programmatic access
![image](https://user-images.githubusercontent.com/63963025/180635112-9eaa5846-d5d7-4758-832c-866e96bed742.png)

- Attach existing policies directly:- <b> AmazonEC2ContainerRegistryFullAccess </b>

![image](https://user-images.githubusercontent.com/63963025/180648943-71344e5e-757f-4c50-80bf-cdffa1d5abec.png)


- Copy Access key and secret key we will use it later for authentication using awscli 

![image](https://user-images.githubusercontent.com/63963025/180647796-eac50831-4928-4c90-8d37-e5cb5274d876.png)

- <b> Note Don't share your Access-key and secret-key to anyone after use can deactivate that user access-key and secret-key</b>

- Setup aws cli profile fargate 
- aws configure --profile <profile name>

![image](https://user-images.githubusercontent.com/63963025/180648037-44ad28be-d2a0-47d1-8f0f-a549a8641313.png)

- confirm using  <b> aws ecr describe-repositories </b> command

![image](https://user-images.githubusercontent.com/63963025/180648330-bc9d506f-76e2-4120-a614-5c8f7523da00.png)

- Now lets push image to ECR 

  ![image](https://user-images.githubusercontent.com/63963025/181893137-2a2f9a72-d773-469f-8c9d-026b3e1b31c0.png)

  ![image](https://user-images.githubusercontent.com/63963025/181893675-0b43cffa-ee80-4cd2-aad8-41a565b08131.png)

- If you face any difficulty pushing image go to mentioned "Actions". Following JSON policy can be added in Amazon ECR >> Repositories >> Select Required Repository >> Permissions >> Edit statement add this principle 
  

```  
"ecr:BatchGetImage",
"ecr:BatchCheckLayerAvailability",
"ecr:CompleteLayerUpload",
"ecr:GetDownloadUrlForLayer",
"ecr:InitiateLayerUpload",
"ecr:PutImage",
"ecr:UploadLayerPart"
```
 

![image](https://user-images.githubusercontent.com/63963025/182036259-8478d0e2-3028-4dd3-903b-b68bf3035ae0.png)

![image](https://user-images.githubusercontent.com/63963025/183281018-8d00d693-c546-4027-97fa-6599c0071e95.png)

  
```
# Command to build an image
docker image build -t <IMAGE_NAME>:<IMAGE_TAG> .

```
```  
# Command to tag an image
docker image tag <IMAGE_NAME>:<IMAGE_TAG>  <REPOSITORY_URI>:<IMAGE_TAG>

```
```
# login ECR 
aws ecr get-login-password --region <REPOSITORY_REGION> | docker login --username AWS --password-stdin <REPOSITORY_URI>  
```  

```
# Push docker image to ECR 
docker image push <IMAGE_NAME[:TAG]>
```  

![image](https://user-images.githubusercontent.com/63963025/183281570-988fab22-0075-4978-9be4-de3d9058c9c5.png)

## Step5 Create ECS Amazon Elastic Container Service
Amazon Elastic Container Service (Amazon ECS) is a highly scalable and fast container management service. You can use it to run, stop, and manage containers on a cluster. With Amazon ECS, your containers are defined in a task definition that you use to run an individual task or task within a service. In this context, a service is a configuration that you can use to run and maintain a specified number of tasks simultaneously in a cluster. You can run your tasks and services on a serverless infrastructure that's managed by AWS Fargate. Alternatively, for more control over your infrastructure, you can run your tasks and services on a cluster of Amazon EC2 instances that you manage. <br>

Reffer this doc:- https://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html

- Go to ECS service create a cluster
![image](https://user-images.githubusercontent.com/63963025/184519828-8cae61f6-6e95-47a4-9cb1-c3c95e90cbaf.png)

- Select networking only powered by fargate 
![image](https://user-images.githubusercontent.com/63963025/184519868-4951ea8f-975f-4a66-8c66-78cac84e9a62.png)

- We are going to setup our cluster on default VPC because we have to setup in-bound out-bound rule and internet access in my case i am using default VPC you can create and setup your network all default and create cluster
![image](https://user-images.githubusercontent.com/63963025/184519988-31e498d9-e64c-45ba-9a95-ccbd60dc28c0.png)

 ![image](https://user-images.githubusercontent.com/63963025/184520038-123f0815-5a09-46bf-aad5-81bf3c889b25.png)

- now we have to create Task defination <br>
Task defination is basically a template where we can define environment variable mounting of disc you put your container image inside the task. 
![image](https://user-images.githubusercontent.com/63963025/184520131-54f1c363-4a25-4c2c-b5e6-52e66bad526c.png)

- Select Fargate
![image](https://user-images.githubusercontent.com/63963025/184520150-d598771d-3a92-4c3f-bf5a-1ffc47c64a33.png)
![image](https://user-images.githubusercontent.com/63963025/184520161-8b0379c3-bb88-4db8-baaa-bcc9cff17718.png)
- Task size we have to define 
<b>2GB</b>
<b>1vcpu</b>

- Now the main thing start form here we have to specify dockaer image port mapping and many more
Container name<br>
image copy image url from ECR that you have push to ECR repository 
Memory Limits (MiB): 2048
Port mappings: the port that you have map to your Dockerfile in my case the port number was 8081 
![image](https://user-images.githubusercontent.com/63963025/184520264-0f2b791f-6154-435f-924a-b28c173a1783.png)

## Step6 Create Loadbalancer HTTP/HTTPS Application Loadbalancer 
- Now go to Cluster section create service before creating service we need to create Loadbalancer go EC2 section >> Loadbalancer
we will select Application loadbalancer Http/Https 
![image](https://user-images.githubusercontent.com/63963025/184520578-303e6400-2845-40f7-ad7f-7dab3490bf5f.png)
![image](https://user-images.githubusercontent.com/63963025/184520676-ce449ffc-a805-4a7c-8592-587414542a34.png)
- Select all subnet 
![image](https://user-images.githubusercontent.com/63963025/184520690-3904c96e-4dc6-4f52-bbcd-cc5705cae27d.png)

- Create new security group 
![image](https://user-images.githubusercontent.com/63963025/184520722-0e170110-cf39-4632-b507-2be02a1e9ce3.png)

 ![image](https://user-images.githubusercontent.com/63963025/184520915-e8591849-f86a-4a46-bc41-3ef667a4857d.png)

- Create target Group in Listeners and routing there option Create target group 
![image](https://user-images.githubusercontent.com/63963025/184521009-f4d8c830-d935-4540-a39b-a22df621e29c.png)
![image](https://user-images.githubusercontent.com/63963025/184521014-4f981778-eb30-4974-8c6e-18c6d7652de6.png)
![image](https://user-images.githubusercontent.com/63963025/184521020-be6f872f-c507-4e00-8450-519188239bf7.png)
target group is been created lets attach to loadbalancer  
![image](https://user-images.githubusercontent.com/63963025/184521038-e09a72ad-8fbf-4725-bee5-3178530e33e6.png)

![image](https://user-images.githubusercontent.com/63963025/184521068-8072d281-0e2c-4298-ae2d-1c4ae06740b7.png)
create loadbalancer

  ## Attach Loadbalancer to ECS 
- lets create service in ECS 

![image](https://user-images.githubusercontent.com/63963025/184521729-f4e0875f-8ab1-4671-b2cd-c0ce882132e9.png)

  ![image](https://user-images.githubusercontent.com/63963025/184521733-dc9e747b-8b74-4c96-bd22-304c4aba29b9.png)
  ![image](https://user-images.githubusercontent.com/63963025/184521734-1711b373-e10c-4efc-8491-a631a773507e.png)

  ![image](https://user-images.githubusercontent.com/63963025/184521840-30922484-b107-4723-abaf-54ba74dc25ae.png)
![image](https://user-images.githubusercontent.com/63963025/184521884-9a02cfd0-8b88-4777-ab8d-8afe006ed351.png)
  - Configure FlaskAppConatiner:8081 listen at port number 80 which for webserver 
![image](https://user-images.githubusercontent.com/63963025/184521983-bd37c0e6-7467-4afe-bc23-36cac3ff4ff8.png)
  - next step
  ![image](https://user-images.githubusercontent.com/63963025/184522030-f58cc0ce-d08a-40cc-98d3-1c3fbfec3c9c.png)
- create service 
- this will take some to create task
  
  ![image](https://user-images.githubusercontent.com/63963025/184522064-8a6fa264-aace-4d57-a1ba-0669f8bd47ae.png)

  ## Block diagram 
 -  They will interact like this
  
  ![LB block for fargate](https://user-images.githubusercontent.com/63963025/184522303-73e2ea09-089a-4e99-a0cb-cf879d6bbda5.png)

  - We will get this type of 504 error 
  ![image](https://user-images.githubusercontent.com/63963025/184522488-1c9521e9-f9d0-426d-b667-3ee82825fbde.png)

  - Go to ECS select cluster >> Service >> details >> open Security Group 
  ![image](https://user-images.githubusercontent.com/63963025/184522578-4e5b3911-75e8-453a-9478-fb5547ad8e2b.png)

  - Select the Security group that you have assign to loadbalancer
  ![image](https://user-images.githubusercontent.com/63963025/184522613-519fbb41-8379-4441-84d8-7022c3eb337b.png)

- Here we are 
![image](https://user-images.githubusercontent.com/63963025/184522701-9a8b7bf1-4a8b-48b1-82d3-cce6e802e10c.png)

- Giving /app path 
  ![image](https://user-images.githubusercontent.com/63963025/184522666-c5703e40-b497-4d9d-9237-a620584da06f.png)
  
  
  ## Try Changing webpage content. We have successfully created loadbalancer with Fargate 
