# Pod OOMKilled but No Memory Leak (Memory Pressure, Not a Bug)

## Why this issue is confusing

You observe:
- Pod restarts
- Reason shows `OOMKilled`
- Application team says “no memory leak”
- Heap usage looks stable
- Code hasn’t changed

Yet:
- Pod keeps getting killed
- Happens under load
- Increasing limits “fixes” it temporarily

This happens because **OOM kill is about available memory, not just heap usage**.

---

## What OOMKilled really means

`OOMKilled` means:
> **The kernel could not satisfy a memory request and killed a process**

It does NOT mean:
- there is a memory leak
- the app heap kept growing
- GC failed

It means **memory pressure crossed a limit**.

---

## First signal: pod status

```bash
kubectl get pod <pod-name>
```

Example:
```text
STATUS: OOMKilled
RESTARTS: 1
```

---

## Confirm container limits

```bash
kubectl describe pod <pod-name>
```

Look for:
```text
Limits:
  memory: 512Mi
```

Interpretation:
- Container cannot exceed 512Mi
- Kernel enforces this strictly

---

## Why heap graphs can lie

Applications often show:
- stable heap
- GC working fine

But containers also use memory for:
- native allocations
- thread stacks
- buffers
- mmap’ed files
- page cache

These count toward the **container memory limit**.

---

## Page cache is the usual hidden consumer

Linux uses memory to cache files:
- reads
- writes
- logs
- temp files

This is **page cache**.

Page cache:
- is not heap
- grows under I/O
- counts against container limits

---

## Prove page cache pressure

Inside the pod (if allowed):

```bash
cat /proc/meminfo | egrep 'Cached|Buffers|MemAvailable'
```

Example:
```text
Cached:        180000 kB
Buffers:        12000 kB
MemAvailable:   20000 kB
```

Interpretation:
- Cache is large
- Available memory is low
- Kernel will kill something

---

## The real signal: cgroup memory

From the node (cgroup v1):

```bash
cat /sys/fs/cgroup/memory/kubepods/<qos>/<pod-id>/memory.usage_in_bytes
```

```bash
cat /sys/fs/cgroup/memory/kubepods/<qos>/<pod-id>/memory.limit_in_bytes
```

Example:
```text
usage: 536870912
limit: 536870912
```

Interpretation:
- Container hit its hard limit
- OOM kill is expected

---

## Why this happens under load

Under load:
- more requests
- more I/O
- more buffers
- more cache

Even if:
- heap is stable
- CPU is fine

Memory pressure increases until the limit is hit.

---

## Why increasing the limit “fixes” it

Raising the limit:
- gives more headroom
- delays the OOM
- does NOT remove the cause

That’s why the issue returns later.

---

## What NOT to assume

❌ OOMKilled = memory leak  
❌ Heap = total memory  
❌ GC graphs tell the full story  

---

## Correct remediation

- Set realistic memory requests and limits
- Leave headroom for:
  - native memory
  - page cache
  - buffers
- Reduce I/O in containers
- Move heavy writes to PVC
- Avoid logging to container filesystem
- Monitor `MemAvailable`, not just heap

---

## SRE rules

- OOM is about **available memory**
- Page cache counts
- Containers don’t get special treatment
- Limits without headroom cause pain

---

## Failure pattern

```text
heap stable
pod OOMKilled
under load
cache grows
limit hit
restart loop
```

This is **memory pressure via page cache**, not a memory leak.

