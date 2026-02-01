# API Server Reachable but Slow (Control Plane Latency, Not Outage)

## Why this issue is confusing

You observe:
- `kubectl` commands work
- API server endpoint responds
- No errors or crashes
- Control plane pods are Running

Yet:
- `kubectl get pods` takes seconds
- Deployments apply slowly
- Controllers lag
- HPA reacts late
- Everything feels “heavy”

This is **API server latency**, not downtime.

---

## First misleading check

```bash
curl -k https://<api-server>:6443/healthz
```

```text
ok
```

Interpretation:
- API server is alive
- This does NOT measure performance

---

## Symptom shows up in kubectl latency

```bash
time kubectl get pods -A
```

Example:
```text
real    0m4.832s
```

A healthy cluster usually responds in <500ms.

---

## What the API server is actually doing

For every request, API server:
- authenticates
- authorizes (RBAC)
- validates
- talks to etcd
- serializes objects
- sends response

Latency anywhere here slows **everything**.

---

## Common hidden causes

- etcd latency
- too many API objects
- heavy watches
- large CRDs
- slow disks on control plane
- overloaded controllers

API server is CPU-light but **I/O sensitive**.

---

## Check API server metrics (quick signal)

```bash
kubectl get --raw /metrics | grep apiserver_request_duration_seconds_bucket | head
```

If high percentiles are large:
- requests are slow
- control plane is overloaded

---

## Check etcd health (most common root cause)

```bash
kubectl exec -n kube-system etcd-<node> -- etcdctl endpoint health
```

Example:
```text
https://127.0.0.1:2379 is healthy: took = 2.8s
```

Interpretation:
- etcd is “healthy”
- but **slow**
- API server waits on it

---

## Disk latency confirms it

On control plane node:

```bash
iostat -x 1
```

Example:
```text
Device   r_await w_await
sda      45.00   120.00
```

Interpretation:
- etcd writes are slow
- API server blocks
- kubectl feels slow

---

## Why SSH and health checks still work

- Health checks are cheap
- SSH uses existing connections
- Simple reads succeed

But:
- LIST operations are heavy
- WATCH updates are expensive
- Controllers pile up work

---

## Kubernetes amplification

In large clusters:
- thousands of pods
- thousands of endpoints
- many CRDs
- many controllers

Every `kubectl get`:
- hits etcd
- serializes large objects
- stresses the API server

---

## What NOT to assume

❌ API server up = healthy  
❌ No errors = no problem  
❌ CPU low = control plane fine  

Latency kills before errors appear.

---

## Correct remediation

- Reduce object count
- Clean up unused resources
- Prune old CRDs
- Reduce controller spam
- Improve disk IOPS on control plane
- Separate etcd from other workloads

---

## Validate improvement

```bash
time kubectl get pods -A
```

Healthy:
```text
real    0m0.300s
```

---

## SRE rules

- API server latency affects everything
- etcd speed = cluster speed
- Health checks lie
- Control plane is I/O bound

---

## Failure pattern

```text
kubectl slow
api reachable
no errors
controllers lag
etcd slow
disk latency high
```

This is **API server slowness**, not a control plane outage.

