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
Mappings:
  RegionMap:
    us-east-1:
      "dev": "us-east-1a"
      "test": "us-east-1b"
      "prod": "us-east-1c"

Resources:
  MyInstance:
    Type: AWS::EC2::Instance
    Properties:
      AvailabilityZone: !FindInMap [RegionMap, !Ref AWS::Region, !Ref Env]
      InstanceType: !Ref InstanceTypeParam
    SecurityGroups:
      - !Ref ServerSecurityGroup
      - !ImportValue SSHSecurityGroup
  
  ServerSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0

Parameters:
  InstanceTypeParam:
    Type: String
    Default: t2.micro
  Env:
    Type: String
    Default: dev

Outputs:
  InstanceID:
    Condition: IsProdEnv
    Value: MyInstance
    Export:
      Name: MyInstanceRefId # used by other stacks when importing

Conditions:
  IsProdEnv: !Equals [ !Ref Env, prod ]
```

- **Notes**
  - Dashes are used to indicate array elements in YAML
  - `!Ref` is used to refer to parameters / resources
  - `!FindInMap` is used to get value from Mappings
  - `!ImportValue` is used to import value from the Output of other stacks
  - Intrinsic function used by Conditions are `Fn::And, Or, Equals, If, Not`
  - `!GetAtt` is used to obtain attribute of a resource
  - `!Join [ delimiter, [list of values] ]`, `!Sub [ target, { currentVal: newVal } ]` are String manipulation functions.
- **Resources** (mandatory)
  - represents the AWS components to be created / updated
  - identifier of the form `AWS::aws-product-nam::data-type-name`
- **Parameters**
  - useful to dynamically provide inputs to CF templates
  - especially if you want to reuse components
  - or if values cannot be determined ahead of time
  - can be controlled with various settings such as data type, constraints, min/max length and value, defaults, allowed values and patterns etc
  - AWS provides pseudo parameters that can be used at anytime in the CF template, such as:
    - AWS::AccountId
    - AWS::Region
    - AWS::StackId
    - AWS::StackName
- **Mappings**
  - Key-Value pairs that are used to declare values in bulk if you know the values ahead of time.
- **Outputs**
  - declares values to be exported from CF stack
  - these values can be used by other stacks
  - some values can only be determined after stack is created, such as Subnet ID, or VPC ID, and Output is the dynamic way to obtain these values
  - **NOTE** CF stack cannot be deleted if its output values are referenced by another stack
- **Condition**
  - Logical evaluation to determine condition of deployment
  - Can be applied to resources / outputs etc.

### CF Rollbacks

- On Stack **Creation** Failure
  - default is to rollback and delete all resources created by CF, and users will only be able to refer to the logs.
  - disabling the default will stop rollback, allowing you to troubleshoot.
- On Stack **Update** Failure
  - Stack automatically rollback to previous working state
  - See the logs to find error messages

## AWS Monitoring

TBC