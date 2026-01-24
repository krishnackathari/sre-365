## Problem
Create a **DynamoDB table** in AWS using Terraform to store user data.

The table must:
- Be named `nautilus-users`
- Use `nautilus_id` as the **primary key** (String)
- Use **PAY_PER_REQUEST** billing mode
- Be created using **Terraform only**
- Be defined in a single `main.tf` file

---

## Approach
- Use the AWS provider to interact with DynamoDB.
- Define an `aws_dynamodb_table` resource.
- Set the table name and billing mode.
- Specify the primary key using `hash_key`.
- Define the key attribute and its type using an `attribute` block.

---

## Terraform Solution

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_dynamodb_table" "nautilus_users" {
  name         = "nautilus-users"
  billing_mode = "PAY_PER_REQUEST"

  # Primary key
  hash_key = "nautilus_id"

  attribute {
    name = "nautilus_id"
    type = "S"
  }
}
```

