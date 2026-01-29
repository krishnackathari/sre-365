# Node Becomes NotReady but Server Is Up (Kubelet Heartbeat Lost)

## Why this issue is confusing

You observe:
- Node suddenly shows `NotReady`
- Pods get rescheduled
- Alerts fire
- But the server is reachable via SSH
- Disk, CPU, memory are not maxed out

After some time:
- Node becomes Ready again
- Issue repeats under load

This happens when **kubelet misses heartbeats**, not because the node crashed.

---

## What “NotReady” actually means

`NotReady` does NOT mean:
- server is down
- kernel panicked
- hardware failed

It means:
> **The control plane did not hear from kubelet in time**

Kubernetes depends on **regular kubelet heartbeats**.

---

## First signal: node condition

```bash
kubectl get nodes
```

Example:
```text
NAME       STATUS     ROLES    AGE   VERSION
worker-1   NotReady   <none>   90d   v1.28.1
```

---

## Confirm condition details

```bash
kubectl describe node worker-1
```

Look for:
```text
Conditions:
  Ready   False   KubeletNotReady   Kubelet stopped posting node status
```

This is the key message.

---

## Why kubelet heartbeats fail

Kubelet is a **normal Linux process**.

If the node is under pressure:
- CPU starvation
- I/O wait
- memory reclaim
- disk pressure

kubelet:
- gets scheduled late
- cannot respond in time
- misses its heartbeat window

The node is alive — kubelet is delayed.

---

## Common trigger: I/O wait

```bash
vmstat 1
```

Example:
```text
r  b   si  so  us  sy  id  wa
1  7    0   0   4   2  35  59
```

Interpretation:
- CPU idle exists
- `wa` is high
- Processes are blocked on disk

kubelet is waiting too.

---

## CPU starvation version

```bash
top
```

Example:
```text
%Cpu(s):  2.0 us, 1.0 sy, 0.0 ni, 5.0 id, 92.0 wa
```

Even though CPU isn’t “busy”, kubelet can’t run on time.

---

## Memory pressure version

```bash
free -m
```

Example:
```text
available: 200MB
```

Interpretation:
- Heavy reclaim
- Page cache thrashing
- kubelet stalls

---

## Why SSH still works

SSH:
- already established
- uses existing TCP sessions
- tolerates latency

kubelet:
- must send **timely heartbeats**
- has strict deadlines

So:
```text
SSH works
kubectl shows NotReady
```

Both can be true.

---

## Control plane reaction

When heartbeats stop:
1. Node marked `NotReady`
2. Pods considered unhealthy
3. Pods rescheduled elsewhere
4. Load increases on other nodes
5. Cascade risk

---

## Why this happens under load only

Under normal load:
- kubelet runs fine
- heartbeats succeed

Under spike:
- queues grow
- kernel delays processes
- kubelet misses deadline

No crash needed.

---

## How to prove it’s kubelet delay

Check kubelet logs on the node:

```bash
journalctl -u kubelet --since "10 minutes ago"
```

Look for:
```text
Failed to update node status
context deadline exceeded
```

This confirms the cause.

---

## What NOT to do

❌ Reboot the node immediately  
❌ Blame the control plane  
❌ Restart all pods blindly  

This hides the real pressure source.

---

## Correct remediation

- Identify resource pressure (CPU / disk / memory)
- Reduce noisy workloads
- Isolate batch jobs
- Ensure kubelet has priority
- Drain node if unstable
- Fix root cause before uncordon

---

## SRE rules

- NotReady ≠ node down
- Kubelet is just another process
- Resource pressure breaks heartbeats first
- SSH success proves nothing

---

## Failure pattern

```text
node flips NotReady
ssh works
pods rescheduled
under load only
kubelet deadline missed
```

This is **kubelet heartbeat loss**, not a node crash.

