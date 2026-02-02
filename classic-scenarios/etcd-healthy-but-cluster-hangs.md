# etcd Is Healthy but the Cluster Hangs (Watch Backlog & Object Churn)

## Why this issue is confusing

You observe:
- etcd reports healthy
- API server is reachable
- Control plane pods are Running
- No crashes or panics

Yet:
- `kubectl get` is slow
- Deployments take forever
- Controllers lag
- Events are delayed
- HPA reacts late

This happens when **etcd is overloaded by watch traffic and object churn**, not because it is down.

---

## First misleading check

```bash
kubectl exec -n kube-system etcd-<node> -- etcdctl endpoint health
```

```text
https://127.0.0.1:2379 is healthy: took = 120ms
```

Interpretation:
- etcd is alive
- This does NOT mean it is keeping up

---

## Symptom shows up as LIST/WATCH slowness

```bash
time kubectl get pods -A
```

```text
real    0m6.214s
```

LIST operations are heavy.
WATCH operations amplify the problem.

---

## What etcd is actually doing

etcd:
- stores every Kubernetes object
- serves LIST requests
- streams WATCH updates to controllers
- persists every write to disk

When objects churn:
- watches fall behind
- queues grow
- latency explodes

---

## Common hidden causes

- Large number of Pods/Endpoints
- Rapid pod churn (Jobs, HPA flapping)
- Large CRDs
- Controllers with aggressive resync
- Event storms
- Slow disks

---

## Prove watch pressure (API server side)

```bash
kubectl get --raw /metrics | grep apiserver_watch_events_total | head
```

High values mean:
- many watches
- high fan-out
- etcd under pressure

---

## Prove etcd backlog

On the control plane node:

```bash
journalctl -u etcd --since "10 minutes ago"
```

Look for:
```text
apply request took too long
watch channel is full
```

These confirm backlog.

---

## Disk latency is the usual bottleneck

```bash
iostat -x 1
```

Example:
```text
Device   r_await w_await
nvme0n1  18.0    65.0
```

Interpretation:
- etcd writes are slow
- WAL fsync blocks
- watchers fall behind

---

## Why CPU looks fine

etcd is:
- mostly I/O bound
- sensitive to fsync latency
- blocked waiting on disk

CPU can be idle while the cluster hangs.

---

## Kubernetes amplification

Each change:
- updates etcd
- notifies all watchers
- triggers controllers
- creates more writes

This feedback loop causes **control-plane congestion**.

---

## What NOT to assume

❌ etcd healthy = cluster healthy  
❌ No errors = no pressure  
❌ CPU low = no problem  

etcd fails by **falling behind**, not crashing.

---

## Correct remediation

Immediate:
- Stop noisy Jobs
- Pause flapping HPAs
- Reduce pod churn
- Rate-limit controllers if possible

Permanent:
- Reduce object count
- Prune unused CRDs
- Increase control plane disk IOPS
- Separate etcd onto fast disks
- Tune controller resync periods

---

## Validate recovery

```bash
time kubectl get pods -A
```

Healthy:
```text
real    0m0.350s
```

Also watch:
- fewer etcd warnings
- stable apiserver latency

---

## SRE rules

- etcd speed = cluster speed
- Watch traffic hurts more than writes
- Disk latency kills control planes
- Health checks lie under load

---

## Failure pattern

```text
etcd healthy
cluster slow
kubectl laggy
controllers delayed
watch backlog
disk latency high
```

This is **etcd watch backlog**, not an etcd outage.

