What is AWS IAM?

AWS Identity and Access Management (IAM) is a service that enables you to securely manage access to AWS services and resources. IAM allows you to:

1. Create and manage users and groups.


2. Define roles that AWS services or external entities can assume.


3. Apply permissions using IAM policies to control who can perform specific actions on specific resources.




---

What Does AWS IAM Control?

AWS IAM controls the following aspects of your AWS account:

1. Authentication:

Verifies the identity of users, applications, or services using credentials.

Supports user passwords, access keys, and MFA (Multi-Factor Authentication).



2. Authorization:

Determines what actions are allowed for authenticated entities.

Uses IAM policies to define specific permissions.



3. Resources:

Controls access to AWS resources such as EC2 instances, S3 buckets, DynamoDB tables, etc.

Enforces the principle of least privilege to limit access.



4. Service-Level Permissions:

Manages fine-grained access for AWS services (e.g., full access to EC2 but read-only access to S3).



5. Cross-Account Access:

Enables secure access to resources in another AWS account using roles.



6. Federation and Identity Provider Integration:

Supports SSO (Single Sign-On) and integration with external identity providers (e.g., SAML or OpenID).





---

Terraform Example for IAM Policy

Here’s how you can create and apply an IAM policy using Terraform:

1. Create an IAM Policy

resource "aws_iam_policy" "example_policy" {
  name        = "example-policy"
  description = "A sample policy to grant S3 read-only access"
  policy      = jsonencode({
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Action": "s3:GetObject",
        "Resource": "arn:aws:s3:::example-bucket/*"
      }
    ]
  })
}

2. Attach the Policy to a User

resource "aws_iam_user" "example_user" {
  name = "example-user"
}

resource "aws_iam_user_policy_attachment" "example_attachment" {
  user       = aws_iam_user.example_user.name
  policy_arn = aws_iam_policy.example_policy.arn
}


---

CloudFormation Example for IAM Policy

Here’s how you can create and apply an IAM policy using AWS CloudFormation:

1. Create an IAM Policy

Resources:
  ExamplePolicy:
    Type: AWS::IAM::Policy
    Properties:
      PolicyName: ExamplePolicy
      PolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Action: "s3:GetObject"
            Resource: "arn:aws:s3:::example-bucket/*"
      Roles:
        - !Ref ExampleRole

2. Attach the Policy to a Role

Resources:
  ExampleRole:
    Type: AWS::IAM::Role
    Properties:
      RoleName: ExampleRole
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Principal:
              Service: ec2.amazonaws.com
            Action: "sts:AssumeRole"


---

How to Apply and Deploy the Policies

Terraform:

1. Save the Terraform script in a file (e.g., main.tf).


2. Initialize Terraform:

terraform init


3. Plan and apply the script:

terraform plan
terraform apply



CloudFormation:

1. Save the YAML script in a file (e.g., template.yaml).


2. Deploy the CloudFormation stack:

aws cloudformation create-stack --stack-name example-stack --template-body file://template.yaml




---

Best Practices for IAM

1. Principle of Least Privilege:

Grant only the permissions required for tasks.



2. Use Groups and Roles:

Avoid assigning policies directly to users.



3. Enable MFA:

Enforce MFA for sensitive accounts and roles.



4. Audit Permissions:

Use AWS IAM Access Analyzer to review unused or risky permissions.



5. Rotate Access Keys:

Regularly rotate access keys for security.




By combining IAM with tools like Terraform and CloudFormation, you can manage permissions programmatically, enforce consistency, and improve security.



To manage VPCs, subnets, and associated security controls in AWS, IAM policies play a crucial role. They control who can perform these actions, what actions they can perform, and which resources they can act upon. Below is an outline of the key IAM permissions and policies needed for each task:


---

1. VPC Creation and Management

Actions Required:

ec2:CreateVpc

ec2:DescribeVpcs

ec2:ModifyVpcAttribute

ec2:DeleteVpc


Example IAM Policy:

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:CreateVpc",
        "ec2:DescribeVpcs",
        "ec2:ModifyVpcAttribute",
        "ec2:DeleteVpc"
      ],
      "Resource": "*"
    }
  ]
}


---

2. Subnet Management

Actions Required:

ec2:CreateSubnet

ec2:DescribeSubnets

ec2:ModifySubnetAttribute

ec2:DeleteSubnet


Example IAM Policy:

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:CreateSubnet",
        "ec2:DescribeSubnets",
        "ec2:ModifySubnetAttribute",
        "ec2:DeleteSubnet"
      ],
      "Resource": "*"
    }
  ]
}


---

3. Internet Gateway (IGW) Management

Actions Required:

ec2:CreateInternetGateway

ec2:AttachInternetGateway

ec2:DetachInternetGateway

ec2:DeleteInternetGateway


Example IAM Policy:

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:CreateInternetGateway",
        "ec2:AttachInternetGateway",
        "ec2:DetachInternetGateway",
        "ec2:DeleteInternetGateway"
      ],
      "Resource": "*"
    }
  ]
}


---

4. Route Table Management

Actions Required:

ec2:CreateRouteTable

ec2:AssociateRouteTable

ec2:CreateRoute

ec2:DeleteRouteTable

ec2:ReplaceRouteTableAssociation


Example IAM Policy:

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:CreateRouteTable",
        "ec2:AssociateRouteTable",
        "ec2:CreateRoute",
        "ec2:DeleteRouteTable",
        "ec2:ReplaceRouteTableAssociation"
      ],
      "Resource": "*"
    }
  ]
}


---

5. Security Group Management

Actions Required:

ec2:CreateSecurityGroup

ec2:AuthorizeSecurityGroupIngress

ec2:RevokeSecurityGroupIngress

ec2:AuthorizeSecurityGroupEgress

ec2:RevokeSecurityGroupEgress

ec2:DeleteSecurityGroup


Example IAM Policy:

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:CreateSecurityGroup",
        "ec2:AuthorizeSecurityGroupIngress",
        "ec2:RevokeSecurityGroupIngress",
        "ec2:AuthorizeSecurityGroupEgress",
        "ec2:RevokeSecurityGroupEgress",
        "ec2:DeleteSecurityGroup"
      ],
      "Resource": "*"
    }
  ]
}


---

6. NACL Management

Actions Required:

ec2:CreateNetworkAcl

ec2:CreateNetworkAclEntry

ec2:ReplaceNetworkAclEntry

ec2:DeleteNetworkAcl

ec2:DeleteNetworkAclEntry


Example IAM Policy:

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:CreateNetworkAcl",
        "ec2:CreateNetworkAclEntry",
        "ec2:ReplaceNetworkAclEntry",
        "ec2:DeleteNetworkAcl",
        "ec2:DeleteNetworkAclEntry"
      ],
      "Resource": "*"
    }
  ]
}


---

7. NAT Gateway Management

Actions Required:

ec2:CreateNatGateway

ec2:DescribeNatGateways

ec2:DeleteNatGateway


Example IAM Policy:

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:CreateNatGateway",
        "ec2:DescribeNatGateways",
        "ec2:DeleteNatGateway"
      ],
      "Resource": "*"
    }
  ]
}


---

8. VPC Endpoint Management

Actions Required:

ec2:CreateVpcEndpoint

ec2:ModifyVpcEndpoint

ec2:DeleteVpcEndpoint

ec2:DescribeVpcEndpoints


Example IAM Policy:

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:CreateVpcEndpoint",
        "ec2:ModifyVpcEndpoint",
        "ec2:DeleteVpcEndpoint",
        "ec2:DescribeVpcEndpoints"
      ],
      "Resource": "*"
    }
  ]
}


---

9. Monitoring and Logging

Actions Required:

logs:CreateLogGroup

logs:CreateLogStream

logs:PutLogEvents

ec2:CreateFlowLogs

ec2:DeleteFlowLogs

ec2:DescribeFlowLogs


Example IAM Policy:

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents",
        "ec2:CreateFlowLogs",
        "ec2:DeleteFlowLogs",
        "ec2:DescribeFlowLogs"
      ],
      "Resource": "*"
    }
  ]
}


---

10. General Permissions for Network Management

Actions Required:

ec2:Describe* (e.g., ec2:DescribeVpcs, ec2:DescribeSecurityGroups, etc.).

These permissions allow read-only access to all resources for auditing or troubleshooting.


Example IAM Policy:

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:Describe*",
      "Resource": "*"
    }
  ]
}


---

Fine-Grained Resource Permissions

For additional security, restrict the "Resource" field to specific ARN patterns:

VPC: "arn:aws:ec2:region:account-id:vpc/vpc-id"

Subnet: "arn:aws:ec2:region:account-id:subnet/subnet-id"

Security Group: "arn:aws:ec2:region:account-id:security-group/sg-id"


Example:

"Resource": "arn:aws:ec2:us-east-1:123456789012:vpc/vpc-0abcd1234efgh5678"


---

By applying these IAM policies, you can grant granular permissions to users, groups, or roles while adhering to the principle of least privilege.

