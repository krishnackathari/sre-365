# classic-scenarios/autoscaling-worsens-load.md

# CPU Autoscaling That Makes Production Slower

## Live Symptom (from real system)

`uptime`
```
08:31:04 up 151 days, load average: 11.93, 10.96, 10.77
```

`nproc`
```
4
```

System has **4 CPUs** but **~12 runnable tasks** → CPU run-queue pressure.

---

## CPU looks "busy"

`top`
```
%Cpu(s):  6.8 us, 58.4 sy, 25.1 hi, 8.9 si, 0.6 id
```

Meaning:
- `us` = user/app work (low)
- `sy` = kernel work (very high)
- `hi` = hardware interrupts (very high)
- `si` = software interrupts (very high)

CPU is busy, but not running application code.

---

## Disk + I/O load

`iostat -x 1`
```
Device    r/s    w/s   r_await   aqu-sz   %util
nvme0n1   252    100     16ms      4.7      71
```

This means:
- ~350 IOPS
- Disk queue ~5
- Disk busy 70% of time

The kernel is spending CPU cycles handling:
- I/O interrupts
- filesystem
- container storage
- networking

---

## Check for blocked (D-state) processes

`ps -eo pid,state,comm | awk '$2=="D"'`
```
(no output)
```

No blocked tasks.  
Everything is runnable but waiting for CPU.

---

## Why Kubernetes HPA makes this worse

HPA rule:
```
scale if CPU > 70%
```

Here CPU is high because:
- kernel is busy with I/O + interrupts
- not because apps need more CPU

Scaling adds:
- more pods
- more disk I/O
- more network traffic
- more kernel work

So load and latency increase.

---

## Root cause

Kernel CPU saturation from:
- disk I/O
- network interrupts
- containers
- overlay filesystems

Not application compute.

---

## SRE rule

Never autoscale on CPU when CPU is consumed by kernel or I/O.

Scale:
- nodes
- disks
- networking

Not pods.

---

