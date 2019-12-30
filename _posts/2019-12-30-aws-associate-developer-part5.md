---
title: "Ultimate AWS Certified Developer Associate 2019 - Part 5"
toc: true
toc_label: "Chapters"
categories:
  - Online Course
tags:
  - aws
---

# Other AWS Services

Part 5 of this course covers ECS, KMS, and other loose ends that are required for the exam.

# AWS Elastic Container Service (ECS)

## Docker

It is essential to have a basic understanding of Docker before using ECS.

- Docker is a software development platform to deploy apps
- Package apps into containers. Run on any OS.
- Predictable behavior, multi-language, easier to deploy and maintain.
- Docker images are stored in Docker Repositories (Docker Hub, AWS Elastic Container Repository ECR)
- More lightweight than VMs since there are no guest OS involved.
- Dockerfile builds Docker Images, which can be pushed to Repositories, or deployed as Containers.

## ECS Clusters

- Clusters are logical groups of EC2 instances
- EC2 instances runs a special AMI optimized for ECS
- Must configure file **/etc/ecs/ecs.config**
- EC2 instances runs an ECS agent, which registers itself with the ECS Cluster service
- ECS agents will be coordinating and launching the containers on the EC2 instances
- underlying technology is still Docker.
- Note: Security Groups apply to EC2 instances, not tasks / containers running within it

### Task Definitions

- Metadata in JSON format to tell ECS how to run a Docker Container, containing:
  - image name
  - port binding for container and host
  - memory and CPU required
  - environment variables
  - networking information
- For multi container environment, must NOT define host port.

### ECS Service

- Defines how many tasks should run and how to run them
- Ensures the desired number of tasks are running across EC2 instances fleet
- Can be linked to ELB / NLB / ALB if required

### ALB Dynamic Port Forwarding

- Needs to create ALB separately
- Dynamic Host Routing is a feature that comes with ALB
- Adding an ALB to an ECS Service needs to be done at creation, and will need you to recreate a Service for existing ECS Services.
- Needs to configure security group, allowing EC2 fleet and ALB to reach each other.

## Elastic Container Repository (ECR)

- Private Docker Repository by AWS
- Access controlled through IAM
- Run AWS ECR push and pull commands to manage images and interact with ECR

`$(aws ecr get-login --no-include-email --region [region])`

The inner command generates a Docker login command for ECR and the outer parenthesis executes the resultant command immediately. **This needs to be done whenever a new session needs to interact with ECR (push or pull image).** Next we need to build and tag the docker image, before finally pushing it to the ECR.

```bash
docker build -t demo
docker tag <original-name> <aws-account>.dkr.ecr.<region>.amazonaws.com/<original name>
docker push <new-name-from-prev-command>

# to pull image
docker pull <new-name-from-prev-command>
```

- To use ECR images, need to configure Task Definitions
- EC2 instances needs to have permissions to pull from ECR

# AWS Fargate

Without Fargate, to scale ECS clusters require manual launching and adding of EC2 instances to the cluster.

Fargate abstracts away infrastructure management, so that developers only need to manage Task Definitions. Fargate will scale EC2 fleet accordingly. Essentially making ECS serverless!

- Task Definitions need to be created with Fargate compatibility
- Similarly, Clusters and Services needs to be created with Fargate compatibility.
- Note: Fargate tasks / containers can have IAM roles.

## X-Ray Integration

For ECS Clusters (using EC2 fleet):

1. **X-Ray Container as Daemon.** Runs a container of X-Ray Daemon in each EC2 instance.

2. **X-Ray Container as a Side Car.** Runs the X-Ray Daemon in each application container instead.

For Fargate Clusters, we have to use the side car pattern to configure X-Ray Daemon within application container in Fargate Tasks.

> Important Note on Task Definitions:
> X-Ray Daemon port 2000, protocol UDP
> Set Environment Variable called AWS_XRAY_DAEMON_ADDRESS to the daemon defined above
> Link the daemon to the application container

## Elastic Beanstalk Single / Multi Docker Container Mode

- Requires a config file at source code root, **Dockerrun.aws.json**
- This mode helps to create ECS Cluster, EC2 fleet configured with ECS agents, Load Balancer, Task Definitions and Execution.
- Must separately pre-build Docker images in ECR
