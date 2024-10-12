# EC2 Instance CloudFormation Template

## Overview

This CloudFormation template automates the creation of an EC2 instance in AWS. It dynamically retrieves the Amazon Machine Image (AMI) ID based on the region where the stack is deployed, utilizing the Mappings feature for easy region-based configuration.

## Objective

The primary objective of this lab is to deploy a web server EC2 instance in your desired AWS region, with the ability to select different instance types.

## Features

- **Dynamic AMI Retrieval**: The template includes a mapping of AMI IDs specific to different AWS regions.
- **Instance Type Parameter**: You can specify the instance type with predefined options.
- **Tagging**: The instance is automatically tagged for easy identification.

## Mappings

The following AMI IDs are predefined for various regions:

```yaml
Mappings:
  RegionBasedAMIIDMap:
    us-east-1:
      MyAMI: ami-0ff8a91507f77f867
    us-west-1:
      MyAMI: ami-0bdb828fd58c52235
    eu-west-1:
      MyAMI: ami-047bb4163c506cd98
    ap-southeast-1:
      MyAMI: ami-08569b978cc4dfa10
    ap-northeast-1:
      MyAMI: ami-06cd52961ce9f0d85
```

## Parameters

### InstanceTypeParameter

- **Description**: Choose the EC2 instance type for the web server. Default is `t2.micro`.
- **Type**: String
- **Default**: `t2.micro`
- **Allowed Values**:
  - `t2.micro`
  - `m1.small`
  - `m1.large`

## Resources

### EC2 Instance

This template creates an EC2 instance with the following properties:

```yaml
Resources:
  myEC2Instance:
    Type: "AWS::EC2::Instance"
    Properties:
      ImageId: !FindInMap [RegionBasedAMIIDMap, !Ref AWS::Region, MyAMI]
      InstanceType: !Ref InstanceTypeParameter
      Tags:
        - Key: "Name"
          Value: "Benwebserver"
        - Key: "Department"
          Value: "Finance"
```

- **ImageId**: Dynamically set based on the region using the `FindInMap` function.
- **InstanceType**: Set according to the parameter provided during stack creation.
- **Tags**: Includes tags for identification and organizational purposes.

## Usage

1. Open the AWS Management Console.
2. Navigate to the CloudFormation service.
3. Create a new stack.
4. Upload this template and specify any necessary parameters.
5. Review and create the stack.

## Conclusion

This template provides a simple yet effective way to deploy a customizable EC2 instance in any AWS region. By leveraging CloudFormation's mapping and parameter capabilities, you can ensure that your deployments are both flexible and efficient.