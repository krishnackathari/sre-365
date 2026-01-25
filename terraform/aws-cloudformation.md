# Terraform CloudFormation Stack – S3 Bucket with Versioning

## Problem
Create an AWS CloudFormation stack using Terraform.  
The stack must be named `devops-stack` and should provision an S3 bucket named `devops-bucket-15943` with **versioning enabled**.

---

## Approach
- Use Terraform to create a CloudFormation stack.
- Define the CloudFormation template inline using `template_body`.
- Create an S3 bucket resource inside the stack.
- Enable versioning on the S3 bucket.

---

## Terraform Solution

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_cloudformation_stack" "devops_stack" {
  name = "devops-stack"

  template_body = <<EOF
AWSTemplateFormatVersion: '2010-09-09'
Description: Create an S3 bucket with versioning enabled

Resources:
  DevOpsBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: devops-bucket-15943
      VersioningConfiguration:
        Status: Enabled
EOF
}

