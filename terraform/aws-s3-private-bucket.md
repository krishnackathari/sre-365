## Problem
Create an S3 bucket in AWS using Terraform that is **private** and blocks **all public access**.

The bucket must:
- Be created in the `us-east-1` region
- Have public access completely blocked
- Use Terraform only

---

## Approach
- Configure the AWS provider with the required region.
- Create an S3 bucket with the specified name.
- Apply a public access block configuration to ensure the bucket remains private.
- Enable all block public access settings to prevent accidental exposure.

---

## Terraform Solution

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_s3_bucket" "xfusion_bucket" {
  bucket = "xfusion-s3-22933"

  tags = {
    Name        = "xfusion-s3-22933"
    Environment = "Dev"
  }
}

resource "aws_s3_bucket_public_access_block" "xfusion_block_public" {
  bucket = aws_s3_bucket.xfusion_bucket.id

  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}

