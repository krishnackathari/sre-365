# Senior SRE Kubernetes Interview – Debugging Latency & Pod Restarts

## Scenario

A user-facing service is deployed as a Kubernetes `Deployment` with 10 replicas behind a `Service`.

Symptoms:
- Latency spikes
- Increased error rate
- Pods restarting intermittently
- Cluster appears healthy

---

## Senior SRE Response (Interview-Ready)

> **Goal:** Identify the failure mode (application, resource, or orchestration)  
> **Rule:** Observe first. Do not change the system while debugging.

---

## Step 1: Check Deployment & Pod Health

### Commands
```bash
kubectl get deploy
kubectl get pods
```

### Example Output
```text
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
payments-api      8/10    10            8           15d
```

```text
NAME                               READY   STATUS             RESTARTS   AGE
payments-api-7c9d8f6b5c-abc12      1/1     Running            0          2h
payments-api-7c9d8f6b5c-def34      0/1     CrashLoopBackOff   6          15m
payments-api-7c9d8f6b5c-ghi56      1/1     Running            3          1h
payments-api-7c9d8f6b5c-jkl78      0/1     Running            5          20m
```

### What I’m Looking For
- Restart counts increasing
- Pods flapping between `Running` and `CrashLoopBackOff`
- `READY` not matching desired replicas

---

## Step 2: Describe a Restarting Pod (Identify Failure Mode)

### Command
```bash
kubectl describe pod payments-api-7c9d8f6b5c-def34
```

### Example Output – OOMKilled
```text
Last State:     Terminated
  Reason:      OOMKilled
  Exit Code:   137
Events:
  Warning  BackOff  2m    kubelet  Back-off restarting failed container
```

### Example Output – Probe Failure
```text
Warning  Unhealthy  1m    kubelet  Readiness probe failed: HTTP probe failed with statuscode: 500
Warning  Unhealthy  1m    kubelet  Liveness probe failed: connection refused
```

### Interpretation
- `OOMKilled` → memory pressure
- Probe failures → app slow, overloaded, or unhealthy
- Exit code `137` → container killed by kernel
- BackOff events → repeated crash loops

---

## Step 3: Validate Readiness vs Traffic

> **Key Insight:** Unready pods must never receive production traffic.

### Common Failure Pattern
- Liveness probe restarts pod
- Readiness probe never stabilizes
- Service continues routing traffic
- Latency and error rate spike

### Commands
```bash
kubectl get pods -o wide
kubectl describe svc payments-api
```

---

## Step 4: Check Node Health (After Pod Signals)

### Commands
```bash
kubectl get nodes
kubectl describe node ip-10-0-2-45
```

### Example Node Pressure
```text
Conditions:
  MemoryPressure   True
  DiskPressure     False
```

### Interpretation
- Pods restarting on same node
- Node is `Ready` but resource-starved
- Scheduler may amplify failures

---

## Step 5: Inspect Application Logs

### Commands
```bash
kubectl logs payments-api-7c9d8f6b5c-def34
kubectl logs payments-api-7c9d8f6b5c-def34 --previous
```

### Example Log Signals
```text
ERROR: database connection timeout
ERROR: request latency exceeded 5s
panic: unable to allocate memory
```

---

## Step 6: Production Safety Rules (Senior SRE Behavior)

### I Avoid:
- Restarting all pods
- Scaling blindly
- Editing configs mid-incident

### My Objective:
- Stabilize traffic
- Identify root cause
- Apply targeted fix

---

## One-Line Senior SRE Summary (Interview Gold)

> **“I debug Kubernetes incidents by identifying the failure mode first — application crash, resource pressure, or orchestration behavior — before making any changes.”**

---

## Mental Checklist (Memorize This)

- Are pods restarting?
- Why are they restarting?
- Are unready pods getting traffic?
- Is Kubernetes reacting or causing harm?
- How do I prevent cascading failures?

---

## Keywords Interviewers Love
- Golden signals
- Readiness vs liveness
- Failure modes
- Cascading restarts
- Traffic safety
- Observability first

