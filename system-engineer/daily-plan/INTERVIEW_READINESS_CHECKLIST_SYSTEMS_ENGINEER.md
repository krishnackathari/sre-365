# Systems Engineer / Systems Reliability Engineer – Interview Readiness Checklist

If you can confidently answer most of the questions below with real examples, tradeoffs, and debugging steps, you are ready to start applying and interviewing for Systems Engineer / Production Engineering roles at Cloudflare/FAANG-tier companies.

---

## 1. Linux & OS Internals
- Explain how Linux schedules processes and what happens during a context switch.  
- How would you debug a process consuming 100% CPU but making no progress?  
- Difference between virtual memory and resident memory (RSS).  
- What does `OOMKilled` mean and how do you prevent it in Kubernetes?  
- What happens when file descriptors are exhausted?  
- How would you trace a hung process using `strace`, `/proc`, or `lsof`?

---

## 2. Networking, DNS, TLS
- Walk through what happens when a user types `https://example.com` in a browser.  
- How does DNS resolution work, and what production failures have you seen or can you imagine?  
- L4 vs L7 load balancing — when would you choose each?  
- Explain the TLS handshake and where TLS termination typically happens.  
- How do you rotate SSL certificates without downtime?  
- How would you debug packet loss or high latency between two services?

---

## 3. Kubernetes (Production Focus)
- What happens when a pod goes into `CrashLoopBackOff` and how do you debug it?  
- When would you use HPA vs VPA, and what are the risks of each?  
- How do readiness and liveness probes differ, and how can misconfigurations cause outages?  
- How do Ingress controllers handle TLS and routing?  
- How do StatefulSets differ from Deployments and when do you use them?  
- How do you safely roll out changes in Kubernetes (canary, blue/green, rolling updates)?

---

## 4. Observability & Incident Response
- Difference between metrics, logs, and traces — when do you use each?  
- What are RED and USE metrics and how do they guide alerting?  
- How do you design alerts that avoid alert fatigue?  
- Walk through how you would debug a production incident from alert → mitigation → root cause.  
- What does a good blameless postmortem include?

---

## 5. Distributed Systems & Reliability
- How do retries cause cascading failures and how do you prevent retry storms?  
- What is backpressure and how do you design for it?  
- Explain circuit breakers and graceful degradation.  
- How do you design systems to avoid single points of failure?  
- Strong vs eventual consistency — when would you choose each?  
- How do you handle partial failures in distributed systems?

---

## 6. AWS & Cloud Infrastructure (Production-Level)
- Design a highly available service on AWS using VPC, ALB/NLB, and multi-AZ deployments.  
- How does autoscaling work on AWS and what can go wrong?  
- How do IAM roles differ from users and why is least privilege important?  
- How do you manage secrets securely on AWS?  
- How do you design disaster recovery (RTO/RPO) for a production service?  
- How do you debug networking issues caused by security groups or NACLs?

---

## 7. System Design (Infra / Platform-Oriented)
- Design a global API platform used by millions of users.  
- How would you design observability for a multi-region system?  
- How do you safely roll out breaking changes?  
- How do you design for graceful degradation when dependencies fail?  
- What tradeoffs would you make between performance, cost, and reliability?

---

## 8. Behavioral & Real-World Scenarios
- Tell me about a time you handled a production incident.  
- Tell me about a time you prevented an outage.  
- Tell me about a failure you caused and what you learned.  
- How do you handle being on-call?  
- How do you prioritize reliability work vs feature delivery?

---

## Self-Assessment Rule

If you can:
- Explain your reasoning clearly  
- Walk through debugging steps  
- Discuss tradeoffs (not just “best practices”)  
- Tie answers back to Linux, networking, Kubernetes, and AWS  

You are ready to start applying and using interviews as practice.

---

**Tip:** You do NOT need perfect answers. Interviewers are evaluating how you think, reason under uncertainty, and debug systems — not whether you memorized documentation.
