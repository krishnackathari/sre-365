# Everything Times Out but Network Is Up (conntrack Table Exhaustion)

## Why this issue is confusing

You observe:
- Pods are Running
- Nodes are Ready
- No packet loss
- CPU and memory look fine
- Services resolve

Yet:
- Requests randomly time out
- New connections fail
- Existing connections sometimes work
- Restarting pods helps temporarily

This happens when the **conntrack table is full**.

---

## What conntrack is (one line)

conntrack is the Linux kernel table that tracks **every network connection**.

If it fills up:
- new connections are dropped
- packets are silently ignored
- apps hang or time out

Nothing “crashes”.

---

## First misleading check

```bash
ping <service-ip>
```

```text
64 bytes from 10.96.0.12: time=0.4 ms
```

Interpretation:
- Network is reachable
- This does NOT test new TCP connections

---

## Real symptom: new connections fail

From inside a pod:

```bash
curl http://service.prod.svc.cluster.local
```

```text
curl: (28) Connection timed out
```

Retrying may succeed sometimes.

This randomness is a key signal.

---

## Check conntrack usage (source of truth)

On the node:

```bash
cat /proc/sys/net/netfilter/nf_conntrack_count
```

```text
262144
```

```bash
cat /proc/sys/net/netfilter/nf_conntrack_max
```

```text
262144
```

Interpretation:
- Table is full
- Kernel cannot track new connections
- Packets are dropped

---

## Kernel confirms the problem

```bash
dmesg | grep -i conntrack
```

Example:
```text
nf_conntrack: table full, dropping packet
```

This is definitive proof.

---

## Why Kubernetes makes this worse

In Kubernetes:
- every Service uses NAT
- kube-proxy creates many iptables rules
- each pod creates many short-lived connections
- retries multiply connection count

Each TCP connection consumes **one conntrack entry**.

---

## Common triggers

- High request rate
- Aggressive retries
- Short-lived HTTP connections
- DNS storms
- Load tests
- Misconfigured clients

One noisy pod can fill the table for the entire node.

---

## Why this looks like a network issue

Because:
- packets are dropped silently
- no TCP RST
- no ICMP error
- apps just wait

Monitoring stays green.

---

## Confirm connection churn

```bash
conntrack -L | wc -l
```

```text
262144
```

Or sample:

```bash
conntrack -L | head
```

You’ll see thousands of similar entries.

---

## Why restarting pods “fixes” it

Restarting:
- closes connections
- frees conntrack entries
- relieves pressure briefly

But traffic refills the table.

This is why the problem keeps coming back.

---

## What NOT to do

❌ Restart all pods repeatedly  
❌ Scale apps blindly  
❌ Blame DNS or the load balancer  

Those treat symptoms, not cause.

---

## Correct remediation

Immediate:
- Reduce retries
- Reduce connection churn
- Kill the noisy pod
- Drain the affected node if needed

Permanent:
- Increase conntrack table size
- Enable keep-alives
- Rate-limit clients
- Separate noisy workloads
- Monitor conntrack usage

---

## Increase conntrack limit (example)

```bash
sysctl -w net.netfilter.nf_conntrack_max=524288
```

Also increase hash size (requires reboot or early boot config).

---

## SRE rules

- Conntrack exhaustion breaks networking silently
- Ping success is meaningless here
- Random timeouts are a strong signal
- One node can poison many pods

---

## Failure pattern

```text
pods running
network reachable
random timeouts
retries increase
conntrack full
dmesg shows drops
```

This is **conntrack exhaustion**, not a service or DNS failure.

