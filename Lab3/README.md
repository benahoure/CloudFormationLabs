# Windows EC2 Instance Deployment using AWS CloudFormation

## Objective
This lab demonstrates how to deploy a Windows EC2 instance using an AWS CloudFormation template. Instead of hardcoding the Amazon Machine Image (AMI) ID, the template dynamically retrieves the latest Windows AMI ID from the AWS Systems Manager (SSM) Parameter Store. This ensures that the deployment uses up-to-date Windows AMIs without manual intervention.

## Features
- **Dynamic Windows AMI Selection**: The latest Windows Server 2016 AMI ID is fetched from the SSM Parameter Store, eliminating the need to hardcode the AMI ID.
- **Configurable Parameters**: Allows users to specify a VPC, Subnet, EC2 KeyPair, instance type, and more during deployment.
- **Security**: Creates a security group with inbound rules to allow RDP (Remote Desktop Protocol) access via port 3389.

## CloudFormation Template Breakdown

### Parameters
The template uses several parameters to make the deployment customizable and flexible:

- **VpcId**: The ID of the VPC where the EC2 instance will be launched.
  - Type: `AWS::EC2::VPC::Id`
  - User selects an existing VPC during deployment.

- **PublicSubnetId**: The ID of a public subnet within the specified VPC.
  - Type: `AWS::EC2::Subnet::Id`
  - User selects an existing subnet during deployment.

- **KeyName**: The name of an existing EC2 KeyPair for SSH access.
  - Type: `AWS::EC2::KeyPair::KeyName`
  
- **SSMParamaterWindowAMIId**: The name of an SSM Parameter that holds the latest Windows Server 2016 AMI ID.
  - Type: `AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>`
  - Default: `/aws/service/ami-windows-latest/EC2LaunchV2-Windows_Server-2016-English-Full-Base`

- **InstanceTypeParameter**: Specifies the EC2 instance type for the deployment.
  - Default: `t2.micro`
  - Allowed Values: `t2.micro`, `t3.medium`, `t3.large`, `m1.small`, `m1.large`, `m2.2xlarge`

### Resources

#### **InstanceSecurityGroup (AWS::EC2::SecurityGroup)**
Creates a security group that allows inbound RDP (Remote Desktop Protocol) traffic on port 3389 from any IP address (`0.0.0.0/0`).

- **VpcId**: References the `VpcId` parameter for VPC association.
- **SecurityGroupIngress**: Defines the inbound rule allowing traffic on port 3389 for RDP access.

#### **WindowEc2Instance (AWS::EC2::Instance)**
Launches a Windows EC2 instance with the following properties:

- **KeyName**: Uses the KeyPair specified by the `KeyName` parameter.
- **ImageId**: Dynamically retrieves the latest Windows AMI ID from SSM Parameter Store using the `SSMParamaterWindowAMIId` parameter.
- **InstanceType**: Uses the instance type defined by the `InstanceTypeParameter` parameter.
- **NetworkInterfaces**: 
  - Associates a public IP address.
  - Uses the public subnet specified by the `PublicSubnetId` parameter.
  - Associates the security group created in the template.
- **Tags**: Tags the instance with a `Name` of `Benwebserver` and `Department` set to `Finance`.

### How to Deploy

1. **Create Parameters in AWS**:
   - Ensure the `SSMParamaterWindowAMIId` is set to the correct path for the latest Windows AMI (`/aws/service/ami-windows-latest/EC2LaunchV2-Windows_Server-2016-English-Full-Base`).
   
2. **Deploy the CloudFormation Stack**:
   - Go to the AWS Management Console.
   - Navigate to the CloudFormation service.
   - Create a new stack and upload this template.
   - Provide values for the `VpcId`, `PublicSubnetId`, and `KeyName` parameters.

3. **Access the Instance**:
   - Once the deployment completes, you can connect to the Windows instance via RDP using the public IP address and the key pair specified during deployment.

### Security Considerations
- The security group allows inbound RDP access on port 3389 from any IP address (`0.0.0.0/0`). In a production environment, consider restricting access to a specific IP range for better security.
  
### Template Configuration

This CloudFormation template allows you to quickly launch a Windows EC2 instance with configurable VPC, subnet, and instance type, while dynamically fetching the latest Windows AMI ID from the AWS SSM Parameter Store. The use of parameters makes the deployment flexible and reusable across different environments.