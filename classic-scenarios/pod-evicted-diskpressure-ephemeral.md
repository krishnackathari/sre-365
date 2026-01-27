# Pod Evicted with DiskPressure (Ephemeral Storage, Not PVC)

## Why this issue is confusing

You observe:
- Pod gets Evicted
- Message says `DiskPressure`
- PVC has plenty of free space
- Disk looks fine with `df -h`

Yet:
- Pod is killed
- Node reports DiskPressure
- Workloads restart unexpectedly

This happens because **ephemeral storage is exhausted**, not PVC storage.

---

## What DiskPressure really means

DiskPressure is about **node-local ephemeral storage**, such as:
- container writable layers
- container logs
- emptyDir (disk-backed)
- kubelet working directories

It does NOT mean:
- PVC is full
- remote storage is out of space

PVCs are **separate from node disk pressure**.

---

## First signal: eviction message

```bash
kubectl describe pod <pod-name>
```

Example:
```text
Reason: Evicted
Message: The node was low on resource: ephemeral-storage.
```

Interpretation:
- Kubelet evicted the pod
- Triggered by node disk exhaustion
- Not related to PVC usage

---

## Check node DiskPressure

```bash
kubectl describe node <node-name>
```

Look for:
```text
Conditions:
  DiskPressure   True
```

Interpretation:
- Node is protecting itself
- Kubelet is reclaiming space
- Pods will be evicted

---

## Why `df -h` is misleading here

```bash
df -h
```

Example:
```text
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        100G   65G   30G  68% /
```

Interpretation:
- Disk is NOT full
- But DiskPressure can still occur

Because eviction is based on **kubelet thresholds**, not raw disk percentage.

---

## The real signal: ephemeral storage usage

```bash
df -h /var/lib/containerd
```

or:
```bash
du -sh /var/lib/containerd/*
```

Example:
```text
45G /var/lib/containerd/io.containerd.snapshotter.v1.overlayfs
```

Interpretation:
- Container writable layers are consuming disk
- Logs and temp files accumulate here

---

## Common causes of ephemeral storage exhaustion

- Containers writing logs aggressively
- Missing log rotation
- Large temporary files inside containers
- emptyDir volumes filling disk
- CrashLooping pods producing logs
- Debug builds dumping files

Any one can trigger DiskPressure.

---

## Why PVC data does NOT count

PVC data:
- Lives on external storage (EBS, NFS, Ceph, etc.)
- Mounted separately
- Not tracked as ephemeral storage

So:
- PVC can be 90% free
- Node can still evict pods

These are **independent storage paths**.

---

## How eviction selection works

Kubelet evicts:
1. BestEffort pods first
2. Burstable pods next
3. Guaranteed pods last

Pods without:
```yaml
resources:
  requests:
    ephemeral-storage:
```

are most vulnerable.

---

## Confirm pod ephemeral usage

```bash
kubectl describe pod <pod-name>
```

Look for:
```text
Requests:
  ephemeral-storage: 0
```

Interpretation:
- Pod has no guaranteed disk
- Easy eviction target

---

## What NOT to do

❌ Increase PVC size  
❌ Restart pods blindly  
❌ Ignore DiskPressure warnings  

These do not fix node disk exhaustion.

---

## Correct remediation

- Set `ephemeral-storage` requests and limits
- Enable log rotation
- Move heavy writes to PVC
- Clean up `/var/lib/containerd`
- Reduce noisy workloads
- Add node disk capacity if needed

---

## SRE rules

- DiskPressure ≠ PVC full
- Ephemeral storage is node-local
- Logs are the #1 killer
- Requests protect pods from eviction

---

## Failure pattern

```text
pod evicted
diskpressure true
df -h looks ok
logs exploding
pvc unused
```

This is **ephemeral storage exhaustion**, not persistent storage failure.

