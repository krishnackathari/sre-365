# Controllers Are Running but Nothing Progresses (kube-controller-manager Backlog)

## Why this issue is confusing

You observe:
- kube-controller-manager pod is Running
- API server is reachable
- etcd is healthy
- No crashes or restarts

Yet:
- Deployments stall
- Pods take long to appear
- Endpoints update slowly
- HPA lags
- Node status updates are delayed

This happens when **controllers are overwhelmed by backlog**, not because they are down.

---

## What kube-controller-manager actually does

Controllers:
- watch API objects
- react to changes
- reconcile desired vs actual state
- write updates back to the API server

If their event queues back up:
- reconciliation slows
- cluster “feels frozen”

---

## First misleading check

```bash
kubectl get pods -n kube-system | grep kube-controller-manager
```

```text
kube-controller-manager-xyz   Running
```

Interpretation:
- Process is alive
- This does NOT mean it is keeping up

---

## Symptom shows up as reconcile latency

```bash
time kubectl rollout status deploy <app>
```

```text
Waiting for deployment "api" rollout to finish: 1 out of 3 new replicas are available...
```

This hangs far longer than expected.

---

## Prove controller backlog via metrics

```bash
kubectl get --raw /metrics | grep workqueue_depth | head
```

Example:
```text
workqueue_depth{queue="endpoint"} 12345
```

Interpretation:
- Controller queues are deep
- Work is not being processed fast enough

---

## Why this happens

- Too many watches
- Too many endpoints changing
- Flapping pods
- Aggressive resync periods
- CRDs with controllers that churn
- Control plane CPU/I/O pressure

Controllers are **event-driven** — bursts overwhelm them.

---

## Why scaling apps makes it worse

Scaling creates:
- endpoint changes
- service updates
- pod status updates
- node updates

Each update creates controller work.

This amplifies backlog.

---

## Check controller logs for lag

```bash
kubectl logs -n kube-system kube-controller-manager-<node> | tail
```

Look for:
```text
slow event processing
context deadline exceeded
```

These confirm lag.

---

## What NOT to do

❌ Restart controller repeatedly  
❌ Scale workloads blindly  
❌ Increase resync frequency  
❌ Ignore queue depth  

These increase backlog.

---

## Correct remediation

Immediate:
- Pause flapping deployments
- Reduce churn (stop jobs / HPAs temporarily)
- Stabilize workloads

Permanent:
- Reduce endpoint churn
- Increase controller resources
- Reduce CRD resyncs
- Scale control plane
- Isolate noisy controllers

---

## Validate recovery

```bash
kubectl get --raw /metrics | grep workqueue_depth | head
```

Healthy:
```text
workqueue_depth{queue="endpoint"} 0
```

Rollouts complete normally.

---

## SRE rules

- Controllers fail by falling behind
- Running ≠ healthy
- Queue depth tells the truth
- Churn kills control planes

---

## Failure pattern

```text
controllers running
deployments hang
endpoints slow
queue depth high
churn present
```

This is **controller backlog**, not an API outage.

