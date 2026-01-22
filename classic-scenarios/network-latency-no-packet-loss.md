# Network Is Slow but No Packet Loss (Latency Without Drops)

## Why this issue is confusing

Everything looks fine:
- No packet loss
- Interfaces are up
- Bandwidth is available
- No errors in basic metrics

Yet:
- Requests are slow
- RPCs time out
- Tail latency spikes
- Users complain

This happens when **latency is introduced without packet loss**.

---

## First check: basic connectivity (misleading)

```bash
ping <target>
```

Example:
```text
64 bytes from 10.0.0.12: icmp_seq=1 ttl=64 time=0.4 ms
64 bytes from 10.0.0.12: icmp_seq=2 ttl=64 time=0.5 ms
```

Interpretation:
- No packet loss
- ICMP looks healthy
- This does NOT mean the network is healthy for applications

---

## Why ping lies

- ICMP packets are small
- Often prioritized
- Do not reflect:
  - TCP behavior
  - queueing delays
  - buffer pressure
  - retransmissions at higher layers

Ping tests **reachability**, not **performance**.

---

## The real symptom: tail latency

Applications experience:
- slow connects
- slow reads/writes
- timeouts under load

This is usually caused by:
- queueing
- bufferbloat
- retransmissions
- congestion control behavior

---

## Check TCP-level latency

```bash
ss -i dst <target-ip>
```

Example:
```text
cwnd:10 rtt:120/30 rto:400
```

Interpretation:
- RTT is high and variable
- Congestion window is small
- TCP is backing off even without packet loss

---

## Check retransmissions (hidden pain)

```bash
ss -s
```

Example:
```text
TCP:
  retransmits: 12489
```

Interpretation:
- Retransmissions exist
- Packet loss may be invisible to ICMP
- TCP is retrying silently

---

## Queueing delay: the usual culprit

When:
- bursts of traffic occur
- buffers fill up
- packets wait in queues

Latency increases even if:
- packets are eventually delivered
- no drops are recorded

This is **queueing delay**, not packet loss.

---

## Confirm queueing on the node

```bash
tc -s qdisc show dev eth0
```

Example:
```text
qdisc fq_codel 0: root refcnt 2 limit 10240p
  backlog 2048p 123456b
```

Interpretation:
- Backlog is non-zero
- Packets are waiting
- Latency increases under load

---

## Kubernetes-specific amplification

In Kubernetes:
- overlays (VXLAN)
- iptables rules
- service proxies
- node density

All add:
- hops
- queues
- latency

Even when packet loss is zero.

---

## Why monitoring misses this

Most dashboards track:
- packet loss
- bandwidth
- errors

But ignore:
- queue depth
- RTT variance
- retransmits
- tail latency

So everything looks “green”.

---

## What NOT to assume

❌ No packet loss ≠ healthy network  
❌ Ping success ≠ low latency  
❌ Bandwidth available ≠ fast requests  

---

## Correct remediation

- Reduce burstiness
- Enable fair queueing (`fq_codel`)
- Tune TCP buffers
- Separate noisy workloads
- Monitor latency, not just loss

---

## SRE rules

- Latency can kill without packet loss
- Queues cause slowness, not drops
- TCP tells the truth, ICMP does not
- Always measure tail latency

---

## Failure pattern

```text
ping OK
no packet loss
apps slow
RTT high
queues growing
```

This is **network latency without packet loss**, not a connectivity issue.

