# App Died Suddenly (OOMKilled vs Memory Pressure)

## Why this issue is confusing

Applications can fail in two very different memory-related ways:

- Sometimes the app is **killed instantly**
- Sometimes the app becomes **slow, stuck, or unstable**
- Sometimes memory graphs look fine
- Sometimes there is no obvious error in app logs

Both problems involve memory, but they are **not the same**.

Understanding the difference between **OOMKilled** and **memory pressure** is critical for SREs.

---

## Two very different memory failures

### OOMKilled (hard failure)
- Kernel forcefully kills a process
- Happens immediately
- Process disappears
- Clear signal once you know where to look

### Memory Pressure (soft failure)
- Kernel struggles to reclaim memory
- System slows down
- Processes block
- No process is killed (at first)

Most outages come from **memory pressure**, not OOMKills.

---

## OOMKilled: what actually happens

OOM = Out Of Memory

When:
- A process requests memory
- Kernel cannot satisfy the request
- Reclaim is too slow or impossible

The kernel chooses a process and **kills it** to save the system.

---

## How to detect OOMKilled (Kubernetes)

```bash
kubectl describe pod <pod-name>
```

Example:
```text
State:          Terminated
Reason:         OOMKilled
Exit Code:      137
```

Interpretation:
- Kernel killed the container
- Exit code 137 = SIGKILL
- App did not crash on its own

---

## How to detect OOMKilled (Linux node)

```bash
dmesg | grep -i oom
```

Example:
```text
Out of memory: Kill process 24567 (java) score 982 or sacrifice child
Killed process 24567 (java) total-vm:8123456kB, anon-rss:4096000kB
```

This is definitive proof of an OOM kill.

---

## Why OOMKilled is easy to debug

- One moment app is running
- Next moment it is gone
- Logs stop suddenly
- Kubernetes restarts it

It is loud, obvious, and fast.

---

## Memory pressure: the silent killer

Memory pressure happens when:
- RAM is almost full
- Kernel is constantly reclaiming memory
- Page cache is repeatedly evicted
- Disk I/O increases
- Processes block

But:
- No process is killed
- Apps stay alive
- System becomes very slow

---

## How to detect memory pressure (Linux)

```bash
free -m
```

Example:
```text
              total   used   free   buff/cache   available
Mem:           16000  14800    120        1080        300
```

Interpretation:
- Free memory is low (normal)
- Available memory is **dangerously low**
- Kernel has very little room to maneuver

---

## The real signal: vmstat

```bash
vmstat 1
```

Example:
```text
r  b   si   so   us  sy  id  wa
1  5    0    0    4   3  55  38
```

Interpretation:
- Multiple blocked processes (`b`)
- High I/O wait (`wa`)
- CPU idle but waiting
- No swapping yet

This is **memory pressure**, not OOM.

---

## Why memory pressure causes slowness

Under pressure:
- Kernel evicts page cache
- Disk reads increase
- Processes wait on disk
- CPU sits idle
- Latency explodes

This looks like:
> “CPU is free but nothing is fast”

---

## Kubernetes view: memory pressure without OOM

```bash
kubectl describe node <node-name>
```

Look for:
```text
MemoryPressure   True
```

Pods may:
- slow down
- timeout
- fail readiness probes

Without being killed.

---

## Why people confuse the two

Both involve memory.
Both can break apps.
But the behavior is completely different.

| Feature | OOMKilled | Memory Pressure |
|------|----------|----------------|
| App dies | ✅ | ❌ |
| Logs stop suddenly | ✅ | ❌ |
| System slows down | ❌ | ✅ |
| Disk I/O increases | ❌ | ✅ |
| Kernel kills process | ✅ | ❌ |

---

## What NOT to do

❌ Do not immediately increase memory limits  
❌ Do not restart blindly  
❌ Do not assume GC or app bug first  

You must identify **which failure mode** you are in.

---

## Correct remediation

### If OOMKilled
- Increase memory limit
- Fix memory leak
- Reduce heap size
- Tune application memory usage

### If memory pressure
- Reduce pod density
- Separate batch workloads
- Add RAM
- Reduce cache-heavy processes
- Watch `MemAvailable`, not `free`

---

## SRE rules

- OOMKilled is loud and fast
- Memory pressure is quiet and deadly
- High I/O wait with idle CPU = memory trouble
- Always check kernel signals before blaming the app

---

## Failure patterns

### OOMKilled
```text
pod restarts
exit code 137
dmesg shows OOM
```

### Memory pressure
```text
CPU idle
disk busy
requests slow
no crashes
```

These are **two different problems** with very different fixes.

