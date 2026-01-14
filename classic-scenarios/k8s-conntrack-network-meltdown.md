# classic-scenarios/k8s-conntrack-network-meltdown.md

# Kubernetes Network Meltdown: conntrack exhaustion

## Symptoms
- Random timeouts
- Services unreachable
- High retries
- Load average rising
- CPU not maxed

---

## Check conntrack usage
`cat /proc/sys/net/netfilter/nf_conntrack_count`

`cat /proc/sys/net/netfilter/nf_conntrack_max`

Example:
```
980432
1048576
```

> ~93% full → danger zone

---

## Confirm kernel drops
`dmesg | grep -i conntrack`

```
nf_conntrack: table full, dropping packet
```

---

## Why this causes load
- Packets dropped
- TCP retries
- LB retries
- Traffic multiplies
- Kernel work increases

---

## Related indicators
`netstat -s | grep -i backlog`

`cat /proc/net/softnet_stat`

---

## Kubernetes-specific reason
- Pod NAT
- Service NAT
- kube-proxy iptables
- Massive connection fanout

---

## SRE Rule
High load + retries + no clear CPU bottleneck
= conntrack until proven otherwise

---

## Commit
`git add classic-scenarios/k8s-conntrack-network-meltdown.md`

`git commit -m "Day 14: Kubernetes conntrack exhaustion causes network meltdown"`

`git push`

