# Services Failing Randomly (File Descriptor Exhaustion)

## Why this issue is confusing

Linux systems can look completely healthy:
- CPU is low
- Memory is available
- Disk space is fine
- No obvious errors in metrics

Yet applications start failing with random errors:
- connection failures
- inability to open files
- services refusing requests

This happens when the system hits a **file descriptor limit**, not a resource limit.

---

## What file descriptors are

A file descriptor (FD) is required for **everything** an app opens:
- files
- sockets
- pipes
- network connections
- logs

If a process runs out of FDs, it **cannot do anything new**, even if resources are available.

---

## Check current file descriptor usage

```bash
lsof | wc -l
```

Example:
```text
1048573
```

This shows the total number of open file descriptors on the system.

---

## Check per-process FD usage

```bash
lsof -p <PID> | wc -l
```

Example:
```text
65536
```

This process is hitting the limit.

---

## Check FD limits

```bash
ulimit -n
```

Example:
```text
1024
```

This is the **soft limit** for the shell or service.

---

## Check system-wide FD limits

```bash
cat /proc/sys/fs/file-max
```

Example:
```text
2097152
```

This is the **global kernel limit**.

---

## Symptoms you will see

Applications may log:
- `Too many open files`
- `EMFILE`
- `ENFILE`
- connection refused
- intermittent request failures

At the system level:
- services flap
- new connections fail
- existing connections continue working

---

## Reproduce the failure (safe test)

```bash
ulimit -n 50
```

```bash
for i in {1..100}; do exec 3<>/tmp/testfile$i; done
```

```text
bash: /tmp/testfile51: Too many open files
```

This confirms FD exhaustion behavior.

---

## The real signal

```bash
cat /proc/<PID>/limits
```

Example:
```text
Max open files            1024                4096                files
```

Interpretation:
- First value = soft limit
- Second value = hard limit
- Process cannot exceed the soft limit

---

## Common real-world causes

- Connection leaks (HTTP clients not closing sockets)
- Log file handles not released
- Thread pools creating sockets repeatedly
- High traffic + low ulimit
- Containers inheriting low FD limits

---

## Kubernetes-specific failure mode

In containers:
- FD limits come from the node
- Pods may inherit **very low defaults**
- One noisy pod can exhaust its own FDs

Check inside a pod:

```bash
ulimit -n
```

---

## Immediate remediation

Temporarily raise limits:

```bash
ulimit -n 65536
```

For systemd services:

```bash
LimitNOFILE=65536
```

---

## Permanent fix

- Fix FD leaks in application code
- Increase limits in:
  - `/etc/security/limits.conf`
  - systemd unit files
- Monitor FD usage per process

---

## SRE rules

- Low CPU does not mean healthy
- Random failures often mean **limits**, not load
- Always check:
  - FDs
  - inodes
  - memory reclaim
  - cgroup limits

---

## Failure pattern

```text
CPU looks fine
memory looks fine
disk looks fine
connections fail randomly
logs show EMFILE
```

This is **file descriptor exhaustion**, not a performance issue.

