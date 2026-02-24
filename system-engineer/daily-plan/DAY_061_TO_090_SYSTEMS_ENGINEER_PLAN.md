# Days 61–90: Systems Engineer / Systems Reliability Engineer Interview Plan (Advanced Production + System Design)

---

## Day 61
🎯 Interview focus: Designing highly available systems (HA vs FT)  
📚 Learn: High availability vs fault tolerance vs resilience  
🔬 Deep dive: Single points of failure in microservices  
🧪 Lab: Break one dependency in your local cluster and observe impact  
💻 Coding: Python script to simulate health checks  
🧠 System design: Design HA architecture for a public API  
🗣 Story prep: Example of eliminating SPOF  

---

## Day 62
🎯 Interview focus: Global traffic management  
📚 Learn: Geo-DNS, Anycast, latency-based routing  
🔬 Deep dive: Failover strategies across regions  
🧪 Lab: Simulate region routing with DNS overrides  
💻 Coding: Python script to test latency to multiple endpoints  
🧠 System design: Global LB for worldwide users  
🗣 Story prep: Handling regional failover  

---

## Day 63
🎯 Interview focus: Service discovery in distributed systems  
📚 Learn: DNS-based vs service mesh discovery  
🔬 Deep dive: Consul basics, Kubernetes service discovery  
🧪 Lab: Inspect service discovery in K8s  
💻 Coding: Python client using service discovery  
🧠 System design: Service discovery for 1000 microservices  
🗣 Story prep: Debugging service resolution issues  

---

## Day 64
🎯 Interview focus: API reliability & contracts  
📚 Learn: API versioning, backward compatibility  
🔬 Deep dive: Schema evolution, breaking changes  
🧪 Lab: Deploy v1/v2 APIs and test compatibility  
💻 Coding: Python API client with retries  
🧠 System design: API gateway architecture  
🗣 Story prep: Handling breaking API change  

---

## Day 65
🎯 Interview focus: Timeouts, retries, idempotency  
📚 Learn: Retry storms, exponential backoff  
🔬 Deep dive: Idempotent operations in APIs  
🧪 Lab: Simulate flaky upstream service  
💻 Coding: Python retry wrapper  
🧠 System design: Safe retry policies  
🗣 Story prep: Retry storm incident  

---

## Day 66
🎯 Interview focus: Queue-based architectures  
📚 Learn: Async processing patterns  
🔬 Deep dive: Exactly-once vs at-least-once semantics  
🧪 Lab: Build producer/consumer workflow  
💻 Coding: Python worker processing queue  
🧠 System design: Decoupling services with queues  
🗣 Story prep: Backlog growth scenario  

---

## Day 67
🎯 Interview focus: Data consistency models  
📚 Learn: Strong vs eventual consistency  
🔬 Deep dive: CAP theorem in practice  
🧪 Lab: Simulate stale reads  
💻 Coding: Python client with consistency checks  
🧠 System design: Choosing consistency for user data  
🗣 Story prep: Consistency tradeoff story  

---

## Day 68
🎯 Interview focus: Distributed locks & leader election  
📚 Learn: Leader election patterns  
🔬 Deep dive: Zookeeper/etcd basics  
🧪 Lab: Observe leader election in K8s  
💻 Coding: Python lock using Redis  
🧠 System design: Leader-based system design  
🗣 Story prep: Split-brain incident  

---

## Day 69
🎯 Interview focus: Schema migrations in production  
📚 Learn: Online schema changes  
🔬 Deep dive: Blue/green schema migrations  
🧪 Lab: Apply DB schema change safely  
💻 Coding: Python migration script  
🧠 System design: Zero-downtime migrations  
🗣 Story prep: Migration gone wrong  

---

## Day 70
🎯 Interview focus: Distributed tracing  
📚 Learn: OpenTelemetry basics  
🔬 Deep dive: Trace context propagation  
🧪 Lab: Add tracing to service  
💻 Coding: Python service with tracing  
🧠 System design: End-to-end tracing pipeline  
🗣 Story prep: Root cause via trace  

---

## Day 71
🎯 Interview focus: eBPF & low-level observability  
📚 Learn: What eBPF is used for  
🔬 Deep dive: strace vs eBPF  
🧪 Lab: Trace syscall activity  
💻 Coding: Python wrapper invoking strace  
🧠 System design: Kernel-level observability  
🗣 Story prep: Debugging kernel-level issue  

---

## Day 72
🎯 Interview focus: Performance bottleneck analysis  
📚 Learn: Profiling techniques  
🔬 Deep dive: Flame graphs  
🧪 Lab: Profile CPU-heavy workload  
💻 Coding: Python CPU profiler  
🧠 System design: Performance monitoring strategy  
🗣 Story prep: Performance regression incident  

---

## Day 73
🎯 Interview focus: Memory leaks in production  
📚 Learn: Heap vs stack memory  
🔬 Deep dive: Leak detection strategies  
🧪 Lab: Simulate memory leak  
💻 Coding: Python script leaking memory  
🧠 System design: Memory monitoring approach  
🗣 Story prep: Memory leak outage  

---

## Day 74
🎯 Interview focus: Network latency debugging  
📚 Learn: Latency sources  
🔬 Deep dive: Packet loss vs jitter  
🧪 Lab: Introduce latency via tc  
💻 Coding: Python latency measurement tool  
🧠 System design: Low-latency network design  
🗣 Story prep: Latency spike investigation  

---

## Day 75
🎯 Interview focus: Graceful degradation  
📚 Learn: Feature flags for degradation  
🔬 Deep dive: Partial outages  
🧪 Lab: Disable downstream dependency gracefully  
💻 Coding: Python fallback logic  
🧠 System design: Graceful degradation strategy  
🗣 Story prep: Partial outage handling  

---

## Day 76
🎯 Interview focus: Bulkheads & isolation  
📚 Learn: Bulkhead pattern  
🔬 Deep dive: Resource isolation  
🧪 Lab: Constrain one service’s resources  
💻 Coding: Python concurrency limiter  
🧠 System design: Isolate noisy neighbors  
🗣 Story prep: Noisy neighbor issue  

---

## Day 77
🎯 Interview focus: Chaos engineering  
📚 Learn: Chaos testing principles  
🔬 Deep dive: Failure injection strategies  
🧪 Lab: Kill pods randomly  
💻 Coding: Python chaos injector  
🧠 System design: Chaos testing plan  
🗣 Story prep: Chaos test findings  

---

## Day 78
🎯 Interview focus: Production readiness reviews  
📚 Learn: Launch checklists  
🔬 Deep dive: Reliability gates  
🧪 Lab: Create PRR checklist  
💻 Coding: Python script to validate configs  
🧠 System design: Release readiness process  
🗣 Story prep: Preventing bad launch  

---

## Day 79
🎯 Interview focus: On-call best practices  
📚 Learn: On-call rotations  
🔬 Deep dive: Runbooks  
🧪 Lab: Write runbook for outage  
💻 Coding: Python CLI tool for runbook access  
🧠 System design: On-call workflow  
🗣 Story prep: Handling on-call incident  

---

## Day 80
🎯 Interview focus: Blameless postmortems  
📚 Learn: Postmortem structure  
🔬 Deep dive: Action items vs root cause  
🧪 Lab: Write full postmortem  
💻 Coding: Python template generator  
🧠 System design: Continuous improvement loop  
🗣 Story prep: Postmortem impact story  

---

## Day 81
🎯 Interview focus: Platform abstractions  
📚 Learn: Internal developer platforms  
🔬 Deep dive: Golden paths  
🧪 Lab: Create simple platform template  
💻 Coding: Python CLI scaffolding tool  
🧠 System design: Platform architecture  
🗣 Story prep: Improving developer velocity  

---

## Day 82
🎯 Interview focus: API rate limiting at scale  
📚 Learn: Distributed rate limiting  
🔬 Deep dive: Centralized vs edge enforcement  
🧪 Lab: Implement rate limiting proxy  
💻 Coding: Python rate limit service  
🧠 System design: Global rate limiting system  
🗣 Story prep: Abuse traffic handling  

---

## Day 83
🎯 Interview focus: Edge computing fundamentals  
📚 Learn: Edge vs centralized compute  
🔬 Deep dive: CDN edge processing  
🧪 Lab: Deploy simple edge function (simulated)  
💻 Coding: Python function for edge logic  
🧠 System design: Edge processing pipeline  
🗣 Story prep: Latency reduction story  

---

## Day 84
🎯 Interview focus: Observability for edge systems  
📚 Learn: Edge metrics challenges  
🔬 Deep dive: Sampling strategies  
🧪 Lab: Collect metrics from edge nodes  
💻 Coding: Python metric aggregator  
🧠 System design: Edge observability architecture  
🗣 Story prep: Debugging edge outage  

---

## Day 85
🎯 Interview focus: Distributed config management  
📚 Learn: Dynamic config reloads  
🔬 Deep dive: Consul/Vault config patterns  
🧪 Lab: Reload config without restart  
💻 Coding: Python config watcher  
🧠 System design: Config distribution system  
🗣 Story prep: Bad config rollout incident  

---

## Day 86
🎯 Interview focus: Data pipelines reliability  
📚 Learn: Batch vs stream processing  
🔬 Deep dive: Backpressure in pipelines  
🧪 Lab: Simulate slow consumer  
💻 Coding: Python streaming pipeline  
🧠 System design: Reliable data pipeline  
🗣 Story prep: Data backlog incident  

---

## Day 87
🎯 Interview focus: Dependency management  
📚 Learn: Third-party dependency risks  
🔬 Deep dive: Dependency isolation  
🧪 Lab: Simulate third-party outage  
💻 Coding: Python circuit breaker for API calls  
🧠 System design: Third-party failover strategy  
🗣 Story prep: Vendor outage mitigation  

---

## Day 88
🎯 Interview focus: Multi-tenant system design  
📚 Learn: Tenant isolation  
🔬 Deep dive: Noisy neighbor problems  
🧪 Lab: Simulate tenant load imbalance  
💻 Coding: Python tenant-aware routing  
🧠 System design: Multi-tenant architecture  
🗣 Story prep: Tenant isolation failure  

---

## Day 89
🎯 Interview focus: Reliability testing strategies  
📚 Learn: Load, stress, soak testing  
🔬 Deep dive: Test environments  
🧪 Lab: Run stress test  
💻 Coding: Python load generator  
🧠 System design: Reliability testing pipeline  
🗣 Story prep: Catching prod bug in test  

---

## Day 90
🎯 Interview focus: End-to-end Systems Engineer mock interview  
📚 Learn: Review all core systems topics  
🔬 Deep dive: Connect Linux → Networking → K8s → Observability → System Design  
🧪 Lab: Full incident simulation drill  
💻 Coding: Small Python tool to detect anomalies  
🧠 System design: Walk through complete Cloudflare-style platform design  
🗣 Story prep: Full narrative of production outage handling  

---
