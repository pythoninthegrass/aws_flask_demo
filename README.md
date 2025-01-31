# aws_flask_demo

> In this post, we will demonstrate how to build and deploy an API running in a microservice architecture. 
> 
> The project we will create addresses how to build and deploy an API to the AWS Cloud. Specifically, we will deploy a Python Flask REST API that will allow users to post their favorite artists and songs from the 90’s to DynamoDB. 
> 
> We will containerize our Flask application and deploy it to Elastic Container Service (ECS). In addition to explaining how to configure an API, we will cover how to automate the deployment of AWS Services using Terraform. We will also walk through some basic testing of the API we create using SOAP API. 

# AWS Services Used

Let’s review the AWS services we are deploying with this project.

**VPC** - [Amazon Virtual Private Cloud](https://aws.amazon.com/vpc/?vpc-blogs.sort-by=item.additionalFields.createdDate&vpc-blogs.sort-order=desc) is a service that lets you launch AWS resources in a logically isolated virtual network that you define.  Terraform creates a VPC containing an Internet gateway, two public subnets, two private subnets, routes, and a NAT gateway.  This allows for a secure networking implementation for the service environment.    

**EC2** - [Amazon Elastic Compute Cloud](https://aws.amazon.com/ec2/?ec2-whats-new.sort-by=item.additionalFields.postDateTime&ec2-whats-new.sort-order=desc) is a web service that provides secure, resizable compute capacity in the cloud.  ECS is used to provide instances running in an autoscaling group (ASG) for the ECS cluster.  This allows the cluster to respond to scaling rulesets designed to allow the cluster to ‘grow’ or ‘shrink’ based on workload demands.

**ECS** - [Amazon Elastic Container Service](https://aws.amazon.com/ecs/?whats-new-cards.sort-by=item.additionalFields.postDateTime&whats-new-cards.sort-order=desc&ecs-blogs.sort-by=item.additionalFields.createdDate&ecs-blogs.sort-order=desc) allows you to easily run, scale, and secure Docker container applications on AWS.  ECS is used to provide an ECS cluster to run the service.  The service is created and managed through an ECS task definition.  The task definition describes the service configuration for the containers hosting the service.  

**ECR** - [Amazon Elastic Container Registry](https://aws.amazon.com/ecr/) is an AWS managed container image registry service that is secure, scalable, and reliable.  ECR is used to store the container image pushed from Docker.  The ECS task definition references this image URL to deliver the service.

**ALB** - [Application Load Balancer](https://aws.amazon.com/elasticloadbalancing/application-load-balancer/) is an Elastic Load Balancer (ELB) which provides layer 7 content based traffic routing to targets in AWS.  The ALB is configured through Terraform to target the ASG and provides health monitoring for the service endpoint.

**DynamoDB** - [Amazon DynamoDB](https://aws.amazon.com/dynamodb/) is a key-value and document database that delivers single-digit millisecond performance at any scale.  A DynamoDB table is created by Terraform as a backend for the delivered API.  API requests can read or write to the DynamoDB table to illustrate an application data flow and operational functionality for a running service.

**CloudWatch** - [Amazon CloudWatch](https://aws.amazon.com/cloudwatch/) is a monitoring and observability service built for DevOps engineers, developers, site reliability engineers (SREs), and IT managers to provide data and actionable insights to monitor your applications, respond to system-wide performance changes, optimize resource utilization, and get a unified view of operational health.  Scaling rulesets provisioned by Terraform are referenced for autoscaling purposes.  Alert metrics provide threshold based triggers to scale the autoscaling groups as needed to meet workload demands. 

# How To Use?

To deploy this project, follow the step by step instructions found here.

## Terraform ECS Cluster with Autoscaling Group

This Terraform template is designed to define a set of specific modules that will perform the following tasks:

- Define the desired state configuration for security, resources, and configurations for delivering defined elements using Infrastructure as Code concepts
- Separate security configurations, cluster configurations, and bootstrapping processes into source control managed definitions making them reusable, defined, and flexible
- Provide a functional process whereby an ECS cluster with these defined dependencies can be effectively leveraged and quickly delivered

## Dependencies

This module has dependencies or requirements in order to successfully deliver the desired state.  These dependencies include the following:

- An AWS account is required
- An execution role or IAM key is required for authentication with the AWS account
- An Elastic Container Repository containing the reference image

## Summary

This module is designed to provide a comprehensive deployment solution for ECS including the following component configurations:

| Component                | Description                                            |
|--------------------------|--------------------------------------------------------|
| Virtual Private Cloud    | Public and private subnets, routes, and a NAT gateway  |
| Elastic Compute Cloud    | Autoscaling configuration                               |
| Elastic Container Service| Cluster configuration and task definition                |
| Application Load Balancer| Load balancer configuration                             |
| Amazon DynamoDB          | Table configuration                                     |
| Amazon CloudWatch        | Alert metrics defined                                   |

**Please read the rest of this document prior to leveraging this Terraform template for platform delivery.**

# Index

[Usage](#usage)  
[Variables](#variables)  

## Provider Dependencies

This Terraform code was tested on Terraform 0.14.7 using AWS provider 3.30.0.    

## Usage

### Pre-Deployment Testing and Validation

Follow these steps to validate and deploy the Terraform configuration:

1. Format and validate the Terraform code
2. Create and review the execution plan 
3. Apply the configuration
4. Clean up resources when done

```bash
# Initialize the Terraform configuration
terraform init

# Format and validate
terraform fmt -recursive
terraform validate

# Plan and deploy
terraform plan
terraform apply

# Optional: Use with var files
terraform plan -var-file=env.tfvars
terraform apply -auto-approve -var-file=env.tfvars

# Cleanup
terraform destroy
```

#### Variables

| Variable           | Description                                                                                              | Type    | Default                |
|--------------------|----------------------------------------------------------------------------------------------------------|---------|------------------------|
| vpc_cidr           | The CIDR block for the VPC                                                                               | string  | 10.0.0.0/16            |
| vpc_dns_support    | Should DNS support be enabled for the VPC?                                                               | boolean | true                   |
| vpc_dns_hostnames  | Should DNS hostnames support be enabled for the VPC?                                                     | boolean | true                   |
| Availability_zone  | A list of allowed availability zones                                                                     | string  | us-east-1a, us-east-1c |
| map_public_ip      | Specify true to indicate that instances launched into the subnet should be assigned a public IP address  | boolean | true                   |
| public_cidr_1      | The CIDR block for the first public subnet                                                                | string  | 10.0.1.0/24            |
| public_cidr_2      | The CIDR block for the second public subnet                                                              | string  | 10.0.2.0/24            |
| private_cidr_1     | The CIDR block for the first private subnet                                                               | string  | 10.0.3.0/24            |
| private_cidr_2     | The CIDR block for the second private subnet                                                             | string  | 10.0.4.0/24            |
| desired_capacity   | Number of instances to launch in the ECS cluster                                                         | number  | 1                      |
| maximum_capacity   | Maximum number of instances that can be launched in the ECS cluster                                      | number  | 5                      |
| instance_type      | EC2 instance type for ECS launch configuration                                                            | string  | m5.large               |
| service_name       | The name for the ECS service                                                                             | string  | -                      |
| ecs_image_url      | The desired ECR image url                                                                                | string  | -                      |
| dynamo_table_name  | The desired DynamoDB table name                                                                          | string  | musicTable             |

### License

This library is licensed under the MIT-0 License. See the [LICENSE](LICENSE) file.
