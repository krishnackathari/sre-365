# Terraform AWS EBS Volume Snapshot Creation

## Problem
The Nautilus DevOps team needs to create automated backups for important data.  
Create a snapshot of an EBS volume named `datacenter-vol` in the `us-east-1` region using Terraform.

### Requirements
- Snapshot name must be `datacenter-vol-ss`
- Description must be `Datacenter Snapshot`
- Ensure the snapshot reaches the `completed` state before submission
- Update the existing `main.tf` file only

---

## Approach
- Create an EBS volume named `datacenter-vol` in the specified availability zone.
- Create an EBS snapshot from the volume.
- Tag the snapshot appropriately for identification.
- Allow Terraform to wait until snapshot creation is complete.

---

## Terraform Solution

```hcl
resource "aws_ebs_volume" "datacenter_vol" {
  availability_zone = "us-east-1a"
  size              = 5
  type              = "gp2"

  tags = {
    Name = "datacenter-vol"
  }
}

resource "aws_ebs_snapshot" "example_snapshot" {
  volume_id   = aws_ebs_volume.datacenter_vol.id
  description = "Datacenter Snapshot"

  tags = {
    Name = "datacenter-vol-ss"
  }
}

