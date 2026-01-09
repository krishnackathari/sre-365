# Terraform AWS Key Pair Creation

## Problem
Create an RSA key pair using Terraform and save the private key locally.  
The key pair must be registered in AWS with the name `datacenter-kp`.

---

## Approach
- Generate an RSA key pair using Terraform.
- Save the private key to a local `.pem` file.
- Upload the public key to AWS as an EC2 key pair.

---

## Terraform Solution

```hcl
resource "tls_private_key" "datacenter_kp" {
  algorithm = "RSA"
}

resource "local_file" "datacenter_kp" {
  filename = "/home/bob/datacenter-kp.pem"
  content  = tls_private_key.datacenter_kp.private_key_pem
}

resource "aws_key_pair" "datacenter_kp" {
  key_name   = "datacenter-kp"
  public_key = tls_private_key.datacenter_kp.public_key_openssh
}

