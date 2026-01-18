# Memory Looks Fine but System Is Slow (Page Cache Dominance)

## Why this issue is confusing

Systems can look healthy:
- Plenty of free disk
- CPU mostly idle
- No OOM kills
- Applications not crashing

Yet:
- Requests are slow
- Latency spikes
- Disk I/O wait increases
- Everything feels sluggish

This happens when the **Linux page cache dominates memory** and forces constant disk I/O.

---

## What page cache is (simple)

Linux uses free RAM to cache:
- file contents
- filesystem metadata
- disk blocks

This improves performance by avoiding disk reads.

Important rule:
> Linux will **aggressively use memory for cache**.

This is normal — until it isn’t.

---

## Why `free` output is misleading

```bash
free -m
```

Example:
```text
              total   used   free   buff/cache   available
Mem:           16000  14000    300        1700        900
```

Interpretation:
- `free` looks low (normal)
- `buff/cache` is high
- `available` is shrinking

The system is **running out of reclaimable memory**.

---

## The real signal: disk I/O wait

```bash
vmstat 1
```

Example:
```text
r  b   si   so   us  sy  id  wa
1  4    0    0    5   3  60  32
```

Interpretation:
- CPU idle is high
- `wa` (I/O wait) is high
- Processes are blocked waiting on disk

This is **memory-driven disk pressure**, not CPU load.

---

## Why page cache causes slowness

When memory is tight:
- Kernel tries to reclaim page cache
- Pages are evicted
- Disk reads increase
- Cache is refilled immediately
- Cycle repeats

Result:
- constant disk churn
- high latency
- blocked processes

---

## Confirm page cache dominance

```bash
cat /proc/meminfo | egrep 'Cached|Buffers|MemAvailable'
```

Example:
```text
Cached:         9800000 kB
Buffers:         600000 kB
MemAvailable:    800000 kB
```

Interpretation:
- Cache dominates memory
- Available memory is dangerously low
- Reclaim is constantly happening

---

## Common real-world causes

- Large file reads (logs, backups, analytics jobs)
- Containers reading huge datasets
- Repeated scans of big directories
- Cache thrashing workloads
- Too many pods on one node

---

## Why applications feel slow

- Threads block on disk reads
- CPU sits idle waiting
- Load average increases due to blocked tasks
- Response times spike

This looks like:
> “The system is idle but slow”

---

## What NOT to do

❌ Do NOT manually drop cache in production:
```bash
echo 3 > /proc/sys/vm/drop_caches
```

This:
- destroys performance
- causes massive I/O spikes
- makes things worse

---

## Correct remediation

- Reduce memory pressure:
  - add RAM
  - reduce pod density
- Limit cache-heavy workloads
- Separate batch jobs from latency-sensitive services
- Monitor:
  - `MemAvailable`
  - `wa`
  - blocked processes (`D` state)

---

## SRE rules

- Page cache is not free memory
- High cache + low available = danger
- I/O wait with idle CPU = memory pressure
- Never trust `free` alone

---

## Failure pattern

```text
CPU idle
memory looks used
disk busy
wa high
requests slow
```

This is **page cache dominance**, not a CPU or disk failure.

