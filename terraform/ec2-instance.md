# Terraform EC2 Instance with Key Pair (Nautilus)

## Problem
Create an EC2 instance in AWS using Terraform as part of the Nautilus DevOps cloud migration.

The solution must:
- Create an RSA key pair named `nautilus-kp`
- Register the key pair in AWS
- Launch an EC2 instance using Amazon Linux AMI `ami-0c101f26f147fa7fd`
- Use instance type `t2.micro`
- Tag the instance with `Name = nautilus-ec2`
- Rely on the default security group

---

## Approach
- Generate an RSA key pair using Terraform.
- Upload the public key to AWS as an EC2 key pair.
- Launch an EC2 instance using the generated key pair.
- Allow AWS to automatically attach the default security group.

This keeps the configuration minimal and aligned with lab requirements.

---

## Terraform Solution

```hcl
resource "tls_private_key" "nautilus_kp" {
  algorithm = "RSA"
  rsa_bits  = 2048
}

resource "aws_key_pair" "nautilus_kp" {
  key_name   = "nautilus-kp"
  public_key = tls_private_key.nautilus_kp.public_key_openssh
}

resource "aws_instance" "nautilus_ec2" {
  ami           = "ami-0c101f26f147fa7fd"
  instance_type = "t2.micro"
  key_name      = aws_key_pair.nautilus_kp.key_name

  tags = {
    Name = "nautilus-ec2"
  }
}

