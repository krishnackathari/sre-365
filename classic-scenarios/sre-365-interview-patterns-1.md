# SRE Interview Pattern Recognition Cheat Sheet (sre-365)

This file maps **symptoms → failure patterns → commands → root cause**.
Use it to respond fast in interviews with confidence and structure.

---

## 1. Pods slow but CPU is low

### Pattern
CPU throttling (cgroups)

### Think
- CPU limits
- Time slicing
- Forced pauses

### Commands
```bash
kubectl describe pod <pod>
cat /sys/fs/cgroup/cpu.stat
```

```text
nr_throttled > 0
throttled_time increasing
```

### Root cause
CPU limits enforce time budgets even when node CPU is idle.

---

## 2. High load average but CPU idle

### Pattern
I/O wait / blocked processes

### Commands
```bash
vmstat 1
```

```text
wa high
b > 0
```

```bash
ps -eo pid,state,comm | awk '$2=="D"'
```

### Root cause
Processes waiting on disk or network I/O inflate load average.

---

## 3. Disk not full but writes fail

### Pattern
Inode exhaustion

### Commands
```bash
df -h
df -i
```

```text
disk OK
inodes 100%
```

### Root cause
Too many small files exhaust filesystem metadata.

---

## 4. Random timeouts, ping works

### Pattern
conntrack table exhaustion

### Commands
```bash
cat /proc/sys/net/netfilter/nf_conntrack_count
cat /proc/sys/net/netfilter/nf_conntrack_max
dmesg | grep conntrack
```

### Root cause
Kernel cannot track new connections → packets dropped silently.

---

## 5. Node becomes NotReady but SSH works

### Pattern
Kubelet heartbeat missed

### Commands
```bash
kubectl describe node <node>
journalctl -u kubelet
vmstat 1
```

### Root cause
Resource pressure delays kubelet heartbeats.

---

## 6. Pod OOMKilled but no memory leak

### Pattern
Memory pressure via page cache

### Commands
```bash
kubectl describe pod <pod>
cat /proc/meminfo
cat memory.usage_in_bytes
```

### Root cause
Page cache + native memory hit container limit.

---

## 7. Services exist but traffic is slow

### Pattern
iptables rule explosion

### Commands
```bash
iptables -t nat -S | wc -l
time curl service
time curl pod-ip
```

### Root cause
Linear iptables rule traversal causes latency at scale.

---

## 8. kubectl works but is very slow

### Pattern
API server latency

### Commands
```bash
time kubectl get pods -A
kubectl get --raw /metrics
```

### Root cause
Control plane latency, often etcd or disk I/O.

---

## 9. etcd healthy but cluster hangs

### Pattern
Watch backlog / object churn

### Commands
```bash
etcdctl endpoint health
journalctl -u etcd
iostat -x 1
```

### Root cause
Watches fall behind due to disk latency or churn.

---

## 10. Network slow without packet loss

### Pattern
Queueing delay

### Commands
```bash
ss -i dst <ip>
ss -s
tc -s qdisc show
```

### Root cause
Buffers fill → packets wait → latency increases without drops.

---

## 11. DNS-related slowness

### Pattern
DNS amplification / ndots

### Commands
```bash
cat /etc/resolv.conf
kubectl top pod -n kube-system
```

### Root cause
ndots causes excessive DNS queries.

---

## 12. Pod evicted without CPU/memory pressure

### Pattern
Ephemeral storage exhaustion

### Commands
```bash
kubectl describe pod <pod>
df -h /var/lib/containerd
```

### Root cause
Container logs / writable layer filled node disk.

---

## 13. One pod causes cluster-wide pain

### Pattern
Noisy neighbor

### Commands
```bash
ss -ant | awk '{print $6}' | cut -d: -f1 | sort | uniq -c | sort -nr | head
kubectl get pods -A -o wide
```

### Root cause
Connection churn, retries, short-lived connections.

---

## 14. API server reachable but controllers lag

### Pattern
Control plane congestion

### Commands
```bash
kubectl get componentstatuses
kubectl get --raw /metrics
```

### Root cause
Controllers blocked on slow API responses.

---

## 15. Why ping lies

### Pattern
TCP-level failure, not ICMP

### Commands
```bash
ss -i
ss -s
```

### Root cause
TCP congestion, retransmits, queueing.

---

## 16. Kubernetes networking adds latency

### Pattern
Overlay overhead (VXLAN / IPIP)

### Commands
```bash
ip link show
tc qdisc show
```

### Root cause
Extra encapsulation, hops, MTU reduction.

---

## 17. Service slower than direct pod access

### Pattern
kube-proxy path cost

### Commands
```bash
time curl service
time curl pod-ip
```

### Root cause
iptables or proxy traversal overhead.

---

## 18. Everything green but users complain

### Pattern
Latency-based failure

### Think
- Tail latency
- Queue depth
- Retries

### Root cause
Systems fail by waiting, not crashing.

---

## Golden Interview Rule

When asked any question:
1. Clarify the symptom
2. Name the pattern
3. Say the first command you’d run
4. Explain why that command proves the root cause

This signals **senior SRE thinking**.

---

## Final mental hook

> Green dashboards don’t mean healthy systems.  
> Latency, queues, and kernel limits tell the truth.

