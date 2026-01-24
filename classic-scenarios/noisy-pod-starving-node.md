# One Noisy Pod Starves the Node (Resource Contention)

## Why this issue is confusing

You observe:
- Node is Ready
- Pods are Running
- No pod crashes
- No obvious errors

Yet:
- Many apps on the node become slow
- Timeouts increase
- Unrelated services misbehave
- Moving pods to another node “fixes” it

This happens when **one noisy pod consumes shared node resources**.

---

## What “noisy pod” means

A noisy pod is a pod that:
- uses excessive CPU, memory, disk, or network
- impacts other pods on the same node
- does not necessarily crash itself

Kubernetes schedules pods,  
but **Linux enforces resources at runtime**.

---

## Most common noisy resources

- CPU (throttling others)
- Memory (page cache pressure)
- Disk I/O (I/O wait)
- Ephemeral storage (logs, temp files)
- Network queues

Any one can starve the node.

---

## First signal: node looks busy, pods look fine

```bash
kubectl top node
```

Example:
```text
NAME        CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
worker-1    3800m        95%    28Gi             88%
```

Interpretation:
- Node is heavily loaded
- Kubernetes does not tell you *who* is causing it

---

## Find the noisy pod

```bash
kubectl top pod --all-namespaces --sort-by=cpu
```

Example:
```text
NAMESPACE   NAME           CPU(cores)
prod        batch-job-1    2200m
prod        api-1           120m
prod        api-2           110m
```

Interpretation:
- One pod is consuming most of the CPU
- Others are innocent victims

---

## Why this hurts other pods

Linux scheduler:
- shares CPU time
- queues runnable processes
- increases latency

Result:
- context switching
- cache thrashing
- request delays

Even if:
- other pods have low CPU usage
- their limits look fine

---

## Disk I/O version of the same problem

Check I/O wait:

```bash
vmstat 1
```

Example:
```text
r  b   si  so  us  sy  id  wa
2  8    0   0   5   3  40  52
```

Interpretation:
- CPU is idle
- High `wa` means waiting on disk
- One pod is flooding disk I/O

---

## Memory pressure variant

```bash
free -m
```

Example:
```text
available: 300MB
```

Interpretation:
- Page cache eviction
- Frequent reclaim
- Apps stall on memory access

No pod is OOMKilled — yet everything slows.

---

## Kubernetes-specific amplification

- Pods share the same node kernel
- Page cache is global
- Disk queues are global
- Network queues are global

One pod can harm many.

---

## Why monitoring misses this

Dashboards often show:
- per-pod CPU
- per-pod memory
- no errors

They miss:
- node-level contention
- queueing
- steal time
- I/O wait

So the problem looks “random”.

---

## What NOT to do

❌ Restart all pods  
❌ Scale everything blindly  
❌ Blame the network first  

These hide the real issue.

---

## Correct remediation

- Set **requests and limits** properly
- Isolate batch / noisy workloads
- Use separate node pools
- Apply QoS classes intentionally
- Throttle or schedule heavy jobs off-peak

---

## SRE rules

- One pod can break many
- Node resources are shared
- Kubernetes schedules, Linux enforces
- Always check node-level metrics

---

## Failure pattern

```text
node slow
pods running
one pod hot
others starved
moving pods helps
```

This is **node starvation by a noisy pod**, not app failure.

