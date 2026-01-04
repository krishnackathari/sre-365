# sre-365

A practical, engineer-focused knowledge base built from **real production behavior**, not theory.

This repository documents how systems actually behave under load, failure, and stress — and how to reason about them using first-principles debugging.

The goal is simple:

> **Build strong operational intuition by studying real signals, not dashboards alone.**

---

## What This Repo Is

`sre-365` is a growing collection of short, precise notes based on:
- Production incidents
- Misleading metrics
- Debugging dead ends
- “This looked fine… but wasn’t” scenarios

Each entry focuses on:
- What the system *appeared* to be doing
- What it was *actually* doing
- Which signals revealed the truth

No tutorials. No fluff. No copy-paste guides.

---

## Topics Covered (and Why)

### Linux (foundation)
Core system behavior everything else depends on:
- Load average vs CPU usage
- Runnable vs running processes
- I/O wait and uninterruptible sleep
- Memory pressure and reclaim
- Why “idle” systems feel slow

📁 `linux/`

---

### Networking (where most bugs hide)
Understanding how network issues masquerade as app or CPU problems:
- Packet loss vs latency
- SYN queues and accept queues
- Ephemeral port exhaustion
- DNS delays and retries
- TCP retransmits and timeouts
- “The network is fine” (until it isn’t)

📁 `networking/`

---

### Kubernetes (when Linux is abstracted)
How Linux and networking behavior surface through Kubernetes:
- CPU throttling vs usage
- Node pressure vs pod metrics
- Scheduling delays
- Pod restarts with no obvious errors
- CNI and kube-proxy side effects

📁 `kubernetes/`

---

### Jenkins (CI as a production system)
Operational behavior of CI pipelines:
- Queue vs executors
- Idle agents with stuck builds
- Disk and workspace exhaustion
- Slow pipelines with “green” status

📁 `jenkins/`

---

### Python (as an operational tool)
Python in long-running, real systems:
- CPU vs GIL behavior
- Memory growth and leaks
- Thread contention
- Debugging stuck or slow processes

📁 `python/`

---

### Terraform / GitHub / CI
Infrastructure and automation under failure:
- State drift
- Partial applies
- CI race conditions
- Green pipelines that deploy broken systems

📁 `infra/`, `github/`

---

## What This Repo Is NOT

- ❌ Beginner tutorials  
- ❌ Installation guides  
- ❌ Tool marketing  
- ❌ “Top 10 commands” lists  

If it doesn’t help debug a real incident, it doesn’t belong here.

---

## Writing Style & Rules

Every note aims to answer:
1. **What was confusing?**
2. **What signal or metric was misleading?**
3. **What finally explained the behavior?**

Preferred tools:
- `vmstat`
- `iostat`
- `ss`, `netstat`
- `tcpdump`
- `uptime`
- `top`
- `strace` / `perf` (when needed)

Short. Precise. Reproducible.

---

## Publishing Rhythm

- 3 focused notes per week
- Each note < 10 minutes to read
- GitHub is the source of truth
- Website mirrors curated highlights

---

## Why This Exists

Because real systems don’t fail loudly —  
they fail *politely*, with green dashboards and wrong assumptions.

This repo is about learning to see past that.
