# CPU Throttling in Kubernetes

## Why pods are slow even when CPU is low

In Kubernetes, pods have CPU limits.  
Linux enforces these limits using **cgroups** by giving each container a fixed CPU time budget.

When a pod uses its time budget, it is **paused** until the next scheduling window.

This causes:
- high latency
- timeouts
- slow requests

Even when node CPU is idle.

---

## What a CPU limit really means

```
cpu: 500m
```

means:
```
The container may run for 50ms every 100ms period
```

```
cpu: 1
```
means:
```
The container may run for 100ms every 100ms period (1 full core)
```

```
cpu: 2
```
means:
```
The container may run for 200ms every 100ms (2 cores worth of time)
```

CPU limits are **time budgets**, not core pinning.

---

## Why CPU usage lies

A throttled container cannot run, so it cannot consume CPU.

Example:
```
Limit = 500m
App wants = 1 CPU
```

Linux behavior:
```
Runs 50ms → frozen 50ms → runs 50ms → frozen 50ms
```

CPU usage shows:
```
500m
```

But the app is stalled half the time.
# CPU Throttling in Kubernetes

## Why pods are slow even when CPU is low

In Kubernetes, pods have CPU limits.  
Linux enforces these limits using **cgroups** by giving each container a fixed CPU time budget.

When a pod uses its time budget, it is **paused** until the next scheduling window.

This causes:
- high latency
- timeouts
- slow requests

Even when node CPU is idle.

---

## What a CPU limit really means

```
cpu: 500m
```

means:
```
The container may run for 50ms every 100ms period
```

```
cpu: 1
```
means:
```
The container may run for 100ms every 100ms period (1 full core)
```

```
cpu: 2
```
means:
```
The container may run for 200ms every 100ms (2 cores worth of time)
```

CPU limits are **time budgets**, not core pinning.

---

## Why CPU usage lies

A throttled container cannot run, so it cannot consume CPU.

Example:
```
Limit = 500m
App wants = 1 CPU
```

Linux behavior:
```
Runs 50ms → frozen 50ms → runs 50ms → frozen 50ms
```

CPU usage shows:
```
500m
```

But the app is stalled half the time.

---

## How to detect throttling (source of truth)

Inside the container:

```bash
cat /sys/fs/cgroup/cpu.stat
```

Example:
```text
nr_periods 105432
nr_throttled 8732
throttled_time 12938712987
```

Meaning:
- The container was forced to stop thousands of times
- It spent real time being frozen
- This is CPU throttling

---

## What kubectl shows (and hides)

```bash
kubectl describe pod <pod>
```

Example:
```text
Limits:
  cpu: 500m
```

This shows the **limit**, not the throttling.

```bash
kubectl top pod
```

Shows:
```
Actual CPU used
```

It does NOT show:
- how much CPU the pod wanted
- how much time it was paused

---

## Why this breaks production

Teams set:
```text
requests: 100m
limits:   200m
```

App spikes to 400m → throttled → slow → timeouts → alerts.

The node may have 20 free CPUs, but the pod is frozen.

---

## What the cpu.stat fields mean

From:
```bash
cat /sys/fs/cgroup/cpu.stat
```

```text
nr_periods
nr_throttled
throttled_time
```

Meaning:

```
nr_periods     = how many 100ms scheduling windows passed
nr_throttled   = how many of those windows the container hit its CPU limit
throttled_time = total time the container was frozen (nanoseconds)
```

Example:
```
nr_periods    10000
nr_throttled   4000
throttled_time 25s
```

Interpretation:
- 40% of scheduling windows were throttled
- The container lost 25 seconds of runtime
- Requests were delayed even though CPU was available

---

## SRE rules

- Low CPU usage does NOT mean the pod is healthy
- CPU limits insert forced pauses
- Throttling causes latency even on idle nodes
- `cpu.stat` is the only authoritative proof

---

## The CPU throttling pattern

```text
Pods slow
CPU usage looks low
Node CPU idle
Limits are set
cpu.stat shows throttling
```

This is **cgroup CPU throttling**, not lack of CPU capacity.

