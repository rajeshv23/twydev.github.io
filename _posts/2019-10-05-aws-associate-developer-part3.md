---
title: "Ultimate AWS Certified Developer Associate 2019 - Part 3"
toc: true
toc_label: "Chapters"
categories:
  - Online Course
tags:
  - aws
---

# Infrastructure

Part 3 of this course covers infrastructure as code using AWS CloudFormation, monitoring of applications on AWS and integration components such as SQS and SNS.

## AWS CloudFormation

### Common Usage and Benefits

- declarative way to outline AWS infrastructure for almost all resources
- after declaring, AWS CF will **automatically** create resources in the **right order**
- users do not need to figure out the order of provisioning, and no need to worry about orchestration
- able to leverage existing CF templates on the web to deploy almost any stack

**Control**

- infrastructure created from code, no manual creation, less prone to human error.
- infrastructure as code can be version controlled
- changes to infrastructure can be reviewed from code
- infrastructure can be created and destroyed on the fly
- automated AWS resource diagram generation
- separation of concern by creating many different CF stacks for many apps and many layers e.g. a VPC stack, a network stack, an app stack

**Cost**

- each resource in a CF stack can be identified helps with cost estimation and calculation
- enable cost saving strategy, e.g. routinely create stack in the morning and delete stack at the end of day.

### CF process

- templates are first uploaded to S3 and then referenced in CF
  - we update templates by re-uploading a newer version to S3
- CF creates stack based on declaration in template
- stacks are identified by a name
- deleting a stack deletes every single artifact that was created by CF

### CF Template Components

```yaml
Resources:
  MyInstance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t2.micro
    SecurityGroups:
      - !Ref ServerSecurityGroup
  
  ServerSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0
```

- Resources (mandatory)
  - represents the AWS components to be created / updated
  - identifier of the form `AWS::aws-product-nam::data-type-name`

- parameters
- mappings
- outputs
- conditionals
- metadata

- references
- functions