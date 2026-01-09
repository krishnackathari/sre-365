# Terraform AWS Security Group Creation

## Problem
Create a security group in the default VPC using Terraform with inbound access for HTTP and SSH.

---

## Approach
- Fetch the default VPC.
- Create a security group with the required name and description.
- Add inbound rules for HTTP (80) and SSH (22) from all IP addresses.

---

## Terraform Solution

```hcl
data "aws_vpc" "default" {
  default = true
}

resource "aws_security_group" "xfusion_sg" {
  name        = "xfusion-sg"
  description = "Security group for Nautilus App Servers"
  vpc_id      = data.aws_vpc.default.id
}

resource "aws_vpc_security_group_ingress_rule" "http_ingress" {
  security_group_id = aws_security_group.xfusion_sg.id
  cidr_ipv4         = "0.0.0.0/0"
  from_port         = 80
  to_port           = 80
  ip_protocol       = "tcp"
}

resource "aws_vpc_security_group_ingress_rule" "ssh_ingress" {
  security_group_id = aws_security_gro

