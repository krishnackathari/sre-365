# Disk Not Full, But Writes Failing (Inode Exhaustion)

## Why `df -h` is misleading

Linux filesystems enforce two limits:

- disk blocks (how much data)
- inodes (how many files)

So disk space can be available while file creation fails  
if all inodes are exhausted.

This results in:

`No space left on device`

even when disk usage looks normal.

---

## Check disk usage (misleading)

```bash
df -h
```

Example:

```text
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        80G   42G   35G  55% /
```

Interpretation:

- Disk is only 55% used
- Plenty of free space exists
- Yet writes are failing

---

## Reproduce the failure

```bash
touch /tmp/testfile
```

```text
touch: cannot touch '/tmp/testfile': No space left on device
```

This confirms the issue is real and not application-specific.

---

## The real signal: inode usage

```bash
df -i
```

Example:

```text
Filesystem       Inodes   IUsed   IFree IUse% Mounted on
/dev/sda1       5242880 5242880       0  100% /
```

Interpretation:

- All inodes are exhausted
- No new files or directories can be created
- Disk space is irrelevant at this point

---

## What inode exhaustion means

Each of the following consumes one inode:

- file
- directory
- symlink

When inodes are exhausted:

- file creation fails
- log rotation breaks
- services fail to start
- containers and pods crash unexpectedly

This is a filesystem metadata exhaustion problem, not a disk capacity problem.

---

## Common causes

- Applications generating many small log files
- Missing or broken log rotation
- Temp file leaks in `/tmp` or `/var/tmp`
- Container logs under `/var/lib/docker`
- Package manager cache buildup

---

## Identify inode-heavy directories

```bash
du --inodes -d 2 / | sort -nr | head -20
```

Example:

```text
2100000 /var
1800000 /var/log
1500000 /var/log/app
```

This identifies the inode hotspot.

---

## Immediate remediation

```bash
find /var/log/app -type f -mtime +7 -delete
```

Or truncate active logs safely:

```bash
truncate -s 0 /var/log/app/app.log
```

---

## Verify recovery

```bash
df -i
```

Example:

```text
Filesystem       Inodes   IUsed   IFree IUse% Mounted on
/dev/sda1       5242880 3120000 2122880  60% /
```

Writes now succeed.

---

## SRE rules

- Always check `df -i` when disk errors don’t match `df -h`
- Monitor inode usage, not just disk percentage
- Many small files are more dangerous than large files

---

## Failure pattern

```text
disk usage looks OK
writes fail
df -i shows 100%
logs explode
services crash
```

This is inode exhaustion, not disk saturation.

