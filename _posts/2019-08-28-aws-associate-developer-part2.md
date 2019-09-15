---
title: "Ultimate AWS Certified Developer Associate 2019 - Part 2"
toc: true
toc_label: "Chapters"
categories:
  - Online Course
tags:
  - aws
---

# Programmatic

## AWS CLI - Command Line Interface

### Set Up AWS CLI

1. Install AWS CLI from official AWS website to always get the most updated installer. Install instructions differ for each operating system.

2. Check AWS CLI version
```
aws --version
```

3. Configure credentials that will be used to access the AWS. After configuring, the configuration files can be found in `~/.aws` or the Windows equivalent of user's home directory.
```
aws configure
# enter access key
# enter secret key
# enter default region
```

### Credentials Profile

We can manage our credentials for multiple AWS accounts by storing them as different profiles

```bash
aws configure --profile myProfile1

# we can now run AWS CLI command using specific profiles
aws s3 ls --profile myProfile1
```

### AWS S3 CLI

1. Use AWS S3 CLI document to reference the available commands.
2. Tip: use `--help` whenever we are unsure of the correct command usage.
3. Commands covered in tutorial:
```bash
# list all buckets
aws s3 ls 
# list all objects in a bucket
aws s3 ls s3://<bucket> 
# copy files to local machine
aws s3 cp s3://<bucket>/<object> localFileName
# make bucket
aws s3 mb s3://<newBucket>
# remove bucket
aws s3 rb s3://<bucket>
```

### AWS CLI on EC2

As a good security practice, **NEVER STORE PERSONAL CREDENTIALS ON EC2 INSTANCES**. Follow the best practice recommended by AWS:

- To use AWS CLI on EC2, we can attach IAM Roles to the EC2 instances.
- Policy can be used to authorize precisely what the EC2 IAM Role can/cannot do.
- EC2 attached with IAM Roles can use these access without additional configurations.

1. Create IAM Role. Select **AWS Service**, **EC2**, attached the desired policies and give this role a unique name.
2. In the EC2 instance settings, attach IAM Role and select the newly created Role.
3. SSH into EC2 instance (AWS CLI is already installed) and test out the AWS CLI commands with the newly granted permissions.
4. NOTE: Attaching IAM Role to EC2 instance may take a while to take effect as the changes are replicated through AWS infrastructure.
5. NOTE: Each EC2 instance can only be attached with one role at a time.

## IAM Roles and Policies

- After creating an IAM Role, Policies can be attached to it.
- We may also create Policies directly on top of Roles (called inline policy) 
- However this is not recommended as it is hard to manage. (the policies created cannot be accessed by other roles)

### AWS Managed Policies

- These are policies pre-built by AWS and available for use by default.
- Policies statements has 3 components:
  - Effect: whether actions are allowed or denied
  - Action: the actions that can be carried out (e.g. Get, List)
  - Resource: the resource that will be affected by the action
- Actions can use wildcard character to give access to all actions (e.g. Get*, List*)
- Specific resources can be specified using ARN.
- Policies can be created using Visual Editor or JSON Editor (under IAM console)
- We may also use another tool outside of IAM console, called AWS Policy Generator.

Creating policies on IAM console has an added advantage of viewing policy usage, and policy version history.

### AWS Policy Simulator

Can be used to test actions on selected roles and policies, to see if those actions are currently allowed.

This tool will help to save many hours of debugging application permission issues.

### CLI Dry-Run

- Some AWS CLI commands contains `--dry-run` options
- This allow us to test if the current permissions to perform the command is available, instead of actually running the commands (as some commands are expensive to test, such as creating a new EC2 instance
)

```bash
# dry run creation of new  EC2 instance
aws ec2 run-instances --dry-run --image-id <ami-id> --instance-type t2.micro
```

- If we do not have permission to perform this action, we will get a long cryptic error message that needs to be decoded to make sense (via AWS Security Token Service)

```bash
aws sts decode-authorization-message --encoded-message <message>
```

- STS decode authorization message permissions need to first be granted to the Role that is attempting to run this command.

## EC2 Metadata

- All EC2 instances are able to access their own metadata from within the instances, without using any IAM Role.
- EC2 metadata are accessible from **http://169.254.169.254/latest/meta-data**
- This allows us to retrieve the attached IAM Role name (part of the instance metadata), but not the associated IAM Policy (as such retrieval would require an authorized IAM Role).
- Metadata = information about EC2 instance
- Userdata = launch script of EC2 instance
- Being able to access an instance's own metadata will be helpful for automation in future.

```bash
# query from within EC2 instance

# shows all available API versions
curl http://169.254.169.254/
# use latest version and list all available metadata
curl http://169.254.169.254/latest/meta-data/

# show security credentials information
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/<iam-role-name>
```

- the last API query will return security credentials information of the currently attached IAM Role.
- It will reveal the access key and secret key that the EC2 instance is using.
- These are temporary values that will expire and be refreshed, to ensure security. But these are all handled by EC2 and IAM service under the hood, and the user do not need to do anything other than attaching the Roles and Policies.

## AWS SDK

- The SDK is used for our application code to access AWS.
- Fun Fact: the AWS CLI is actually a wrapper around the boto3 SDK (a.k.a the Python SDK).
- NOTE: if region is not configured when using SDK, the default will be us-east-1.
- For security credentials when using SDK, it is recommended to use the **default credential provider chain**:
  - the credentials file (~/.aws/credentials) for local machine
  - instance profile credentials using IAM Role (for EC2 instances or AWS services)
  - Environment Variables (export AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY)
- **NEVER STORE AWS CREDENTIALS IN SOURCE CODE**
- Exponential Back-off retry is included in most SDK API as a retry mechanism.