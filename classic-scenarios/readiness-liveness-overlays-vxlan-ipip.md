# Readiness & Liveness Probes in Overlay Networks (VXLAN / IPIP)

## Why this matters

In Kubernetes clusters using **VXLAN or IPIP**, probes can fail even when:
- the app is healthy
- the pod is running
- there is no packet loss

This leads to:
- pods marked Unready
- traffic suddenly stopping
- confusing “random” outages

The reason is **extra network hops and latency**, not crashes.

---

## Quick recap: Readiness vs Liveness

### Readiness probe
Answers:
> “Can this pod accept traffic **right now**?”

- Failing readiness:
  - pod stays running
  - pod is removed from Service endpoints
  - traffic stops

---

### Liveness probe
Answers:
> “Is the process still alive?”

- Failing liveness:
  - pod is restarted
  - container is killed and recreated

---

## What VXLAN / IPIP change

Overlay networks add **extra steps** to every packet.

### Pod → Pod path with overlay
```text
Pod
→ veth
→ Linux bridge
→ VXLAN/IPIP encapsulation
→ host NIC
→ network
→ host NIC
→ VXLAN/IPIP decapsulation
→ Linux bridge
→ veth
→ Pod
```

Each step adds:
- queueing
- buffering
- scheduling delay

Even with **zero packet loss**.

---

## Why readiness probes fail first

Readiness probes usually:
- run frequently
- have short timeouts (1s is common)
- use HTTP

Example:
```yaml
timeoutSeconds: 1
periodSeconds: 5
```

With overlays:
- RTT increases
- response time increases slightly
- probe times out

Result:
> **Pod is slow, not dead — but marked Unready**

---

## Why liveness probes often survive

Liveness probes:
- run less frequently
- tolerate slower responses
- often hit very simple endpoints

So:
- process responds eventually
- liveness passes
- pod keeps running

This creates the pattern:
> Pod running, but no traffic

---

## Overlay + load = amplification

When traffic increases:
- overlay queues grow
- RTT increases
- TCP backs off
- app threads get busy

Then:
1. App responds slower
2. Readiness probe times out
3. Pod removed from Service
4. Remaining pods get more traffic
5. They slow down too
6. More readiness failures

This is a **cascade failure**.

---

## Why this is invisible in monitoring

Most dashboards show:
- packet loss
- CPU
- memory

They do NOT show:
- RTT variance
- queue depth
- probe timeout behavior
- readiness endpoint latency

So everything looks “green”.

---

## What NOT to do

❌ Do not disable readiness probes  
❌ Do not switch readiness → liveness  
❌ Do not restart pods blindly  

That hides the problem and removes protection.

---

## Correct probe design for VXLAN / IPIP

### 1️⃣ Separate endpoints
- `/live` → process exists
- `/ready` → capacity check

---

### 2️⃣ Increase readiness tolerance
```yaml
timeoutSeconds: 5
failureThreshold: 3
```

This allows:
- transient latency
- overlay delays
- brief load spikes

---

### 3️⃣ Make readiness cheap
Readiness endpoint should:
- avoid DB calls
- avoid external dependencies
- check internal queue depth only

---

## When this problem is MOST common

- VXLAN / IPIP clusters
- High pod density per node
- Bursty traffic
- Low readiness timeouts
- CPU throttling or queueing present

---

## SRE rules

- Overlay networks add latency, not loss
- Readiness fails before liveness
- Slow ≠ dead
- Probes must match network reality

---

## Failure pattern

```text
pods running
no restarts
overlay networking
RTT increases
readiness fails
endpoints empty
traffic drops
```

This is **overlay-amplified readiness failure**, not an app crash.

