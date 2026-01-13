# classic-scenarios/network-latency-hidden-bottleneck.md

# When Everything Looks Healthy but the Service Is Slow

## Symptom

Users report:
- Slow API
- Hanging pages
- Timeouts

But:
- CPU is low
- Disk is fine
- Memory is available

---

## Check the network backlog

`s‌s -s`
```
Total: 10234 (kernel 0)
TCP:   8123 (estab 410, closed 7600, orphaned 0, synrecv 920, timewait 1200)
```

`synrecv > 0` means:
Clients are waiting for TCP handshake → server is overloaded.

---

## Check listen queue overflow

`s‌s -lnt`
```
State   Recv-Q  Send-Q   Local Address:Port
LISTEN  512     128      0.0.0.0:443
```

If:
```
Recv-Q > Send-Q
```
Connections are piling up and being dropped.

---

## Check socket errors

`netstat -s | egrep -i "listen|drop|overflow|reset"`
```
TCP: listen queue overflows 421
TCP: dropped connection requests 1193
```

This is **silent packet loss**.

---

## Kernel is overloaded, not the app

`top`
```
%Cpu(s):  6 us, 52 sy, 20 hi, 10 si
```

Network interrupts are consuming CPU.

---

## Why this causes slowness

Even if the app is fast:
- SYN packets drop
- TCP retries happen
- clients stall
- latency explodes

Load balancers retry → traffic multiplies → meltdown.

---

## How to confirm network saturation

`cat /proc/net/softnet_stat`
```
00000000 000004ff 00000010
```

Second column > 0 = packet drops inside kernel.

---

## Root cause

Kernel networking stack saturated by:
- too many connections
- too many packets
- too much interrupt load

---

## The SRE rule

> If latency is high and CPU is mostly system + interrupts, check the network stack.

---

## Commit

`git add classic-scenarios/network-latency-hidden-bottleneck.md`
`git commit -m "Add real incident: network stack saturation causes hidden latency"`
`git push`

