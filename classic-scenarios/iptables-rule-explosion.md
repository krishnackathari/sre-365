# Services Exist but Traffic Is Slow or Flaky (iptables Rule Explosion)

## Why this issue is confusing

You observe:
- Pods are Running
- Services resolve
- kube-proxy is healthy
- No packet loss
- Node CPU is not high

Yet:
- Requests are slow
- Latency increases with scale
- Some connections time out
- Behavior worsens as more Services are added

This happens when **iptables rules grow too large**.

---

## What iptables is doing in Kubernetes

In kube-proxy (iptables mode):
- Every Service creates rules
- Every Endpoint adds more rules
- Every packet is checked rule-by-rule

iptables works like:
> a long checklist evaluated **top to bottom**

More rules = more work per packet.

---

## First misleading check

```bash
kubectl get pods -n kube-system | grep kube-proxy
```

```text
kube-proxy-abc123   Running
```

Interpretation:
- kube-proxy is alive
- This does NOT mean traffic is fast

---

## Confirm iptables rule count

On the node:

```bash
iptables -t nat -S | wc -l
```

Example:
```text
185432
```

Interpretation:
- ~185k NAT rules
- Every packet walks this list

This is dangerous.

---

## Why performance degrades silently

iptables:
- does linear rule matching
- does not short-circuit early
- runs for every packet

So:
- latency increases
- CPU cost increases
- tail latency explodes

No errors. No drops. Just waiting.

---

## Prove packet traversal cost

Measure rule traversal indirectly:

```bash
perf stat -e cycles iptables -t nat -L >/dev/null
```

You’ll see:
- high CPU cycles
- proportional to rule count

---

## Why Kubernetes makes this worse

Kubernetes adds rules for:
- ClusterIP Services
- NodePort Services
- ExternalTrafficPolicy
- Session affinity
- Health checks
- Endpoints churn

Large clusters = massive rule sets.

---

## Common triggers

- Hundreds or thousands of Services
- High pod churn
- Frequent deploys
- Many short-lived Jobs
- Large node density

The rules never “crash” — they just grow.

---

## Why scaling pods makes it worse

Each new endpoint:
- adds more iptables rules
- increases traversal depth

So scaling **increases latency**, not decreases it.

---

## Confirm service path is slow

From a pod:

```bash
time curl http://service-name.namespace.svc.cluster.local
```

```text
real  0m0.420s
```

Then hit the pod directly:

```bash
time curl http://10.244.3.17:8080
```

```text
real  0m0.040s
```

Interpretation:
- Service path is 10x slower
- iptables is the difference

---

## Why monitoring misses this

Most dashboards track:
- packet loss
- bandwidth
- CPU usage

They ignore:
- rule count
- traversal cost
- tail latency

Everything looks “green”.

---

## What NOT to do

❌ Restart kube-proxy  
❌ Scale pods blindly  
❌ Increase CPU limits only  
❌ Ignore service count growth  

These don’t reduce rule depth.

---

## Correct remediation

- Switch kube-proxy to IPVS mode
- Reduce Service count
- Reduce Endpoint churn
- Separate noisy workloads
- Consider eBPF-based CNI (Cilium)

---

## Verify IPVS instead of iptables

```bash
kubectl get cm kube-proxy -n kube-system -o yaml | grep mode
```

```text
mode: ipvs
```

IPVS uses:
- hash tables
- O(1) lookup
- constant latency

---

## SRE rules

- iptables scales poorly with size
- Services are not free
- Latency grows silently
- kube-proxy “healthy” means nothing

---

## Failure pattern

```text
services work
pods healthy
latency increases
scales badly
iptables huge
traffic flaky
```

This is **iptables rule explosion**, not a network outage.

