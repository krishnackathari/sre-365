# SRE Personal Index (180-Day Prep)

This index maps key SRE / Platform / DevOps topics to the day numbers in my 180-day plan.
Use this to quickly jump to the right notes before interviews or when debugging real systems.

---

## Linux & OS Fundamentals
- Linux process model → Day 1
- Process lifecycle, zombies, /proc → Day 1
- CPU scheduling basics → Day 2
- Memory management, OOM, swap → Day 2
- File descriptors, I/O, disk basics → Day 3
- Networking in Linux (ports, sockets) → Day 4
- Systemd, services, logs → Day 5
- OS-level debugging (ps, top, strace) → Days 1–5

---

## Networking (Foundations for SRE)
- DNS (recursive vs authoritative resolvers) → Day 6
- TCP handshake, TLS basics → Day 7
- Load balancing concepts → Day 8
- L4 vs L7 proxies → Day 9
- NAT, routing basics → Day 10
- Debugging network issues → Days 6–10

---

## Kubernetes Core
- Kubernetes architecture (API server, etcd, scheduler) → Day 11
- Pods, Deployments, ReplicaSets → Day 12
- Services, ClusterIP, NodePort, LoadBalancer → Day 13
- Ingress controllers → Day 14
- CrashLoopBackOff debugging → Day 15
- Resource requests/limits → Day 16
- Scheduling & affinity → Day 17
- Rolling updates & rollbacks → Day 37
- PodDisruptionBudgets, zero-downtime deploys → Day 40
- Cluster upgrades & version skew → Day 41
- Multi-cluster patterns → Day 45

---

## GitOps, CI/CD, Helm
- GitOps principles → Day 31
- ArgoCD architecture & reconciliation → Days 31–32, 43
- Helm charts, templating → Days 33–34
- CI/CD pipelines (build, test, deploy) → Days 35–36
- Rollbacks & progressive delivery (canary, blue/green) → Days 37–39
- Drift detection & reconciliation → Day 43
- Environment promotion via Git → Day 46

---

## Infrastructure as Code (Terraform & Cloud)
- Terraform workflow (init/plan/apply) → Day 61
- Providers & auth → Day 62
- Remote state & locking → Day 63
- Terraform modules → Day 64
- Cloud networking (VPC/VNet, subnets, routing) → Days 65–66
- IAM & least privilege → Day 67
- Hybrid cloud connectivity → Day 68
- Multi-env infra (dev/stage/prod) → Day 69
- Drift detection & reconciliation → Day 71
- Backup & DR planning → Days 72–73
- Capacity planning at infra layer → Day 74
- Cost vs reliability tradeoffs → Day 75
- Multi-region design → Day 76
- Bootstrapping cloud environments → Day 79
- IaC + GitOps integration → Day 81
- Safe decommissioning → Day 85

---

## Kubernetes Operators & Platform Engineering
- Operator pattern & reconcile loop → Day 91
- CRDs & API extension → Day 92
- Watches, informers → Day 93
- Idempotent reconciliation → Day 94
- Leader election for HA → Day 95
- Managing child resources → Day 96
- Error handling & retries → Day 97
- Observability for operators → Day 98
- CRD versioning → Day 99
- Admission webhooks → Day 100
- Testing operators → Day 101
- RBAC & least privilege for controllers → Day 103
- Multi-tenant platform design → Day 108
- Platform abstractions (golden paths) → Days 111–112
- Auto-remediation via platform features → Day 114
- Platform readiness review → Day 120

---

## Observability (Metrics, Logs, Traces)
- Observability pillars (metrics/logs/traces) → Day 121
- RED method, golden signals → Day 122
- Grafana dashboards → Day 123
- Alerting best practices → Day 124
- SLIs & SLOs → Day 125
- Error budgets → Day 126
- Distributed tracing (OpenTelemetry) → Day 127
- Correlating logs, metrics, traces → Day 128
- Capacity planning with metrics → Day 129
- Load testing & latency percentiles → Day 130
- Bottleneck analysis → Day 131

---

## Reliability Patterns (Failure Handling)
- Chaos engineering basics → Day 132
- Timeouts & retries → Day 133
- Circuit breakers → Day 134
- Bulkheads → Day 135
- Rate limiting → Day 136
- Backpressure → Day 137
- Cascading failure prevention → Day 139
- Graceful degradation → Day 140

---

## Incident Response & SRE Practices
- Incident detection & triage → Day 141
- On-call design & escalation → Day 142
- Incident command & comms → Day 143
- Fast mitigation strategies → Day 144
- Root cause analysis → Day 145
- Blameless postmortems → Day 146
- Turning incidents into prevention → Day 147
- Reducing toil via automation → Day 148
- SRE effectiveness metrics (MTTR, toil) → Day 149
- Reliability readiness review → Day 150

---

## Large-Scale System Design (Fleet / Platform)
- Fleet backend architecture → Day 151
- Device identity & auth → Day 152
- OTA update pipelines → Day 153
- Safe remote commands (blast radius control) → Day 154
- Multi-region active-active design → Day 155
- Offline-first & eventual consistency → Day 156
- Telemetry ingestion pipelines → Day 157
- Capacity planning at fleet scale → Day 158
- API rate limiting & abuse prevention → Day 159
- Streaming data pipelines → Day 160
- Schema evolution → Day 161
- Data retention & privacy → Day 162
- mTLS & zero-trust service-to-service security → Day 163
- Network segmentation → Day 164
- Disaster recovery for global backends → Day 165
- Chaos testing for distributed systems → Day 166

---

## Interview & Career Readiness
- Whiteboard system design → Days 171, 177
- Debugging under pressure → Day 172
- Tradeoff articulation → Day 173
- Behavioral interviews (STAR) → Day 174
- Failure narratives & ownership → Day 175
- Mock SRE interviews → Days 176–177
- Resume mapping to JD → Day 178
- Final technical review → Day 179
- Full platform storytelling → Day 180

---

## Quick Interview Refresh Packs (What to skim)
- Kubernetes Debugging → Days 11–17, 37, 40
- GitOps & CI/CD → Days 31–39, 43, 46
- Terraform & Cloud → Days 61–76, 81, 85
- SRE Core (SLOs, incidents) → Days 124–150
- System Design (Large scale) → Days 151–166
