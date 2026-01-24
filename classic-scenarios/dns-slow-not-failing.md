# DNS Works but Everything Is Slow (DNS Latency Without Failure)

## Why this issue is confusing

During an incident you see:

- Pods are Running
- No restarts
- No obvious errors
- DNS queries succeed

Yet:
- Applications are slow
- Requests time out
- External calls feel random
- Retries increase latency

This happens when **DNS is slow, not broken**.

---

## Why DNS latency hurts more than DNS failure

Applications rely on DNS for:
- service discovery
- external APIs
- databases
- caches
- auth services

DNS lookup happens:
- on startup
- on reconnect
- on retries

Slow DNS = every request waits.

---

## First check (misleading)

```bash
nslookup kubernetes.default
```

Example:
```text
Server: 10.96.0.10
Address: 10.96.0.10#53

Name: kubernetes.default.svc.cluster.local
Address: 10.96.0.1
```

Interpretation:
- DNS works
- This does NOT mean DNS is fast

---

## The real signal: DNS latency

```bash
dig kubernetes.default.svc.cluster.local
```

Example:
```text
;; Query time: 180 ms
```

Interpretation:
- 180ms per lookup
- Apps doing many lookups will feel slow
- Retries amplify the delay

---

## Why this causes timeouts

Application flow:
1. Request starts
2. DNS lookup blocks
3. TCP connect waits
4. Request times out

Even if:
- DNS eventually responds
- No errors are logged

Latency compounds.

---

## Kubernetes-specific amplification

In Kubernetes:
- Every pod uses CoreDNS
- CoreDNS runs as pods
- DNS traffic goes through overlays
- Node density increases contention

Each adds:
- queues
- RTT
- CPU pressure

---

## CoreDNS under load (common cause)

```bash
kubectl top pod -n kube-system
```

Example:
```text
coredns-abc   CPU: 180m   MEM: 140Mi
```

Interpretation:
- CoreDNS is busy
- Queries queue up
- Latency increases

---

## Hidden issue: retries and ndots

Default resolver behavior:
- multiple retries
- multiple search domains

Example:
```text
ndots:5
```

This causes:
- multiple DNS queries per lookup
- extra delay under load

---

## Confirm from inside a pod

```bash
time nslookup google.com
```

Example:
```text
real    0m0.42s
```

Interpretation:
- DNS alone took 420ms
- App hasn’t even connected yet

---

## Why monitoring misses this

Dashboards show:
- DNS availability
- Pod health
- No errors

They miss:
- DNS latency
- queue depth
- tail latency
- retries

So everything looks “green”.

---

## What NOT to assume

❌ DNS success ≠ fast DNS  
❌ No errors ≠ healthy DNS  
❌ CoreDNS running ≠ responsive  

---

## Correct remediation

- Increase CoreDNS replicas
- Allocate more CPU to CoreDNS
- Reduce pod density per node
- Tune resolver settings (ndots)
- Cache aggressively in apps where possible

---

## SRE rules

- DNS latency is application latency
- Slow DNS breaks retries and timeouts
- Kubernetes amplifies DNS issues
- Always measure query time, not success

---

## Failure pattern

```text
pods running
dns resolves
queries slow
apps time out
retries increase
latency spikes
```

This is **DNS latency**, not DNS failure.

