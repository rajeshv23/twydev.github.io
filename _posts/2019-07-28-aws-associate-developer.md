---
title: "Ultimate AWS Certified Developer Associate 2019"
toc: true
toc_label: "Chapters"
categories:
  - Online Course
tags:
  - aws
---

# Exam Coverage

- 22% - Deployment
    - CICD, Beanstalk, Serverless
- 26% - Security
    - each service
- 30% - Development with AWS Services
    - Serverless, API, SDK, CLI
- 10% - Refactoring
    - AWS services best for migration
- 12% - Monitoring & Troubleshooting
    - CloudWatch, CloudTrail, X-Ray

# Fundamentals

## IAM - Identity & Access Management

### IAM Key Concepts

- **Regions** is a separate geographic area. (us-east-1)
- **Availability Zones** are isolated data centers in the Region. (us-east-1a)
- AWS consoles and resources are region scoped, except IAM and S3. Therefore permissions granted by IAM applies globally.

- **Root Account** is an AWS account used to sign up for the service.
- **Users** are IAM accounts used by physical persons.
- **Groups** are logical grouping of Users, to apply permissions to group for easy management.
- **Roles** are IAM accounts given to applications for internal usage within AWS
- **Never write IAM credentials in code**
- **Never use Root account for anything other than initial AWS set-up**

- **Federation** uses SAML standard to integrate with other enterprise Identity Provider to allow Single Sign-On.

### IAM Best Practice

- Delete Root account access keys
- Activate MFA on Root account
- Create individual IAM user
- Use groups to assign permissions
- Apply an IAM password policy
- Delegate administrative permissions to an "admin" user group, and use those IAM account to manage AWS from now.

## EC2 - Elastic Cloud Compute

The signature service offering of AWS. Understanding EC2 lays the groundwork to understanding cloud computing in general.

EC2 consists of the following capabilities:
- the virtual machines (EC2)
- storing data on virtual drives (EBS)
- distributing load across machines (ELB)
- scaling the services using an auto-scaling group (ASG)

EC2 instances have 5 distinct resources that determines their size:
- RAM (type, amount, generation)
- CPU (type, make, frequency, generation, number of cores)
- I/O (disk performance, EBS, optimization)
- Network (bandwidth, latency)
- GPU

Special mentions: 
- M instances are balanced. 
- T2/T3 instance are burst-able instances, which accumulates CPU credit if it is running at normal load, but will tap into burst credits to handle surge in computation, ensuring good CPU performance until credit runs out.
- T2 Unlimited is a new type that allows unlimited burst credit, which you will be billed after the fact.

### EC2 SSH Private Key

The SSH private key file generated from EC2 console will always encounter the error "Unprotected Private Key: permissions 0644 for file are too open" when you first open it from the local terminal. This means that other users on the machine are also able to access the file, potentially causing security leak. Solution is to run `chmod 0400 the-key-file.pem` to reduce the permission.

Using PuTTy on Windows machine requires the conversion of .pem file to .ppk file.

### Security Groups

Security group controls how traffic is allowed into and out of EC2. (It is like firewall rules).
- *specify access to ports*
- *authorized IP ranges (IPv4 adn IPv6)*
- *Control inbound network (default is all blocked)*
- *Control outbound network (default is all opened)*
- each group can be attached to multiple instances
- tied to a region/VPC combination
- exists outside of the EC2 instance (therefore not possible to troubleshoot from within EC2 if traffic from that instance has already been blocked)

**Specify another Security Group as Source/Destination**

This allows instances associated with the specified security group to access instances associated with current security group. This does not add rules from the source security group to current security group.

Usually, when encountered with network issues and unable to reach instances due to **time out**, it is likely due to misconfiguration of security groups. However, if error is **connection refused** then it is an application level problem.

### Elastic IPs

EC2 instances will be assigned a different IP every time it stops and starts. 

To use a fixed IP, you will need Elastic IP. **Limited to 5 Elastic IPs per AWS account**.

Try to avoid using Elastic IPs as:
- you are probably making poor architectural decisions
- you may use random IPs and register DNS name to it
- or use a Load Balancer without the need for fixed public IP.

### Installing Server

```shell
sudo su #assume superuser
yum update -y #update all packages using YUM
yum install -y httpd.x86_64 #install Apache httpd server
systemctl start httpd.service #starts it as a service using systemd
systemctl enable httpd.service #enable the service across machine reboot
```
At this point remember to allow HTTP access in the Security Group to reach the server from your local browser.

```shell
curl localhost:80 #test to see that server is working. do the same using your local browser.
echo "Hello World from $(hostname -f)" > /var/www/html/index.html #overwrite webpage with custom message and hostname
```

### EC2 User Data Script

- Allows us to bootstrap our EC2 instances.
- Script is only run once at the instance first start.
- Used to automate boot tasks such as installing updates, installing software, downloading common files, anything you want and etc.
- The script will run with machine root user identity.

The script is configured when provisioning a new EC2 instance in the console, under "Configure Instance Details".

Paste the script into the **User Data** text box. The first line is important to indicate the shell to be used.

```shell
#!/bin/bash
# install httpd (Linux 2 version)
yum update -y
yum install -y httpd.x86_64
systemctl start httpd.service
systemctl enable httpd.service
echo "Hello World from $(hostname -f)" > /var/www/html/index.html
```

Navigate to the website served by the Apache service of this new instance to see if User Data Script is working. Likewise, you may SSH to the new instance and inspect the index.html file to verify that the script has been executed.

### EC2 Instances Pricing Options

Instance type refers to sizing, like t2-micro and etc.

- **On Demand Instances** pay as you use, highest cost, but no upfront payment or commitment. For short-term, un-interrupted workloads where you cannot anticipate your application behavior.
- **Reserved Instances** pay upfront, commit between 1-3 years, reserve a specific instance type, discount up to 75% compared to On Demand. Recommended for steady usage applications (database).
    - **Convertible Reserved Instance** - is a Reserved Instance but allows changing of EC2 instance type during commitment. But the added feature reduces the discount to 54%.
    - **Scheduled Reserved Instances** - is a Reserved Instance scheduled to run on recurring time window.
- **Spot Instances** offer up to 90% discount compared to On Demand instances. Bid a price to get an instance, and as long as the current spot price is under the bid, you will get the instance.
    - instances are reclaimed with a 2 minute notification warning when spot price goes above bid price.
    - good for batch jobs, big data analytics, workloads resilient to failures.
    - not good for critical jobs or databases
- **Dedicated Hosts** offers physically dedicated EC2 server. Full control of EC2 instance placement, with visibility of underlying sockets, physical cores of hardware.
    - reserve for 3 years
    - more expensive
    - suitable for Bring Your Own License (BYOL) customers
    - suitable for regulatory compliance requirements
    - billed per host
- **Dedicated Instances** the hardware is still dedicated to you, but unlike Dedicated Host, you have no visibility of hardware. 
    - automatic instance placement
    - may share hardware with other instances in your account
    - on instance stop/start, the hardware may change.
    - billed per instance

### Pricing

EC2 instance prices (per hour) varies based on:
    - AWS region
    - instance type
    - instance pricing option
    - operating system

Billing is by the second, but with a minimum of 60 seconds first.

Final bill may include storage, data transfer, fixed IP public addresses, load balancing, and whatever additional services used.

But if instance is stopped, you will not be billed.

### Amazon Machine Images

Instead of using the default AMIs provided, you may use your own custom AMI for the following:
- pre-install packages needed, which is faster to boot than relying on EC2 User Data Script
- pre-install monitoring/enterprise software, such as Active Directory integration
- security concerns
- Control of maintenance and updates over time
- pre-install application for faster deploy when auto-scaling
- using other AMI that is optimized for specific purposes

**AMI are locked to a specific AWS region!**

### EC2 Exam Checklist
- know how to SSH into instance
- know how to properly use security groups
- know the fundamental differences between private vs public vs elastic IP
- know how to use User Data to customize instance at boot time
- know about custom AMI to enhance the OS
- know that EC2 instances are billed per second, and can be easily spined up/tear down

## ELB - Elastic Load Balancer

### Purpose of ELB
Similar to classic load balancers:
- spread load across multiple downstream instances
- expose single point of access via DNS to your application
- resilient to downstream failures
- perform regular health checks on downstream (can specify route for ALB)
- SSL termination (HTTPS) for website, and forward traffic as normal HTTP to downstream
- enforce stickiness with cookies so that client communicate to the same instance
- high availability across zones
- separate public from private traffic
- *AWS managed (guaranteed uptime, upgrades and maintenance, HA, easily configured)*
- *integrated with many AWS services*

### Application Load Balancer v2
- LB to multiple HTTP apps across machines (target groups)
- LB to multiple apps on same machines (containers)
- LB based on route in URL
- LB based on hostname in URL
- port mapping feature to redirect to dynamic port
- stickiness provided by ALB directly, and can be enabled at target group level, to ensure same requests go to same instance.
- supports HTTP/HTTPS/Websockets protocol
- applications don't see client IP directly. ALB terminates client connection and start a new one with downstream.
    - client true IP is in the header **X-Forwarded-For**
    - **X-Forwarded-Port** and **X-Forwarded-Proto** contains port and proto information.

### Network Load Balancer v2
- forward TCP traffic
- millions of request per seconds
- support static or elastic IP
- less latency (100ms vs 400ms for ALB)
- used for extreme performance and hardly the default choice
- directly sees client IP

### Classic Load Balancer
- is deprecated
- all 3 LBs have health check capabilities
- only CLB and ALB supports SSL certificates and SSL termination
- all LB uses a static host name. Do not resolve and use the underlying IP.
- LBs can scale, but not instantaneously
- HTTP 4xx errors are client induced
- HTTP 5xx errors are application induced
    - 503 means LB error, either capacity issue or no registered target
- if LB cannot connect to application, check security groups

## ASG - Auto Scaling Group

### Purpose of ASG
- scale out more EC2 instances on load increase/scale in on load decrease
- ensure minimum/maximum number of instances running
- automatically register new instances to LB

### ASG attributes
It has a launch configuration which states the details of EC2 instances it will be provisioning:
- AMI, Instance Types, User Data
- EBS Volumes
- Security Groups
- SSH Key Pair

You can also configure min size/max size/initial capacity, network information, LB information and scaling policies.

### Auto Scaling Alarms
- CloudWatch monitors a metric and raise alarm on conditions
    - (if an ASG metric is selected, it will be computed for the overall ASG instances)
- based on the alarm, we can create scale out/scale in policies
- can also create alarm using custom metric sent from applications to CloudWatch

### ASG other details
- Scaling policies can be based on CloudWatch metrics, custom metrics, even schedules
- IAM roles attached to ASG will get assigned to EC2 instances
- ASG are free. You only pay for underlying instances
- Having instances under ASG provides extra guarantee
- ASG can terminate instances marked by LB as unhealthy

## EBS - Elastic Block Store
EBS Volume is a network drive you can attach to your instance while they run, ensures the drive persists even if instance terminates unexpectedly.

### EBS details
- network drive, so might have a bit of latency
- but is able to detach and attach to other instances quickly
- is locked to AZ (rather than region. To move volume across AZ requires you to create snapshot).
- have a provisioned capacity (in GB space and IOPS) which will be billed regardless of usage. You may increase capacity over time.

### EBS Volume Types
(not testing specifics)
- GP2 is general purpose SSD balancing price and performance
- IO1 is highest-performance SSD for low latency high throughput
- ST1 is low cost HDD volume for throughput intensive usage
- SC1 is lowest cost HDD for less frequent access

### Volume Resize
- can only increase EBS volume
- increase by size
- increase by IOPS is only for IO1 type
- after resizing, need to repartition the drive

### Snapshot
- snapshot only take the actual space of the data, not the entire EBS volue
- used for backups
- used for migration (resizing down, changing volume type, encrypting, changing AZ)

### Encryption
Creating an encrypted EBS volume lets you benefit from the following:
- **data at rest encryption**
- **data in flight encryption** between the instance and the volume
- all snapshots are encrypted
- all volumes created from snapshot are encrypted
- is transparent to the user
- AWS guarantees minimal latency
- encryption leverages keys from KMS
- copying an un-encrypted snapshot allows encryption to create a new volume

### EBS other notes
- some EC2 instances use instance store physically attached to the machine
    - faster IO performance
    - on termination, instance store will be lost
    - cannot be resized
    - backups must be operated by user
- EBS should meet most use cases compared to instance store unless you need extreme performance
- EBS only attach to one instance at a time
- EBS backup uses IO so should be run during your application off-peak window
- by default Root EBS Volumes on EC2 instances will be deleted on termination, which you can disable this option.

# ToBeContinued