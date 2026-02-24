# Days 31–60: Systems Engineer / Systems Reliability Engineer Interview Plan

---

## Day 31
🎯 Interview focus: Explain TLS/SSL handshake and how HTTPS works end-to-end  
📚 Learn: TLS handshake, certificate chain, CA, intermediate certs  
🔬 Deep dive: Symmetric vs asymmetric crypto, where TLS terminates (LB vs ingress vs app)  
🧪 Lab: Generate self-signed cert, configure Nginx with HTTPS locally  
💻 Coding: Python script to validate certificate expiry dates  
🧠 System design: Where do you terminate TLS in a microservices architecture?  
🗣 Story prep: Explain a scenario where incorrect TLS caused outage  

---

## Day 32
🎯 Interview focus: How do you rotate SSL certificates without downtime?  
📚 Learn: Cert rotation strategies, zero-downtime rotation patterns  
🔬 Deep dive: Ingress cert reloads, cert-manager basics  
🧪 Lab: Simulate cert update in Kubernetes ingress  
💻 Coding: Python script to scan Kubernetes secrets for cert expiry  
🧠 System design: Design cert lifecycle management for 1000 services  
🗣 Story prep: Explain how you'd prevent cert expiry incidents  

---

## Day 33
🎯 Interview focus: Secrets management in production systems  
📚 Learn: K8s Secrets, Vault basics, env vars vs files  
🔬 Deep dive: Secret rotation, least privilege, blast radius  
🧪 Lab: Mount secret into pod, rotate and reload app  
💻 Coding: Python script to read secrets securely  
🧠 System design: How would you design secret management for multi-cluster env?  
🗣 Story prep: Example of leaked secret and mitigation steps  

---

## Day 34
🎯 Interview focus: Kubernetes security best practices  
📚 Learn: RBAC, service accounts, PSP/Pod Security Standards  
🔬 Deep dive: Namespace isolation, cluster roles  
🧪 Lab: Create RBAC roles and test access  
💻 Coding: Python script to audit RBAC permissions  
🧠 System design: Design multi-tenant cluster security model  
🗣 Story prep: How to prevent privilege escalation in K8s  

---

## Day 35
🎯 Interview focus: Network security and TLS termination patterns  
📚 Learn: L4 vs L7 LB, mTLS basics  
🔬 Deep dive: TLS offload vs end-to-end encryption  
🧪 Lab: Configure mTLS between two services  
💻 Coding: Python HTTPS client with cert verification  
🧠 System design: Secure service-to-service communication  
🗣 Story prep: Explain tradeoffs of TLS termination at LB  

---

## Day 36
🎯 Interview focus: Kubernetes resource management  
📚 Learn: Requests vs limits  
🔬 Deep dive: OOMKilled, CPU throttling  
🧪 Lab: Trigger OOMKilled and observe behavior  
💻 Coding: Python memory hog script  
🧠 System design: Resource allocation strategy for multi-tenant cluster  
🗣 Story prep: Debugging memory issues in prod  

---

## Day 37
🎯 Interview focus: HPA vs VPA in Kubernetes  
📚 Learn: Horizontal Pod Autoscaler concepts  
🔬 Deep dive: CPU vs custom metrics autoscaling  
🧪 Lab: Setup HPA on a deployment  
💻 Coding: Python app exposing metrics for autoscaling  
🧠 System design: When to use HPA vs VPA  
🗣 Story prep: Autoscaling incident example  

---

## Day 38
🎯 Interview focus: Kubernetes scheduling  
📚 Learn: Node selectors, taints & tolerations  
🔬 Deep dive: Pod placement strategies  
🧪 Lab: Force pods to specific nodes  
💻 Coding: Python script to simulate uneven load  
🧠 System design: Node pools for different workloads  
🗣 Story prep: Scheduling issue causing outage  

---

## Day 39
🎯 Interview focus: Stateful workloads in Kubernetes  
📚 Learn: StatefulSets, PVCs  
🔬 Deep dive: Storage classes  
🧪 Lab: Deploy stateful app with PVC  
💻 Coding: Python script writing to mounted volume  
🧠 System design: Databases in Kubernetes – pros/cons  
🗣 Story prep: Stateful app failure recovery  

---

## Day 40
🎯 Interview focus: Kubernetes networking internals  
📚 Learn: CNI, pod networking model  
🔬 Deep dive: kube-proxy, iptables vs IPVS  
🧪 Lab: Trace traffic between pods  
💻 Coding: Python TCP client/server  
🧠 System design: Network isolation in cluster  
🗣 Story prep: Debugging service connectivity issue  

---

## Day 41
🎯 Interview focus: DNS in distributed systems  
📚 Learn: DNS resolution flow, caching  
🔬 Deep dive: TTL, split-horizon DNS  
🧪 Lab: Setup CoreDNS record override  
💻 Coding: Python DNS lookup tool  
🧠 System design: DNS architecture for global services  
🗣 Story prep: DNS outage story  

---

## Day 42
🎯 Interview focus: Load balancing strategies  
📚 Learn: Round-robin, least connections  
🔬 Deep dive: Health checks  
🧪 Lab: Nginx LB config  
💻 Coding: Python client to simulate LB behavior  
🧠 System design: Global traffic routing  
🗣 Story prep: LB misconfig incident  

---

## Day 43
🎯 Interview focus: Observability fundamentals  
📚 Learn: Metrics vs logs vs traces  
🔬 Deep dive: RED & USE metrics  
🧪 Lab: Expose Prometheus metrics  
💻 Coding: Python app with Prometheus exporter  
🧠 System design: Observability pipeline  
🗣 Story prep: Finding root cause via metrics  

---

## Day 44
🎯 Interview focus: Alerting design  
📚 Learn: Alert fatigue, SLO-based alerts  
🔬 Deep dive: Prometheus Alertmanager  
🧪 Lab: Create alert rules  
💻 Coding: Python script to simulate failure metrics  
🧠 System design: Alerting strategy for platform  
🗣 Story prep: False positive alert incident  

---

## Day 45
🎯 Interview focus: Log aggregation  
📚 Learn: Centralized logging patterns  
🔬 Deep dive: Structured logs  
🧪 Lab: Ship logs to central store  
💻 Coding: Python logging with JSON  
🧠 System design: Logging pipeline architecture  
🗣 Story prep: Debugging via logs  

---

## Day 46
🎯 Interview focus: CI/CD pipelines  
📚 Learn: GitOps, deployment pipelines  
🔬 Deep dive: Canary vs blue/green  
🧪 Lab: Build simple CI pipeline  
💻 Coding: Python script in CI job  
🧠 System design: Safe deployment strategy  
🗣 Story prep: Failed deployment recovery  

---

## Day 47
🎯 Interview focus: Rollbacks and safe releases  
📚 Learn: Feature flags  
🔬 Deep dive: Progressive delivery  
🧪 Lab: Simulate bad deploy and rollback  
💻 Coding: Python feature flag demo  
🧠 System design: Deployment rollback strategy  
🗣 Story prep: Rollback after outage  

---

## Day 48
🎯 Interview focus: Terraform basics  
📚 Learn: Terraform workflow  
🔬 Deep dive: State files  
🧪 Lab: Provision simple infra  
💻 Coding: Python wrapper for terraform  
🧠 System design: IaC for multi-env  
🗣 Story prep: Drift detection story  

---

## Day 49
🎯 Interview focus: Infrastructure drift & config mgmt  
📚 Learn: Drift detection  
🔬 Deep dive: Immutable infra  
🧪 Lab: Change infra manually and detect drift  
💻 Coding: Python drift checker  
🧠 System design: Config consistency  
🗣 Story prep: Config drift outage  

---

## Day 50
🎯 Interview focus: Incident response lifecycle  
📚 Learn: Incident phases  
🔬 Deep dive: Blameless postmortems  
🧪 Lab: Write mock postmortem  
💻 Coding: Python script for incident notes  
🧠 System design: Incident command structure  
🗣 Story prep: Handling Sev-1 outage  

---

## Day 51
🎯 Interview focus: Capacity planning  
📚 Learn: Forecasting traffic  
🔬 Deep dive: Load testing  
🧪 Lab: Stress test app  
💻 Coding: Python load generator  
🧠 System design: Capacity planning model  
🗣 Story prep: Scaling incident  

---

## Day 52
🎯 Interview focus: Kafka basics  
📚 Learn: Kafka architecture  
🔬 Deep dive: Producers/consumers  
🧪 Lab: Produce & consume messages  
💻 Coding: Python Kafka producer  
🧠 System design: Event-driven architecture  
🗣 Story prep: Message backlog incident  

---

## Day 53
🎯 Interview focus: Backpressure handling  
📚 Learn: Queue overflow strategies  
🔬 Deep dive: Rate limiting  
🧪 Lab: Simulate overload  
💻 Coding: Python rate limiter  
🧠 System design: Backpressure mechanisms  
🗣 Story prep: Traffic spike handling  

---

## Day 54
🎯 Interview focus: Caching strategies  
📚 Learn: Redis basics  
🔬 Deep dive: Cache invalidation  
🧪 Lab: Implement simple cache  
💻 Coding: Python Redis client  
🧠 System design: Cache layers  
🗣 Story prep: Cache miss incident  

---

## Day 55
🎯 Interview focus: Distributed system failures  
📚 Learn: Partial failures  
🔬 Deep dive: Circuit breakers  
🧪 Lab: Inject failures  
💻 Coding: Python retry logic  
🧠 System design: Resilience patterns  
🗣 Story prep: Cascading failure story  

---

## Day 56
🎯 Interview focus: Rate limiting & quotas  
📚 Learn: Token bucket vs leaky bucket  
🔬 Deep dive: API gateways  
🧪 Lab: Implement rate limiter  
💻 Coding: Python rate limiter  
🧠 System design: Global rate limiting  
🗣 Story prep: Abuse mitigation  

---

## Day 57
🎯 Interview focus: Multi-region deployments  
📚 Learn: Active-active vs active-passive  
🔬 Deep dive: Data replication  
🧪 Lab: Simulate region failover  
💻 Coding: Python failover logic  
🧠 System design: Multi-region platform  
🗣 Story prep: Regional outage response  

---

## Day 58
🎯 Interview focus: Disaster recovery  
📚 Learn: RTO/RPO  
🔬 Deep dive: Backup strategies  
🧪 Lab: Backup & restore test  
💻 Coding: Python backup script  
🧠 System design: DR architecture  
🗣 Story prep: DR drill scenario  

---

## Day 59
🎯 Interview focus: Performance tuning  
📚 Learn: CPU profiling  
🔬 Deep dive: Latency vs throughput  
🧪 Lab: Profile slow app  
💻 Coding: Python profiler  
🧠 System design: Performance optimization plan  
🗣 Story prep: Latency regression fix  

---

## Day 60
🎯 Interview focus: End-to-end production review  
📚 Learn: Review all systems concepts  
🔬 Deep dive: Tie Linux → K8s → Networking → Observability  
🧪 Lab: Run simulated outage drill  
💻 Coding: Small Python tool to detect system anomalies  
🧠 System design: Walk through entire production architecture  
🗣 Story prep: Full incident narrative (from alert to fix)  

---
