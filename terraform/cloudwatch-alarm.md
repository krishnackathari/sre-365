# Terraform AWS CloudWatch Alarm Creation

## Problem
The Nautilus DevOps team is setting up monitoring in their AWS account.  
As part of this setup, a CloudWatch alarm needs to be created to monitor CPU utilization.

### Requirements
- Create a CloudWatch alarm named `xfusion-alarm`
- Monitor EC2 CPU utilization
- Trigger the alarm when CPU utilization exceeds **80%**
- Set the evaluation period to **5 minutes**
- Use a **single evaluation period**
- Implement the entire configuration using **Terraform**
- Use only the `main.tf` file

---

## Approach
- Define an AWS CloudWatch metric alarm using Terraform.
- Configure the alarm to monitor the `CPUUtilization` metric under the `AWS/EC2` namespace.
- Set the threshold and evaluation criteria as specified.
- Allow Terraform to manage the alarm lifecycle.

---

## Terraform Solution

```hcl
resource "aws_cloudwatch_metric_alarm" "xfusion_alarm" {
  alarm_name          = "xfusion-alarm"
  comparison_operator = "GreaterThanOrEqualToThreshold"
  evaluation_periods  = 1
  metric_name         = "CPUUtilization"
  namespace           = "AWS/EC2"
  period              = 300
  statistic           = "Average"
  threshold           = 80
  alarm_description   = "This metric monitors EC2 CPU utilization"

  insufficient_data_actions = []
}

