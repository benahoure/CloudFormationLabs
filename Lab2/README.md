# PostgreSQL RDS Deployment using AWS CloudFormation

## Objective
This lab demonstrates how to deploy a PostgreSQL RDS instance using an AWS CloudFormation template. The RDS instance is configured with essential parameters such as database name, instance identifier, and username, while the database password is securely stored and retrieved from the AWS Systems Manager (SSM) Parameter Store. This approach enhances security by avoiding hardcoding of sensitive data within the template.

## Features
- **PostgreSQL RDS Instance**: Deploys a managed PostgreSQL database instance on AWS.
- **Enhanced Monitoring**: Demonstrates how to configure a database with monitoring enabled.
- **SSM Parameter Store Integration**: Securely stores the database password and username in the AWS Systems Manager Parameter Store.
- **Configurable Parameters**: DBInstanceIdentifier, DBName, and MasterUsername are configurable through parameters to customize the deployment.

## CloudFormation Template Breakdown

### AWSTemplateFormatVersion
```yaml
AWSTemplateFormatVersion: 2010-09-09
```
Specifies the version of AWS CloudFormation being used.

### Description
```yaml
Description: >-
  AWS CloudFormation Sample Template for creating an Amazon RDS DB instance with Enhanced Monitoring enabled.
```
Provides a brief description of the template's purpose, which is to create a PostgreSQL RDS instance.

### Parameters
- **DBInstanceIdentifier**: Defines the identifier for the database instance.
  - Default: `mydbinstance`
  - Constraints: Must start with a letter and contain alphanumeric characters only.

- **DBName**: The name of the PostgreSQL database to be created.
  - Default: `bendatabase`
  - Constraints: Must start with a letter and contain only alphanumeric characters.

- **DBUsernameSSMParamaters**: Retrieves the database username securely from the AWS SSM Parameter Store.
  - Default: `/BenDataBase/Username/hidden`

- **DBPasswordWithSSMParamaters**: Retrieves the database password securely from the AWS SSM Parameter Store.
  - Default: `/Ben/Database/password`
  
### Resources

- **MyDB (AWS::RDS::DBInstance)**: Defines the PostgreSQL RDS instance with the following properties:
  - **DBInstanceIdentifier**: Uses the value from `DBInstanceIdentifier` parameter.
  - **DBName**: Uses the value from `DBName` parameter.
  - **DBInstanceClass**: Specifies the instance class (`db.t4g.micro`).
  - **AllocatedStorage**: Sets the allocated storage size (20 GB).
  - **Engine**: Specifies `Postgres` as the database engine.
  - **EngineVersion**: Sets the PostgreSQL engine version (`15.5`).
  - **MasterUsername**: Retrieves the master username securely from SSM.
  - **MasterUserPassword**: Retrieves the master password securely from SSM.

## How to Deploy

1. **Create Parameters in SSM**:
   - Store the database username and password in AWS Systems Manager Parameter Store under the paths:
     - `/BenDataBase/Username/hidden`
     - `/Ben/Database/password`
   Ensure these parameters are of type "String" and have proper permissions set for CloudFormation to retrieve them.

2. **Deploy the CloudFormation Stack**:
   - Go to the AWS Management Console.
   - Navigate to the CloudFormation service.
   - Create a new stack and upload this template.
   - Provide values for any required parameters, or use the default ones specified in the template.

3. **Monitor the Deployment**:
   - Once the stack is created, AWS will provision the PostgreSQL RDS instance.
   - You can monitor the RDS instance in the **RDS** section of the AWS Management Console.

## Security Considerations
- **NoEcho Parameters**: The `DBUsernameSSMParamaters` and `DBPasswordWithSSMParamaters` parameters are marked as `NoEcho`, ensuring that sensitive information such as usernames and passwords are not displayed in the CloudFormation console during or after deployment.
- **SSM Parameter Store**: Storing credentials in the AWS Systems Manager Parameter Store ensures that secrets are securely stored and can be rotated without updating the CloudFormation template.

## Template Configuration

This template allows users to customize key settings like the database name, instance identifier, and credentials, while leveraging AWS services to maintain security and flexibility in deployment.

