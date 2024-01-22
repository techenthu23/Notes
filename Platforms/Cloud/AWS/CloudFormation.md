# AWS CloudFormation

- [AWS CloudFormation](#aws-cloudformation)
  - [What is AWS CloudFormation (CF)?](#what-is-aws-cloudformation-cf)
  - [Advantages](#advantages)
  - [Monitoring](#monitoring)
  - [Security](#security)
  - [Concepts](#concepts)
    - [Main Elements](#main-elements)
        - [Templates](#templates)
      - [Stack](#stack)
      - [Stack Sets](#stack-sets)
      - [Change Sets](#change-sets)

## What is AWS CloudFormation (CF)?

[AWS CloudFormation](https://aws.amazon.com/cloudformation/) is a free-to-use Infrastructure as a Service (i.e. Pay only for the resources) that helps developers and businesses to easily create and provision AWS infrastructure deployments predictably and repeatedly snd in a orderly way.

AWS CloudFormation enables you to use a template file to create and delete a collection of resources together as a single unit (a stack).

Provides a common language to describe and provision the AWS resources required for your infrastructure
The configuration is defined in the simple JSON or YML file
Understand the cloud dependencies i.e. It will first create a VPC, then subnets and later the resources in that subnet

Offers functionality like -

- Provides a common language to describe and provision the AWS resources required for your infrastructure
- The configuration is defined in the simple JSON or YML file
- Understand the cloud dependencies i.e. It will first create a VPC, then subnets and later the resources in that subnet
- Offers functionality like -
- Automatic rollback on errors (i.e. If the resource creation is failed, it would automatically delete the subnet and the VPC)
- Version control the configuration file

## Advantages

Offers automatic deployment and modification of AWS resources in an orderly and predictable way
Avoids the manual configuration mistakes and configuration drift
Configuration Drift enables you to detect whether a Stack’s actual configuration differs, or has drifted, from its expected configuration
Versioning the environment

## Monitoring

- Since AWS provides effective monitoring for almost each of their services so in here we have the AWS CloudTrail (CT) which provides a detail of the action taken by a user, role, or an AWS service.
- CT captures the API calls for CloudFormation service as events

## Security

- CloudFormation activities such as Create Stack, View Stack templates, or delete stacks can be controlled through IAM
- CloudFormation can use the IAM service role to make calls to the resources in a stack on your behalf
  
## Concepts

### Main Elements

##### Templates

The JSON or the YAML file that consists of the instructions for building the AWS resources in the cloud environment.

A template is a JSON or the YAML file that describes a stack, a collection of AWS resources you want to deploy together as a group. You use the template to define all the AWS resources you want in your stack.

Developers can also use the CloudFormation Designer tool for creating, viewing, or modifying the templates

n the template, you declare the AWS resources you want to create and configure. You declare an object as a name-value pair or a pairing of a name with a set of child objects enclosed. The syntax depends on the format you use. The only required top-level object is the Resources object, which must declare at least one resource.

#### Stack

Stack is a single unit describe by the Template and is created, updated, and deleted as when required.
If the resource creation gets failed, CloudFormation automatically rolls back the Stack and deletes the created resources. If the resource cannot be deleted, it is retained until the stack can be deleted successfully

#### Stack Sets

Allows to roll out the CloudFormation stacks over the multiple AWS accounts and in multiple regions with just a few clicks. We can also specify the key and value pairs (popularly called as tags) during the Stack set create and update operations

#### Change Sets

It is a summary of the changes that are proposed to the Stack and will allow the users to verify the impact that will be propagated to the existing cloud environment


