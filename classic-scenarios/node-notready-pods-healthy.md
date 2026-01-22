# Node NotReady but Pods Look Healthy (Control Plane vs Kubelet Reality)

## Why this issue is confusing

You see something alarming:

- `kubectl get nodes` shows **NotReady**
- Alerts fire
- Cluster health looks bad

But at the same time:
- Pods are still running
- Applications are serving traffic
- No obvious crashes
- CPU and memory look normal

This creates panic and confusion.

---

## First symptom: node status

```bash
kubectl get nodes
```

Example:
```text
NAME        STATUS     ROLES    AGE   VERSION
node-1      NotReady   worker   42d   v1.28.1
```

Interpretation:
- Kubernetes control plane thinks the node is unhealthy
- This does **not** immediately mean workloads are dead

---

## What “NotReady” actually means

A node is marked **NotReady** when:
- kubelet stops reporting status
- or kubelet reports unhealthy conditions
- or node heartbeats stop reaching the API server

Important:
> **NotReady means control plane lost confidence in the node**
> not that the node is powered off.

---

## Step 1: Check node conditions

```bash
kubectl describe node node-1
```

Look at **Conditions**:

```text
Conditions:
  Ready            False
  MemoryPressure   False
  DiskPressure     False
  PIDPressure      False
  NetworkUnavailable False
```

And the message:

```text
Kubelet stopped posting node status
```

Interpretation:
- Node resources are fine
- kubelet communication is the issue

---

## Step 2: Why pods may still be running

Key Kubernetes behavior:

- Pods are **local processes**
- Once started, they do not depend on the API server
- Containers keep running even if control plane is unreachable

So:
> **Apps can keep serving traffic while the node is NotReady**

This is expected behavior.

---

## Step 3: Check kubelet health on the node

SSH into the node:

```bash
systemctl status kubelet
```

Example:
```text
Active: failed (Result: exit-code)
```

Or:

```text
Active: active (running)
```

If kubelet is:
- crashed
- hung
- blocked

The node will go NotReady.

---

## Step 4: Common real-world causes

### 1️⃣ Disk pressure or full disk
Even temporary disk issues can:
- block kubelet
- prevent heartbeats

```bash
df -h
```

```bash
df -i
```

---

### 2️⃣ Container runtime issues
containerd or docker may be stuck:

```bash
systemctl status containerd
```

If runtime is unhealthy:
- kubelet cannot manage pods
- node goes NotReady

---

### 3️⃣ Network connectivity issues
Node cannot reach API server:

```bash
curl -k https://<api-server>:6443/healthz
```

If this fails:
- kubelet cannot report status
- control plane marks node NotReady

---

### 4️⃣ High load / starvation
Extreme cases:
- CPU starvation
- memory pressure
- kernel stalls

Kubelet misses heartbeats and gets marked unhealthy.

---

## Step 5: Check node events (control plane view)

```bash
kubectl get events --field-selector involvedObject.kind=Node
```

Example:
```text
NodeNotReady
NodeNotReady
NodeNotReady
```

Repeated events confirm this is not a transient blip.

---

## Step 6: What Kubernetes will do next

After a grace period:
- Scheduler stops placing new pods on the node
- Existing pods may continue running
- Eventually pods may be evicted (depending on config)

This is Kubernetes protecting the cluster.

---

## What NOT to assume

❌ Node NotReady does NOT mean:
- pods are dead
- app is broken
- immediate restart is required

❌ Restarting pods blindly can:
- cause outages
- drop traffic
- make things worse

---

## Correct response order (on-call playbook)

1. Confirm pods are still serving traffic
2. Check node conditions and events
3. Inspect kubelet and container runtime
4. Verify disk, inode, and memory health
5. Restore kubelet or node connectivity
6. Let Kubernetes reschedule only if needed

---

## SRE rules

- Control plane view ≠ data plane reality
- NotReady is about **communication**, not power
- Pods can run fine on a NotReady node
- Always check kubelet before touching workloads

---

## Failure pattern

```text
kubectl get nodes → NotReady
pods still running
traffic still flowing
kubelet unhealthy or disconnected
```

This is a **node–control-plane desync**, not an application outage.

