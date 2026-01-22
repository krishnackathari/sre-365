# Traffic Stops but Pods Are Running (Readiness Probes Failing Under Load)

## Why this issue is confusing

You see this during peak traffic:

- Pods are **Running**
- Containers are **not restarting**
- CPU and memory look acceptable
- No crashes in logs

Yet:
- Service suddenly stops receiving traffic
- Requests fail or time out
- Everything recovers when load drops

This happens because **readiness probes are failing**, not because the app is down.

---

## What readiness really means

Readiness does NOT mean:
- app is alive
- container is running

Readiness means:
> **“Can this pod accept traffic right now?”**

If readiness fails:
- Pod stays running
- Pod is removed from Service endpoints
- Traffic stops going to it

---

## First signal: endpoints disappear

```bash
kubectl get endpoints <service-name>
```

Example:
```text
SUBSETS:   <none>
```

Interpretation:
- Service has no ready pods
- Kubernetes removed all pods from traffic
- This is not a crash

---

## Confirm readiness probe failures

```bash
kubectl describe pod <pod-name>
```

Look for:

```text
Readiness probe failed: HTTP probe failed with statuscode: 503
```

Or:
```text
Readiness probe failed: context deadline exceeded
```

This is the smoking gun.

---

## Why readiness fails under load

Under high traffic:
- request queues grow
- threads are busy
- response times increase

But readiness probes:
- have short timeouts
- expect fast responses
- fail if app is slow, not dead

So:
> **App is overloaded, not broken**

---

## Common misconfiguration pattern

Example probe:

```yaml
readinessProbe:
  httpGet:
    path: /health
    port: 8080
  timeoutSeconds: 1
  periodSeconds: 5
```

Problem:
- Probe expects response in **1 second**
- Under load, app responds slower
- Probe fails → pod marked Unready

---

## Why liveness does NOT fail

Liveness checks:
- only ask “is the process alive?”
- often hit a simple endpoint
- tolerate slower responses

So:
- Pod stays running
- But is removed from traffic

This makes the issue feel random.

---

## Why this cascades into an outage

1. Traffic increases
2. Pods slow down
3. Readiness fails
4. Pods removed from Service
5. Fewer pods receive traffic
6. Remaining pods overload
7. More readiness failures
8. Traffic drops to zero

This is a **self-amplifying failure**.

---

## How to prove this during an incident

Watch readiness in real time:

```bash
kubectl get pods -w
```

You’ll see:
```text
READY 0/1 → 1/1 → 0/1
```

Pods flap between ready and unready.

---

## What NOT to do

❌ Do NOT increase replicas immediately  
❌ Do NOT restart pods blindly  
❌ Do NOT disable readiness probes  

Those hide the symptom, not the cause.

---

## Correct remediation

- Make readiness probes **reflect capacity**, not speed
- Increase:
  - `timeoutSeconds`
  - `failureThreshold`
- Use a **cheap readiness endpoint**
- Separate:
  - `/ready` (capacity check)
  - `/live` (process alive)

Example improvement:

```yaml
timeoutSeconds: 5
failureThreshold: 3
```

---

## SRE rules

- Readiness protects users, not pods
- Slow ≠ dead
- Readiness failures remove traffic instantly
- Probe tuning matters under load

---

## Failure pattern

```text
pods running
no restarts
traffic drops
endpoints empty
readiness failing
```

This is **readiness probe failure under load**, not an application crash.

