---
title: "Ultimate AWS Certified Developer Associate 2019 - Part 4"
toc: true
toc_label: "Chapters"
categories:
  - Online Course
tags:
  - aws
---

# Serverless

Part 4 of this course covers Serverless components offered by AWS (Lambda and Step Functions, DynamoDB, Cognito, API Gateway, S3, SNS, SQS, Kinesis, Aurora Serverless)

# AWS Lambda

## Benefits of Lambda

- pay per request and compute time
  - usually very cheap that is why it is popular
- integrated with whole AWS stack
  - easy monitoring on CloudWatch
- compatible with many languages
  - NodeJS, Python, Java, C# (.NET Core), Golang, Powershell
- easy to scale up resources
  - up to 3GB RAM
  - larger RAM sizes also mean larger CPU and network resources

## Lambda Configuration

- Timeout, 3s (default) to 15 mins
- Environment Variables
- Allocated Memory (128M to 3G)
- deploy within VPC, assign security group
- attach IAM execution role to Lambda

### Concurrency and Throttling

- can have up to 1000 concurrent executions per account (request ticket to increase)
- set **reserved concurrency** at function level
- invocation beyond limit will trigger a throttle
  - if synchronous invoke, return ThrottleError 429
  - if asynchronous invoke, lambda will retry automatically or message goes to DLQ
  - DLQ can be just a SNS topic or a SQS queue (ensure IAM permissions)

### Logging, Monitoring, Tracing

CloudWatch
- Lambda execution logs are stored in CloudWatch
- Lambda metrics are displayed in CloudWatch
- ensure Lambda execution role has sufficient permission to write to CloudWatch

X-Ray
- enable in Lambda configuration (Lambda automatically runs X-Ray daemon)
- use AWS SDK in function code
- similarly ensure IAM role has appropriate permissions

### Lambda Limits

1. Memory Allocation
  - between 128 MB to 3008 MB (64 MB increments)
2. Maximum Execution Time
  - previously 5 mins, now 15 mins
3. Disk Capacity in Function Container
  - 512 MB
4. Concurrency Limit
  - 1000 executions
5. Function Deployment Size
  - 50 MB (compressed .zip)
  - 250 MB code and dependencies (uncompressed deployment)
  - can use /tmp directory to load other files at startup
6. Environment Variables Size
  - 4 KB

### Versioning

- **$LATEST** version is mutable
- published versions are immutable
  - immutable for both code and configs
  - versions numbers are increasing
  - each version gets their own ARN (Amazon Resource Name)
  - all versions can be accessed using the correct ARN
- **Aliases** are pointers to specific versions of lambda function
  - mutable, can point to a different version
  - can be used to define dev, test, prod etc
  - alias can redirect weighted traffic to different versions
  - enable Blue / Green deployment
  - abstracts away the actual version, enable stable configuration of event triggers and destinations
  - aliases have their own ARN

### External Dependencies

- Dependency packages need to be installed alongside function code and zip together
  - NodeJS, use npm and *node_modules* directory
  - Python, use *pip --target* options
  - Java, include relevant *.jar* files
- Upload zip straight to lambda if less than 50 MB, else upload to S3 first
- Native libraries work, need to be compiled on Amazon Linux first

### Deploy using CloudFormation

- Simply zip and store function code in S3
- Reference S3 location in CF

### Lambda local /tmp directory

- can be used if function needs to download a big file
- or if function needs disk space
- max size of /tmp is 512 MB
- provides transient cache across multiple invocations (if context is frozen, helpful for checkpointing work)
- recommended to use S3 if persistence is required

### Best Practices

- Perform heavy-duty work outside of function handler, such as
  - connect to db
  - initialize AWS SDK
  - pull in dependencies or datasets
- Use environment variables for
  - db connection strings, S3 bucket
  - password, sensitive values (can be encrypted using KMS)
- Minimize deployment package size to its runtime necessities
  - break down the function if necessary
  - don't exceed lambda limits
- avoid using recursive code (have lambda function calls itself)
- recommended not to use VPC unless necessary

### Lambda@Edge

Deploy Lambda functions alongside CloudFront CDN
- more responsive
- deployed globally
- can be used to customize CDN content
- pay for only what you use

Can be used to change CloudFront requests / responses
- after requests received from viewer
- before requests forwarded to origin
- after responses received from origin
- before responses forwarded to viewer
- can even generate responses for viewer without ever forwarding to origin

Use cases
- website security and privacy
- dynamic web app on the edge
- search engine optimization
- intelligently route across origins and data centers
- bot mitigation on the edge
- real-time image transformation 
- A/B testing
- user authentication / authorization
- user prioritization
- user tracking and analytics