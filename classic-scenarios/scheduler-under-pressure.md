# Pods Stay Pending but Nodes Have Free Resources (kube-scheduler Under Pressure)

## Why this issue is confusing

You observe:
- Pods stuck in `Pending`
- Nodes show free CPU and memory
- No obvious errors
- Cluster is “healthy”

Yet:
- Scheduler does not place pods
- Backlog grows
- Deployments stall

This happens when **kube-scheduler is overloaded or constrained**, not because the cluster lacks resources.

---

## First misleading check

```bash
kubectl get nodes
```

```text
NAME       STATUS   ROLES    AGE   VERSION
worker-1   Ready    <none>   120d  v1.28.1
worker-2   Ready    <none>   120d  v1.28.1
```

Interpretation:
- Nodes are Ready
- This does NOT mean scheduler can place pods fast enough

---

## Symptom: pods Pending

```bash
kubectl get pods -n prod
```

```text
api-7c9d6f4f9d-abcde   0/1   Pending   0   3m
```

---

## Confirm scheduler events

```bash
kubectl describe pod api-7c9d6f4f9d-abcde
```

Look for:
```text
Events:
  Type     Reason            Message
  Warning  FailedScheduling  0/5 nodes are available
```

Even when resources exist, placement can be delayed.

---

## Why scheduler falls behind

Scheduler must:
- watch pod creations
- score nodes
- evaluate constraints
- account for affinities, taints, topology spread
- write binding decisions

Under load:
- many pods created at once
- complex scheduling rules
- large node count

This creates **scheduling latency**.

---

## Common hidden causes

- Bursty Job workloads
- Large clusters with many nodes
- Heavy use of:
  - node affinity
  - pod affinity/anti-affinity
  - topology spread constraints
- Scheduler CPU-starved
- API server latency

---

## Prove scheduler backlog via metrics

```bash
kubectl get --raw /metrics | grep scheduler_pending_pods | head
```

Example:
```text
scheduler_pending_pods  245
```

Interpretation:
- Scheduler queue is backed up

---

## Check scheduler latency

```bash
kubectl get --raw /metrics | grep scheduling_latency_seconds_bucket | head
```

High tail latency = scheduler under pressure.

---

## Why “free CPU” is misleading

Nodes may have:
- free raw CPU
- but no valid placement due to:
  - affinity rules
  - taints/tolerations
  - resource fragmentation
  - topology constraints

Scheduler is solving a constraint problem, not just packing bins.

---

## What NOT to do

❌ Add more pods blindly  
❌ Remove constraints without understanding  
❌ Restart scheduler repeatedly  
❌ Assume it’s a capacity problem  

These hide the root cause.

---

## Correct remediation

Immediate:
- Reduce burst size (batch pod creation)
- Temporarily relax affinity rules
- Stagger Job launches

Permanent:
- Increase scheduler resources
- Reduce scheduling constraints
- Use multiple schedulers for different workloads
- Shard large Job bursts
- Reduce API server latency

---

## Validate recovery

```bash
kubectl get pods -n prod -w
```

Healthy:
```text
api-...   1/1   Running
```

Scheduling happens quickly.

---

## SRE rules

- Scheduler can be the bottleneck
- Free CPU ≠ schedulable
- Constraints amplify latency
- Bursts overwhelm schedulers

---

## Failure pattern

```text
pods pending
nodes free
constraints heavy
scheduler lag
deployments stalled
```

This is **scheduler pressure**, not lack of capacity.

