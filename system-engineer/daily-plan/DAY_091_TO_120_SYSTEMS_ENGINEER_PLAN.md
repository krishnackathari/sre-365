# Days 91–120: Systems Engineer / Systems Reliability Engineer Interview Plan (AWS + Production System Design + Mock Interviews)

---

## Day 91
🎯 Interview focus: AWS global infrastructure & shared responsibility model  
📚 Learn: Regions, AZs, edge locations, shared responsibility  
🔬 Deep dive: What AWS manages vs what you manage  
🧪 Lab: Explore AWS console (EC2, VPC, IAM)  
💻 Coding: Python boto3 script to list EC2 instances  
🧠 System design: Map Cloudflare-style global infra onto AWS regions  
🗣 Story prep: Explain AWS responsibility in outages  

---

## Day 92
🎯 Interview focus: VPC networking design  
📚 Learn: VPC, subnets, route tables, IGW, NAT GW  
🔬 Deep dive: Public vs private subnets  
🧪 Lab: Create VPC with public/private subnets  
💻 Coding: Python script to enumerate VPC resources  
🧠 System design: Design multi-AZ VPC for HA  
🗣 Story prep: Networking misconfiguration incident  

---

## Day 93
🎯 Interview focus: Load balancing on AWS  
📚 Learn: ALB vs NLB vs ELB  
🔬 Deep dive: L7 vs L4 LB behavior  
🧪 Lab: Put ALB in front of EC2 app  
💻 Coding: Python health check client  
🧠 System design: Global traffic routing using AWS LB  
🗣 Story prep: LB misroute outage  

---

## Day 94
🎯 Interview focus: Autoscaling on AWS  
📚 Learn: ASG, target tracking policies  
🔬 Deep dive: Scale-in vs scale-out risks  
🧪 Lab: Trigger ASG scale event  
💻 Coding: Python load generator  
🧠 System design: Autoscaling strategy for API service  
🗣 Story prep: Bad autoscaling configuration story  

---

## Day 95
🎯 Interview focus: Kubernetes on AWS (EKS)  
📚 Learn: EKS architecture  
🔬 Deep dive: Control plane vs worker nodes  
🧪 Lab: Inspect EKS cluster components  
💻 Coding: Python script to query EKS API  
🧠 System design: Running production K8s on AWS  
🗣 Story prep: EKS outage mitigation  

---

## Day 96
🎯 Interview focus: IAM security best practices  
📚 Learn: IAM users, roles, policies  
🔬 Deep dive: Least privilege & role assumption  
🧪 Lab: Create role and attach minimal policy  
💻 Coding: Python boto3 IAM audit script  
🧠 System design: IAM for multi-team platform  
🗣 Story prep: Credential leak incident  

---

## Day 97
🎯 Interview focus: Secrets on AWS  
📚 Learn: Secrets Manager vs Parameter Store  
🔬 Deep dive: Rotation strategies  
🧪 Lab: Store secret and retrieve from EC2  
💻 Coding: Python script to fetch secret  
🧠 System design: Secrets architecture for microservices  
🗣 Story prep: Secret rotation without downtime  

---

## Day 98
🎯 Interview focus: Observability on AWS  
📚 Learn: CloudWatch metrics/logs  
🔬 Deep dive: Custom metrics  
🧪 Lab: Push custom metric to CloudWatch  
💻 Coding: Python CloudWatch metric sender  
🧠 System design: Observability pipeline on AWS  
🗣 Story prep: Alerting failure incident  

---

## Day 99
🎯 Interview focus: S3 durability & data safety  
📚 Learn: S3 consistency, versioning  
🔬 Deep dive: Data lifecycle policies  
🧪 Lab: Enable versioning and simulate delete  
💻 Coding: Python S3 uploader  
🧠 System design: Durable object storage  
🗣 Story prep: Accidental data deletion recovery  

---

## Day 100
🎯 Interview focus: Disaster recovery on AWS  
📚 Learn: Backup strategies, cross-region replication  
🔬 Deep dive: RTO/RPO tradeoffs  
🧪 Lab: Snapshot EC2/EBS and restore  
💻 Coding: Python backup automation  
🧠 System design: DR architecture for API platform  
🗣 Story prep: DR drill scenario  

---

## Day 101
🎯 Interview focus: Terraform on AWS  
📚 Learn: Terraform AWS provider  
🔬 Deep dive: State backend in S3 + locking  
🧪 Lab: Provision EC2 via Terraform  
💻 Coding: Python wrapper around terraform  
🧠 System design: IaC for multi-env AWS  
🗣 Story prep: Infra drift incident  

---

## Day 102
🎯 Interview focus: Rolling deployments on AWS  
📚 Learn: Blue/green with ALB  
🔬 Deep dive: Canary releases  
🧪 Lab: Simulate blue/green deployment  
💻 Coding: Python deploy script  
🧠 System design: Safe release strategy  
🗣 Story prep: Failed rollout recovery  

---

## Day 103
🎯 Interview focus: Cost awareness for Systems Engineers  
📚 Learn: AWS cost drivers  
🔬 Deep dive: Autoscaling vs overprovisioning  
🧪 Lab: Estimate EC2 cost  
💻 Coding: Python cost calculator  
🧠 System design: Cost-efficient architecture  
🗣 Story prep: Cost blowup incident  

---

## Day 104
🎯 Interview focus: AWS networking failures  
📚 Learn: Security groups vs NACLs  
🔬 Deep dive: Debugging connectivity issues  
🧪 Lab: Break SG rule and debug  
💻 Coding: Python connectivity tester  
🧠 System design: Secure networking model  
🗣 Story prep: Firewall misconfig outage  

---

## Day 105
🎯 Interview focus: Hybrid cloud design  
📚 Learn: On-prem ↔ AWS connectivity  
🔬 Deep dive: VPN vs Direct Connect  
🧪 Lab: Simulate hybrid networking  
💻 Coding: Python health checker  
🧠 System design: Hybrid architecture  
🗣 Story prep: Hybrid connectivity incident  

---

## Day 106
🎯 Interview focus: Message queues on AWS  
📚 Learn: SQS vs SNS  
🔬 Deep dive: DLQs  
🧪 Lab: Build producer/consumer on SQS  
💻 Coding: Python SQS consumer  
🧠 System design: Event-driven AWS architecture  
🗣 Story prep: Message backlog recovery  

---

## Day 107
🎯 Interview focus: Rate limiting at AWS edge  
📚 Learn: API Gateway throttling  
🔬 Deep dive: WAF rate rules  
🧪 Lab: Configure basic rate limit  
💻 Coding: Python API client with throttling  
🧠 System design: Abuse protection system  
🗣 Story prep: DDoS mitigation  

---

## Day 108
🎯 Interview focus: Security incidents on AWS  
📚 Learn: GuardDuty, CloudTrail  
🔬 Deep dive: Forensics basics  
🧪 Lab: Inspect CloudTrail logs  
💻 Coding: Python script to analyze logs  
🧠 System design: Security monitoring pipeline  
🗣 Story prep: Security incident response  

---

## Day 109
🎯 Interview focus: Large-scale architecture review  
📚 Learn: Review all AWS building blocks  
🔬 Deep dive: Map infra to reliability principles  
🧪 Lab: Draw full AWS architecture diagram  
💻 Coding: Python health audit tool  
🧠 System design: Cloudflare-like platform on AWS  
🗣 Story prep: Architecture review narrative  

---

## Day 110
🎯 Interview focus: System design interview (production flavored)  
📚 Learn: Design methodology  
🔬 Deep dive: Requirements, constraints, tradeoffs  
🧪 Lab: Design a global API platform  
💻 Coding: Python skeleton service  
🧠 System design: Full design walkthrough  
🗣 Story prep: Whiteboard explanation  

---

## Day 111
🎯 Interview focus: Debugging complex production outages  
📚 Learn: Triage methodology  
🔬 Deep dive: Narrowing blast radius  
🧪 Lab: Inject multi-layer failures  
💻 Coding: Python diagnostic script  
🧠 System design: Incident command flow  
🗣 Story prep: Outage war story  

---

## Day 112
🎯 Interview focus: Behavioral interview (Systems Engineer)  
📚 Learn: STAR method  
🔬 Deep dive: Reliability ownership stories  
🧪 Lab: Write 3 STAR stories  
💻 Coding: N/A  
🧠 System design: N/A  
🗣 Story prep: Conflict resolution example  

---

## Day 113
🎯 Interview focus: Resume-to-interview mapping  
📚 Learn: Map skills to resume bullets  
🔬 Deep dive: Metrics-driven stories  
🧪 Lab: Rewrite resume bullets  
💻 Coding: N/A  
🧠 System design: N/A  
🗣 Story prep: Impact framing  

---

## Day 114
🎯 Interview focus: Mock interview – Linux + Networking  
📚 Learn: Review Linux/net topics  
🔬 Deep dive: Weak areas  
🧪 Lab: Timed mock interview  
💻 Coding: Small Python debugging task  
🧠 System design: Mini design  
🗣 Story prep: Clear explanation practice  

---

## Day 115
🎯 Interview focus: Mock interview – Kubernetes + AWS  
📚 Learn: Review K8s + AWS  
🔬 Deep dive: Failure scenarios  
🧪 Lab: Timed mock interview  
💻 Coding: Debugging task  
🧠 System design: Design K8s platform  
🗣 Story prep: Operational story  

---

## Day 116
🎯 Interview focus: Mock interview – Observability + Incident Response  
📚 Learn: Review metrics/logs/tracing  
🔬 Deep dive: Alerting strategies  
🧪 Lab: Mock on-call scenario  
💻 Coding: Small monitoring script  
🧠 System design: Observability architecture  
🗣 Story prep: Incident narrative  

---

## Day 117
🎯 Interview focus: Mock interview – System design  
📚 Learn: Design global service  
🔬 Deep dive: Bottlenecks  
🧪 Lab: Whiteboard full system  
💻 Coding: Pseudocode key components  
🧠 System design: End-to-end platform  
🗣 Story prep: Tradeoff explanation  

---

## Day 118
🎯 Interview focus: Weak area remediation  
📚 Learn: Review weakest domain  
🔬 Deep dive: Deep practice  
🧪 Lab: Extra labs in weak area  
💻 Coding: Extra coding drill  
🧠 System design: Re-design weak area  
🗣 Story prep: Improved explanation  

---

## Day 119
🎯 Interview focus: Full mock interview loop  
📚 Learn: Review everything  
🔬 Deep dive: Timing & clarity  
🧪 Lab: 3-round mock interview simulation  
💻 Coding: Debugging + small design  
🧠 System design: Full architecture review  
🗣 Story prep: Concise storytelling  

---

## Day 120
🎯 Interview focus: Final readiness check  
📚 Learn: Review checklist  
🔬 Deep dive: Calm under pressure  
🧪 Lab: Light review only  
💻 Coding: Warm-up script  
🧠 System design: Elevator pitch of platform design  
🗣 Story prep: Final confidence story  

---
