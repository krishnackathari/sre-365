# Memory Pressure (Linux SRE Notes)

## Why `free` is misleading
Linux uses RAM for:
- page cache
- filesystem metadata
- buffers

So "free" memory is often near zero even when the system is healthy.  
Always look at `available`.

---

## Check memory correctly

```bash
free -m
```

Example:
```text
              total   used   free   buff/cache   available
Mem:           16000  14000    200        1800        3200
```

Interpretation:
- `free = 200MB` looks scary  
- `available = 3200MB` means the system is healthy  

`available` is how much memory can be given to applications without causing I/O or stalls.

---

## The real signal: swapping & reclaim

```bash
vmstat 1
```

Important columns:
- `si` → swap in (disk → RAM)
- `so` → swap out (RAM → disk)
- `wa` → CPU waiting on I/O
- `r`  → runnable processes
- `b`  → blocked processes

Example:
```text
r  b   si   so   us  sy  id  wa
1  0  2048  4096  5   3  70  22
```

Interpretation:
- Memory pages are being written to disk (`so`)
- Pages are being read back (`si`)
- CPU is idle but waiting on I/O (`wa`)
- System feels slow  

This is **memory pressure**.

---

## What memory pressure means
When RAM is tight:
- Kernel evicts pages
- Writes them to swap
- Reads them back later
- Applications block

This causes:
- high latency
- high `wa`
- slow response
- eventual OOM kill

---

## Why OOM kill happens
OOM kill occurs when:
- available memory is ~0
- reclaim is too slow
- kernel cannot satisfy a memory request

The kernel kills a process to keep the system alive.

---

## SRE rules
- Always trust `available`, not `free`
- Any sustained `si` or `so` means memory is the bottleneck
- Swapping = performance collapse

---

## Memory failure pattern

```text
free looks OK
available is low
si / so > 0
wa > 0
CPU idle
system slow
```

This is memory-driven I/O, not a CPU or disk capacity problem.

