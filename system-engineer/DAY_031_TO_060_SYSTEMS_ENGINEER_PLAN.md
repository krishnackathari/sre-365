# Systems Engineer / Systems Reliability Engineer — 120-Day Sprint
# Days 1–30 (Foundation Month): Linux + Networking + Kubernetes Ops + Observability + “Production Debugging”

## Daily rules (2 hours/day, no overcommit)
- 25 min: Learn (book/course)
- 55 min: Lab (hands-on; break/fix)
- 25 min: Coding (tiny automation tool)
- 10 min: Notes (important notes template)

## The Month-1 storyline (so it feels seamless)
You are building ONE thing all month:
➡️ A “mini production platform” on your mini PC:
- a Kubernetes cluster (local)
- a demo app (web + api)
- monitoring + alerting basics
- TLS cert rotation
- autoscaling example
And every day you practice the same interview muscle:
➡️ “You see this symptom… what do you do next?”

-------------------------------------------------------------------
DAY 01 — Linux process model (the base of ALL debugging)
Interview focus:
- What a Systems Reliability Engineer does: keep platforms reliable by debugging quickly + automating recurring ops.
Learn (25m):
- How Linux Works: read the section that explains: kernel vs user space, processes, users, permissions.
Concepts you MUST know today:
- PID, PPID, process states (R/S/D/Z), signals (TERM/KILL), exit codes.
Lab (55m):
- Start a long-running process: `sleep 9999 &`
- Observe:
  - `ps -o pid,ppid,stat,cmd -p <PID>`
  - `top` / `htop`
- Send signals:
  - `kill -TERM <PID>` then `kill -KILL <PID>`
- Create a zombie (quick demo): run a tiny program that forks and doesn’t wait (use Python sample below).
Coding (25m):
- Python: list top 10 CPU processes (use `ps` output parse).
Notes (10m):
- 1-liner takeaway: “I can observe + control process lifecycle using ps/top/kill and explain process states.”
Mini-quiz prompts to paste:
- “Explain R/S/D/Z states.”
- “Why is kill -9 dangerous?”
- “How would you debug a stuck process?”
-------------------------------------------------------------------
DAY 02 — /proc and “what the OS knows” (Cloudflare-style debugging)
Interview focus:
- When asked “how would you debug,” you must start with OS facts: /proc, fd, memory maps.
Learn (25m):
- How Linux Works: sections on /proc, process introspection, file descriptors.
Concepts:
- `/proc/<pid>/status`, `/proc/<pid>/fd`, `/proc/<pid>/cmdline`, `/proc/<pid>/net/*`
Lab (55m):
- Pick a running process (your shell, python, sleep) and inspect:
  - `cat /proc/<pid>/status`
  - `ls -l /proc/<pid>/fd | head`
  - `cat /proc/<pid>/cmdline | tr '\0' ' '`
- Find “what files does this process have open?”:
  - `lsof -p <pid> | head`
Coding (25m):
- Python: given a PID, print command, open FD count, RSS memory (parse /proc).
Notes:
- 3 bullets max: what /proc is, what FD means, why FD leaks matter.
Quiz prompts:
- “What is an FD leak and symptoms?”
- “How do you check what sockets a process owns?”
-------------------------------------------------------------------
DAY 03 — CPU: load vs utilization, run queue, saturation thinking
Interview focus:
- “CPU is high” → you must separate utilization from saturation and find the culprit fast.
Learn:
- How Linux Works: CPU/scheduling section (run queue concept).
Concepts:
- load average meaning, context switching, nice, iowait (high iowait ≠ CPU bound)
Lab:
- Generate CPU load (simple loop) and compare:
  - `uptime` (load)
  - `top` (CPU%)
  - `vmstat 1` (runnable procs, context switches)
- Identify hottest process:
  - `ps -eo pid,pcpu,pmem,cmd --sort=-pcpu | head`
Coding:
- Python: “cpu_watch.py” — sample CPU usage every 2s for 1 minute, print max.
Notes:
- Debugging model: “Confirm symptom → identify top offender → check saturation (run queue) → mitigate.”
Quiz prompts:
- “When can load average be high but CPU low?”
- “What does iowait tell you?”
-------------------------------------------------------------------
DAY 04 — Memory fundamentals (RSS, cache, swap) + OOM story (K8s tie-in later)
Interview focus:
- “pod OOMKilled” is Linux memory reality. You need a clean mental model.
Learn:
- How Linux Works: memory sections (RSS vs VIRT; page cache; swap).
Concepts:
- RSS vs VSZ, page cache, swap, OOM killer basics.
Lab:
- Check memory snapshot:
  - `free -h`
  - `cat /proc/meminfo | head`
- Create memory pressure (lightly) using a python script that allocates memory.
- Watch:
  - `vmstat 1`
  - `dmesg | tail` (look for OOM messages if you push too far—don’t crash your box)
Coding:
- Python: “mem_top.py” — show top 10 RSS processes.
Notes:
- Interview soundbite: “OOM is last resort; I confirm RSS growth, limits, and whether cache is reclaimable.”
Quiz:
- “Why does Linux ‘use all memory’?”
- “Difference between RSS and cache?”
-------------------------------------------------------------------
DAY 05 — Disk I/O + why apps get slow (latency, fsync, saturation)
Interview focus:
- Real interviews ask “site slow” → you must check I/O and saturation.
Learn:
- How Linux Works: disks/filesystems, buffering, sync.
Concepts:
- IOPS vs throughput, latency, fsync, iowait, queue depth (high-level).
Lab:
- Observe I/O:
  - `iostat -xz 1` (if available) or `vmstat 1` + `iotop` (if available)
- Create a slow-write scenario:
  - write a big file with `dd` (small enough to be safe)
- Find process I/O:
  - `lsof` and `pidstat -d 1` (if available)
Coding:
- Python: write a file and time it; print throughput + latency per chunk.
Notes:
- Debug model: “If CPU low + iowait high → check disk saturation first.”
Quiz:
- “What’s the difference between throughput and latency?”
-------------------------------------------------------------------
DAY 06 — Networking basics on Linux: sockets, ports, connection states
Interview focus:
- Cloudflare-style: deep network stack basics matter. :contentReference[oaicite:2]{index=2}
Learn:
- How Linux Works: networking intro; plus any quick networking refresher video you like.
Concepts:
- listening vs established, ephemeral ports, TCP states (SYN-SENT, ESTABLISHED, TIME_WAIT).
Lab:
- Run a local server:
  - Python `http.server` on port 8000
- Inspect:
  - `ss -lntp | grep 8000`
  - `ss -antp | head`
- Create connections with `curl localhost:8000`
Coding:
- Python: simple TCP echo server + client.
Notes:
- 1-liner: “I can map ‘is it listening?’ + ‘is it accepting connections?’ using ss.”
Quiz:
- “What causes TIME_WAIT storms?”
- “How do you confirm a port is open locally?”
-------------------------------------------------------------------
DAY 07 — DNS resolution end-to-end (critical for Cloudflare / Systems roles)
Interview focus:
- “DNS is broken” + “How does a web request work?” is common. :contentReference[oaicite:3]{index=3}
Learn:
- DNS basics: recursive resolver vs authoritative; TTL; NXDOMAIN.
Concepts:
- resolution chain: client cache → recursive → root → TLD → authoritative.
Lab:
- `dig example.com`
- `dig +trace example.com` (see the chain)
- Check local resolver config:
  - `cat /etc/resolv.conf`
- Measure DNS latency:
  - run dig multiple times and compare cached vs uncached.
Coding:
- Python: resolve hostname + time it (use `socket.getaddrinfo`).
Notes:
- Soundbite: “DNS issues are often caching/TTL or upstream resolver problems; I verify with dig +trace.”
Quiz:
- “What’s a recursive resolver?”
- “What does TTL really control?”
-------------------------------------------------------------------
DAY 08 — TLS/SSL fundamentals + cert rotation story (interview favorite)
Interview focus:
- “How did you rotate certificates?” is a real ops question.
Learn:
- TLS handshake high-level: cert, chain, SNI, expiration, renewals.
Concepts:
- cert chain, SANs, expiration monitoring, renewal automation, rollback.
Lab:
- Create a self-signed cert, run HTTPS locally (nginx or python + ssl).
- Inspect cert:
  - `openssl s_client -connect localhost:443 -servername localhost`
  - `openssl x509 -in cert.pem -text -noout | head -n 40`
Coding:
- Python: “cert_check.py” — given host:port, print expiry date and days remaining.
Notes:
- Write a 5-step cert rotation runbook outline (monitor → stage → deploy → verify → rollback).
Quiz:
- “What’s SNI?”
- “How do you prevent downtime during cert rotation?”
-------------------------------------------------------------------
DAY 09 — L4 vs L7 load balancing + health checks (Cloudflare core)
Interview focus:
- load balancing + failure detection + traffic shift.
Learn:
- L4 vs L7; health checks; passive vs active checks.
Lab:
- Run 2 backend servers (ports 9001, 9002).
- Put nginx in front as reverse proxy (round robin).
- Simulate one backend down and verify fail behavior.
Coding:
- Python: health check tool that hits /health and reports status/latency.
Notes:
- Debug model: “Confirm if LB is routing correctly; isolate backend vs LB vs DNS.”
Quiz:
- “Difference between active/passive health checks?”
- “Why can L7 LB help with retries and headers?”
-------------------------------------------------------------------
DAY 10 — HTTP request path + debugging “site is slow”
Interview focus:
- Walk through: DNS → TCP → TLS → HTTP → backend.
Learn:
- End-to-end request lifecycle; common bottlenecks.
Lab:
- Use `curl -v https://example.com` and interpret:
  - DNS time, connect time, TLS time (if you use `curl -w` format).
- Add artificial latency using `tc` and see impact.
Coding:
- Python: mini “http_timing.py” using `requests` + timing around connect/TTFB.
Notes:
- Make your “standard web debugging checklist.”
Quiz:
- “How do you separate client vs server latency?”
-------------------------------------------------------------------
DAY 11 — Kubernetes Core Concepts (Mumshad CKA: Core Concepts)
Interview focus:
- K8s objects and why they exist (not memorization).
Learn (CKA course):
- Section 2: Core Concepts (Pods, ReplicaSets, Deployments, Namespaces, Services)
Lab:
- Create namespace `prod-sim`
- Deploy a simple app Deployment + Service
- Verify:
  - `kubectl get all -n prod-sim`
Coding:
- Python: “k8s_inventory.py” — call kubectl and summarize pods by status.
Notes:
- 3 bullets: why Deployment, why Service, why Namespace.
Quiz:
- “When would you use a Deployment vs StatefulSet?”
-------------------------------------------------------------------
DAY 12 — Scheduling (Mumshad: Scheduling) + “why is my pod Pending?”
Interview focus:
- Debug Pending pods like a production engineer.
Learn:
- Mumshad Section 3: Scheduling (node selectors, taints/tolerations, affinity).
Lab:
- Create a pod that cannot schedule (wrong nodeSelector).
- Debug using:
  - `kubectl describe pod ...` (events)
- Fix it and confirm scheduling.
Coding:
- Python: parse `kubectl get events` and show top scheduling failures.
Notes:
- Debug model: “Pending → describe → events → constraints → resources.”
Quiz:
- “Taint vs affinity: when do you use each?”
-------------------------------------------------------------------
DAY 13 — App Lifecycle (Mumshad: App Lifecycle) + probes (CrashLoop)
Interview focus:
- “Why is it CrashLoopBackOff?” is guaranteed.
Learn:
- Mumshad Section 5: rolling updates, rollbacks, probes.
Lab:
- Deploy app with wrong command to induce CrashLoop.
- Use:
  - `kubectl logs`, `kubectl describe`, `kubectl get events`
- Add readiness + liveness probe; intentionally break readiness to see traffic behavior.
Coding:
- Python: “pod_triage.py” — given namespace, print pods not Ready + last event snippet.
Notes:
- Soundbite: “I start with events/logs, then probes/config, then resources, then node conditions.”
Quiz:
- “Difference between readiness and liveness?”
-------------------------------------------------------------------
DAY 14 — Logging & Monitoring (Mumshad: Logging/Monitoring) + Prometheus intro
Interview focus:
- Observability basics: logs vs metrics vs traces.
Learn:
- Mumshad Section 4: Logging & Monitoring.
Lab:
- Deploy metrics-server (if not present).
- Use:
  - `kubectl top nodes/pods`
- Install a simple Prometheus stack (lightweight) OR use KodeKloud lab environment if easier.
Coding:
- Python: “k8s_resource_report.py” — list top CPU/mem pods from kubectl top output.
Notes:
- 3 bullets: what metrics answer vs what logs answer.
Quiz:
- “What are golden signals / RED metrics?”
-------------------------------------------------------------------
DAY 15 — Security (Mumshad: Security) + real-world “TLS + secrets + RBAC”
Interview focus:
- RBAC + secrets + least privilege (Cloudflare calls out security).
Learn:
- Mumshad Section 7: Security (RBAC, ServiceAccounts, SecurityContext).
Lab:
- Create a ServiceAccount with minimal RBAC to list pods only.
- Confirm forbidden actions fail.
- Store a secret; mount it; verify app reads it.
Coding:
- Python: “rbac_check.py” — run `kubectl auth can-i` for a set of actions and print matrix.
Notes:
- Soundbite: “I enforce least privilege with RBAC + audit via can-i checks.”
Quiz:
- “Secret vs ConfigMap?”
- “How do you rotate a secret safely?”
-------------------------------------------------------------------
DAY 16 — Storage (Mumshad: Storage) + PVC debugging
Interview focus:
- PVC Pending + Stateful workloads.
Learn:
- Mumshad Section 8: Storage (PV/PVC/StorageClass).
Lab:
- Create PVC; intentionally reference missing StorageClass to get Pending.
- Debug with describe + events; fix it.
Coding:
- Python: “pvc_triage.py” — list PVCs not Bound + reason from describe/events.
Notes:
- 3 bullets: PV vs PVC vs StorageClass.
Quiz:
- “When do you use StatefulSet?”
-------------------------------------------------------------------
DAY 17 — Networking in Kubernetes (Mumshad: Networking)
Interview focus:
- service discovery, cluster DNS, connectivity debugging.
Learn:
- Mumshad Section 9: Networking.
Lab:
- Validate DNS inside cluster:
  - run a busybox pod and `nslookup service-name`
- Break service selector (no endpoints) and debug:
  - `kubectl get endpoints`
Coding:
- Python: “svc_endpoints_check.py” — report services with 0 endpoints.
Notes:
- Debug model: “Can’t reach service → endpoints → selectors → pod labels → network policy.”
Quiz:
- “Why does Service have no endpoints?”
-------------------------------------------------------------------
DAY 18 — Cluster Maintenance (Mumshad: Cluster Maintenance)
Interview focus:
- node issues, drain/cordon, upgrades mental model.
Learn:
- Mumshad Section 6: Cluster Maintenance.
Lab:
- Practice:
  - `kubectl cordon`, `kubectl drain`, `kubectl uncordon`
- Simulate node pressure by running CPU/mem heavy pod (small scale).
Coding:
- Python: “node_health.py” — summarize node conditions and pressure flags.
Notes:
- Soundbite: “I drain nodes safely, protect workloads with PDBs, and watch node conditions.”
Quiz:
- “What’s the difference between cordon and drain?”
-------------------------------------------------------------------
DAY 19 — HPA basics + why autoscaling fails (interview-specific)
Interview focus:
- “When did you use HPA/VPA?” → you must explain *why* and *tradeoffs*.
Learn:
- K8s autoscaling basics (HPA); metrics-server dependency.
Lab:
- Deploy an app with CPU requests set.
- Configure HPA based on CPU.
- Generate load and watch replicas change.
Coding:
- Python: “hpa_observer.py” — watch HPA status (current/desired replicas) every 10s.
Notes:
- Soundbite: “HPA needs requests set + metrics; I validate metrics pipeline first.”
Quiz:
- “Why does HPA not scale even under load?”
-------------------------------------------------------------------
DAY 20 — VPA concepts (even if you don’t run it) + requests/limits mastery
Interview focus:
- HPA vs VPA vs Cluster Autoscaler tradeoffs.
Learn:
- VPA modes (Off/Auto/Recommend), interaction with HPA.
Lab:
- Do NOT install full VPA if it’s heavy—just practice:
  - calculate sane requests/limits from observed metrics (`kubectl top`).
Coding:
- Python: “requests_recommender.py” — take observed CPU/mem and suggest requests/limits (simple heuristic).
Notes:
- 3 bullets: when HPA, when VPA, when both are risky.
Quiz:
- “Why is VPA tricky with HPA?”
-------------------------------------------------------------------
DAY 21 — Git fundamentals for platform engineers (branching/rebase reality)
Interview focus:
- Cloudflare mentions branching/merging/rebasing explicitly.
Learn:
- Git: branching, rebase vs merge, resolving conflicts.
Lab:
- Create a repo for your month-1 platform manifests.
- Make 2 branches, conflicting edits, rebase and resolve.
Coding:
- Small: write a pre-commit check script (YAML lint or simple grep rules).
Notes:
- Soundbite: “I use rebase to keep history clean; merge when preserving context is better.”
Quiz:
- “When do you prefer rebase over merge?”
-------------------------------------------------------------------
DAY 22 — CI/CD minimal pipeline (build → test → deploy)
Interview focus:
- “How do you ship safely?” (pipeline + rollback).
Learn:
- CI/CD basics: build, unit test, deploy, canary/blue-green.
Lab:
- GitHub Actions (or local script): validate manifests + run a smoke test (`curl` service).
Coding:
- Python: “smoke_test.py” — test /health, /version endpoints.
Notes:
- 5-step safe deploy checklist.
Quiz:
- “What makes a deploy ‘safe’?”
-------------------------------------------------------------------
DAY 23 — Terraform fundamentals (only what interviews expect early)
Interview focus:
- Manage infra changes, state, drift. (common SRE interview topic)
Learn:
- Terraform: state, plan/apply, modules concept, drift.
Lab:
- Create a tiny module structure locally (even without real AWS apply).
- Practice:
  - `terraform init/plan`
Coding:
- Python: parse a terraform plan output (simple grep) and summarize resources to change.
Notes:
- Soundbite: “I treat plan as a code review artifact; avoid manual drift.”
Quiz:
- “Why is terraform state sensitive?”
-------------------------------------------------------------------
DAY 24 — Prometheus/Alerting basics + alert quality (SRE mindset)
Interview focus:
- “How do you avoid alert fatigue?”
Learn:
- Google SRE book: alerting philosophy (symptoms vs causes).
Lab:
- Create one useful alert (symptom-based) for your demo service:
  - high error rate OR high latency.
Coding:
- Python: “error_rate_calc.py” — compute error rate from logs/metrics snapshot.
Notes:
- 3 bullets: good alert properties (actionable, low noise, tied to SLO).
Quiz:
- “Symptom-based vs cause-based alert?”
-------------------------------------------------------------------
DAY 25 — Incident drill #1: DNS breaks
Interview focus:
- Cloudflare-style scenario interviews (“You see this… what do you do?”) :contentReference[oaicite:4]{index=4}
Scenario:
- Your app is up but name resolution fails intermittently.
Lab:
- Break CoreDNS or point to wrong resolver in test namespace.
- Debug steps:
  - inside pod: `nslookup`, `dig`
  - check coredns logs
  - check svc endpoints
Coding:
- Python: “dns_triage.py” — run DNS checks from inside a pod (exec) and report failures.
Notes:
- Write a mini postmortem: impact, root cause, fix, prevention.
Quiz:
- “How do you prove it’s DNS and not TCP?”
-------------------------------------------------------------------
DAY 26 — Incident drill #2: TLS cert expired / mismatch
Interview focus:
- cert expiry monitoring + safe rotation.
Scenario:
- HTTPS endpoint returns cert error.
Lab:
- Swap cert to expired/self-signed and observe client errors.
- Fix by replacing cert and validating with openssl/curl.
Coding:
- Extend cert_check.py to run daily and output “ALERT if < 14 days”.
Notes:
- Runbook: detect → mitigate (serve old cert?) → rotate → validate → monitor.
Quiz:
- “How do you rotate certs with zero downtime?”
-------------------------------------------------------------------
DAY 27 — Incident drill #3: CrashLoopBackOff + OOMKilled
Interview focus:
- most common real production K8s failures.
Scenario:
- deploy with low memory limit; trigger OOM.
Lab:
- Observe `kubectl describe pod` reason OOMKilled.
- Tie to Linux: check node dmesg (if accessible) + memory usage.
Coding:
- “oom_triage.py” — list pods restarted > N times + reason.
Notes:
- Debug model: logs → events → resources → app behavior → mitigation.
Quiz:
- “Why might OOMKilled happen even when node has free memory?”
-------------------------------------------------------------------
DAY 28 — System design (infra flavored): “safe deploy platform”
Interview focus:
- Cloudflare Production Engineering: build tools to deploy safely.
Design prompt:
- “Design a deploy system for microservices on Kubernetes with rollback + observability.”
Deliverable:
- 1 page design with:
  - pipeline stages
  - canary strategy
  - metrics gates
  - rollback triggers
Coding:
- generate a YAML template builder for Deployment + Service + HPA.
Notes:
- Soundbite: “I gate deploys on SLO signals; rollbacks are automated and fast.”
Quiz:
- “What signals would you gate a deploy on?”
-------------------------------------------------------------------
DAY 29 — Mock interview day (Linux + networking + K8s)
Interview focus:
- practice the exact loop style reported by candidates: troubleshooting + shell + networking + coding + system design. :contentReference[oaicite:5]{index=5}
Mock structure (2h):
- 30m: Linux debugging questions (process/mem/io)
- 30m: Networking (DNS, TCP, LB, TLS)
- 30m: K8s scenario (Pending/CrashLoop/No endpoints)
- 30m: 1 small coding task (health check / triage tool)
Notes:
- write your “gaps list” (max 5 items).
-------------------------------------------------------------------
DAY 30 — Consolidation day: build your Month-1 “portfolio artifact”
Goal:
- end Month 1 with something you can point to in interviews:
  - a repo with: manifests, small tools, runbooks, postmortems.
Deliverable:
- README that describes:
  - what you built
  - what failures you simulated
  - how you debugged
Coding:
- combine your scripts into one CLI: `platform_triage.py` with subcommands:
  - cpu, mem, dns, cert, k8s, endpoints
Notes:
- Write 3 interview stories:
  1) DNS incident
  2) OOM/CrashLoop incident
  3) Cert rotation incident
-------------------------------------------------------------------

# IMPORTANT: What Cloudflare/Systems interviewers care about (use this lens daily)
- Deep network stack understanding is valued at Cloudflare. :contentReference[oaicite:6]{index=6}
- Linux debugging with strace/ps/netstat(/ss)/proc is a known interview pattern. :contentReference[oaicite:7]{index=7}
- Candidates report troubleshooting + UNIX shell test + networking + coding + system design in loops. :contentReference[oaicite:8]{index=8}
