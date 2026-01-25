# Service Works from Node but Not from Pod (CNI / iptables Issue)

## Why this issue is confusing

You observe:
- Service is reachable from the node
- `curl` works on the node
- Pods are Running
- No obvious pod errors

Yet:
- Same Service fails from inside pods
- Requests time out or are refused
- Apps break but infra looks healthy

This happens when **pod networking is broken, not the Service itself**.

---

## First check (misleading but common)

From the node:

```bash
curl http://<service-cluster-ip>:<port>
```

Example:
```text
HTTP/1.1 200 OK
```

Interpretation:
- Service works
- kube-proxy rules exist
- Backend pods are reachable from node

But this does NOT prove pod networking works.

---

## Reproduce the failure from a pod

```bash
kubectl run test --rm -it --image=busybox -- sh
```

Inside the pod:

```bash
wget -qO- http://<service-cluster-ip>:<port>
```

Example:
```text
wget: bad address
```
or:
```text
Connection timed out
```

This confirms:
- Pod → Service path is broken

---

## Why node works but pod fails

Node traffic:
- originates from host network
- bypasses some CNI paths
- hits iptables directly

Pod traffic:
- goes through:
  - veth pairs
  - CNI bridge
  - overlay (VXLAN / IPIP)
  - iptables NAT

If **any layer breaks**, pods fail but nodes still work.

---

## Most common root causes

- CNI plugin crashed or misconfigured
- iptables rules partially missing
- kube-proxy unhealthy
- Overlay network broken on one node
- MTU mismatch (very common with VXLAN)
- Stale conntrack entries

---

## Check kube-proxy

```bash
kubectl get pods -n kube-system -l k8s-app=kube-proxy
```

Example:
```text
kube-proxy-xyz   Running
```

Running does NOT mean healthy.

Check logs:

```bash
kubectl logs -n kube-system kube-proxy-xyz
```

Look for:
```text
Failed to sync iptables rules
```

---

## Check CNI pod health

```bash
kubectl get pods -n kube-system
```

Look for:
- flannel
- calico
- cilium
- weave

Any pod:
- CrashLoopBackOff
- NotReady

is a red flag.

---

## Confirm pod routing table

Inside a pod:

```bash
ip route
```

If routes are missing or wrong:
- pod cannot reach Service CIDR
- DNS and Services fail

---

## MTU mismatch (silent killer)

VXLAN reduces MTU.

Symptoms:
- small packets work
- large packets fail
- DNS sometimes works
- HTTP hangs

Check node MTU:

```bash
ip link show eth0
```

If MTU not adjusted for overlay:
- packets fragment
- drops happen silently

---

## Why monitoring misses this

Monitoring shows:
- Service healthy
- Endpoints present
- Node connectivity OK

But misses:
- pod-to-pod path
- overlay failures
- veth drops

So everything looks “green”.

---

## What NOT to do

❌ Restart application pods first  
❌ Scale deployments blindly  
❌ Blame the app team  

This is infra-level.

---

## Correct remediation

- Restart CNI pods on affected node
- Restart kube-proxy if needed
- Fix MTU mismatch
- Drain and cordon bad node if unsure
- Verify pod-to-pod connectivity before uncordon

---

## SRE rules

- Node success ≠ pod success
- Always test from inside a pod
- CNI issues are node-local
- Overlay failures are subtle but fatal

---

## Failure pattern

```text
service works from node
fails from pod
dns flaky
overlay in use
one node affected
```

This is **pod networking failure**, not a Service or app issue.

