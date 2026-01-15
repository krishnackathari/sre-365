# Terraform AWS AMI Creation from EC2 Instance

## Problem
Create an Amazon Machine Image (AMI) from an existing EC2 instance using Terraform.  
The EC2 instance is named `devops-ec2`, and the resulting AMI should be named `devops-ec2-ami`.

---

## Approach
- Provision an EC2 instance with the required AMI, instance type, and security group.
- Use the `aws_ami_from_instance` resource to create an AMI from the EC2 instance.
- Dynamically reference the EC2 instance ID to avoid hardcoding values.
- Ensure the AMI creation happens only after the EC2 instance is successfully created.

---

## Terraform Solution

```hcl
# Provision EC2 instance
resource "aws_instance" "ec2" {
  ami           = "ami-0c101f26f147fa7fd"
  instance_type = "t2.micro"

  vpc_security_group_ids = [
    "sg-8ac365b78ebc526b0"
  ]

  tags = {
    Name = "devops-ec2"
  }
}

# Create AMI from EC2 instance
resource "aws_ami_from_instance" "example" {
  name               = "devops-ec2-ami"
  source_instance_id = aws_instance.ec2.id

  depends_on = [
    aws_instance.ec2
  ]
}

