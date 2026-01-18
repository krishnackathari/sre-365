# Ephemeral Storage vs PVC (ELI5)

## Mental model (kid version)
Imagine a **node** is a **computer classroom**.

Pods/containers are like **kids working at desks**.

Some storage is **temporary** (goes away when you leave).  
Some storage is **permanent** (stays even if you leave).

---

## 1) Container FS (Container Filesystem)
**What it is:**  
Each container gets a small **notebook** to write in.

**Examples inside container:**
- `/tmp/file.txt`
- `/app/cache/`
- `/var/run/app.pid`

**Where it lives on the node:**  
Usually under:
- `/var/lib/containerd` (container runtime storage)

**What happens on restart:**  
When the container dies/restarts → the notebook is thrown away.

**Kid version:**  
Container FS = **scratch paper** (temporary)

✅ Counts as **ephemeral storage**

---

## 2) emptyDir (disk)
**What it is:**  
A pod gets a **temporary toy box** (scratch folder) on the node disk.

```yaml
emptyDir: {}
```

**Used for:**
- temp files
- downloads
- processing scratch space

**Where it lives:**  
On the node disk (often ends up under container runtime storage paths).

**What happens on pod delete:**  
Pod goes away → toy box is emptied (data deleted).

**Kid version:**  
emptyDir (disk) = **temporary toy box** (temporary)

✅ Counts as **ephemeral storage**

---

## 3) Container logs
**What it is:**  
Everything the app prints to stdout/stderr is saved as **log files**.

**Where it lives on the node:**
- Real log files: `/var/log/pods`
- Symlinks for convenience: `/var/log/containers`

**Why logs can be dangerous:**
- Can grow forever if not rotated
- One noisy pod can fill node disk
- Can trigger pod evictions

**Kid version:**  
Logs = **teacher writing everything you say on paper**  
Paper pile can fill the room.

✅ Counts as **ephemeral storage**

---

## 4) PVC (Persistent Volume Claim)
**What it is:**  
A PVC is a **school locker** outside the classroom.

- You can leave and come back tomorrow
- Your stuff is still there

**Used for:**
- databases
- user uploads
- long-term app state

**Where it lives:**  
Usually on **external storage** (not node disk), e.g.:
- cloud disks (EBS)
- NFS
- Ceph

On the node you only see a **mount point**, not the actual storage.

**Kid version:**  
PVC = **locker** (permanent)

❌ Does NOT count as ephemeral storage

---

## What “ephemeral storage” means
Ephemeral storage = **temporary stuff pods write on the node’s hard disk**.

Includes:
- container filesystem
- emptyDir (disk)
- container logs

---

## Ephemeral vs PVC (simple table)

| Thing | Temporary? | Lives on node disk? | Deleted on restart? |
|------|------------|---------------------|---------------------|
| Container FS | ✅ | ✅ | ✅ |
| emptyDir (disk) | ✅ | ✅ | ✅ |
| Container logs | ✅ | ✅ | ✅ |
| PVC | ❌ | ❌ | ❌ |

---

## Why Kubernetes evicts pods
When node disk gets full (DiskPressure), kubelet evicts pods to protect the node.

PVC data is usually safe (external).  
Ephemeral storage is what typically fills the node.

---

## One-line summary
Ephemeral storage is temporary node-local disk usage by pods.  
PVC is permanent storage that survives pod restarts.

