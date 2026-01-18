# Pods Restarting Randomly (Ephemeral Storage Exhaustion in Kubernetes)

## Why this issue is confusing

In Kubernetes, pods can restart even when:
- CPU usage is low
- Memory usage is normal
- Node disk looks mostly empty
- No obvious application errors

Yet pods keep crashing with:
- `Evicted`
- `Exceeded local storage limit`
- `ContainerStatusUnknown`

This happens due to **ephemeral storage exhaustion**, not CPU or memory pressure.

---

## What ephemeral storage is

Ephemeral storage includes:
- container writable layer
- `/tmp` inside containers
- container logs
- emptyDir volumes (disk-backed)

It is **node-local** and **not persistent**.
When it fills up, Kubernetes evicts pods.

---

## First signal: pod eviction reason

```bash
kubectl get pod <pod-name> -o wide
```

Example:
```text
NAME        READY   STATUS    RESTARTS   NODE
api-pod     0/1     Evicted   5          node-1
```

Describe the pod:

```bash
kubectl describe pod <pod-name>
```

```text
Reason: Evicted
Message: The node was low on resource: ephemeral-storage.
```

This confirms the root cause category.

---

## Check ephemeral storage requests and limits

```bash
kubectl get pod <pod-name> -o jsonpath='{.spec.containers[*].resources}'
```

Example:
```text
{"limits":{"cpu":"500m","memory":"512Mi"}}
```

Interpretation:
- No ephemeral-storage requests or limits defined
- Pod can consume unlimited disk until eviction

---

## Check node-level ephemeral storage pressure

```bash
kubectl describe node <node-name>
```

Look for:

```text
Conditions:
  DiskPressure   True
```

And events like:
```text
Node is low on ephemeral-storage
```

This means **the node**, not just the pod, is under disk pressure.

---

## Identify what is consuming disk on the node

SSH into the node:

```bash
df -h
```

```bash
du -xh /var/lib/containerd | sort -h | tail -20
```

Common offenders:
- container logs
- large temporary files
- runaway emptyDir volumes

---

## Check container log size

```bash
ls -lh /var/log/containers | tail
```

Large or fast-growing log files are a common cause.

---

## Reproduce the failure (safe test)

Deploy a pod that writes endlessly:

```bash
kubectl run disk-writer --image=busybox -- /bin/sh -c "while true; do echo test >> /tmp/data; done"
```

Watch the pod:

```bash
kubectl get pod disk-writer -w
```

```text
disk-writer   Evicted
```

This demonstrates ephemeral storage eviction behavior.

---

## Immediate remediation

### Option A: Restart pods (temporary)
```bash
kubectl delete pod <pod-name>
```

### Option B: Clean node disk
- remove old images
- rotate logs
- delete unused containers

---

## Correct fix: set ephemeral storage limits

```yaml
resources:
  requests:
    ephemeral-storage: "500Mi"
  limits:
    ephemeral-storage: "1Gi"
```

This ensures:
- scheduler places pods correctly
- kubelet enforces limits
- pods are evicted before killing the node

---

## Kubernetes-specific gotchas

- emptyDir defaults to node disk
- logs count toward ephemeral storage
- one noisy pod can evict others
- monitoring often ignores disk pressure

---

## SRE rules

- Evictions ≠ crashes
- Disk pressure ≠ disk full
- Always set ephemeral-storage requests for prod pods
- Monitor node disk, not just container metrics

---

## Failure pattern

```text
CPU looks fine
memory looks fine
pods restart
status = Evicted
node shows DiskPressure
```

This is **ephemeral storage exhaustion**, not an application bug.

