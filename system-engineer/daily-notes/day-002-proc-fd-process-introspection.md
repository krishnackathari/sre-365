# Day 002 – Systems Engineer Daily Notes

> Date: 2026-03-26  
> Focus: /proc, file descriptors (FD), and process introspection (Cloudflare-style debugging)

---

## 🎯 One-line takeaway
I can inspect any running process using /proc to understand its state, memory usage, open file descriptors, and active resources, and use this to debug real production issues.

---

## 🧠 Core Concepts Learned (max 3–5 bullets)
- /proc is a kernel-exposed virtual filesystem showing real-time process data  
- Each process has /proc/<pid>/ with status, fd, cmdline, and network info  
- File Descriptors (FDs) represent all resources: files, sockets, pipes, terminals  
- /proc/<pid>/fd shows actual open FDs, while lsof shows additional metadata (mem, txt, cwd)  
- FD leaks lead to “too many open files” and service failures  

---

## 🔍 Deep Understanding (Explain it like I’m teaching a junior engineer)
Linux exposes process information through /proc, which is a virtual filesystem backed by the kernel, not actual disk files.  
Each process has a directory /proc/<pid>/ that contains everything the OS knows about it—its state, memory usage, command, and resources.  
File descriptors are integer handles (0,1,2,...) that represent open resources. By default, every process has stdin (0), stdout (1), and stderr (2), usually pointing to a terminal like /dev/pts/X.  
Additional FDs (3, 4, …) represent sockets, files, or pipes.  
The /proc/<pid>/fd directory shows only real open FDs, while lsof includes memory-mapped libraries and executables, which is why it looks larger.  
SREs use this to debug issues like FD leaks, stuck processes, and resource exhaustion in production systems.

---

## 🛠 Commands / Tools I Used
```bash
echo $$                                  # get current shell PID
cat /proc/<pid>/status                   # process state, memory, threads
ls -l /proc/<pid>/fd | head              # list open file descriptors
cat /proc/<pid>/cmdline | tr '\0' ' '    # full command of process
lsof -p <pid> | head                     # list open files/sockets
ls /proc/<pid>/fd | wc -l                # count open FDs
watch -n 1 "ls /proc/<pid>/fd | wc -l"   # monitor FD count over time
readlink /proc/<pid>/fd/<fd>             # resolve FD target
ss -p                                   # inspect sockets
```

---

## 🧪 Observations from Lab
- A simple process usually has 3–4 FDs: stdin, stdout, stderr, and sometimes a socket  
- /proc/<pid>/fd entries are symlinks pointing to actual resources  
- lsof shows many “mem” entries (shared libraries), which are NOT actual FDs  
- Terminal processes map 0,1,2 → /dev/pts/X  
- Sockets appear as socket:[inode] and represent active connections  

---

## 🧠 Interview Q&A (must remember)

**Q: What is an FD leak and its symptoms?**  
A:  
An FD leak happens when a process opens files or sockets but doesn’t close them.  
Symptoms include increasing FD count, “too many open files” errors, and the application failing to accept new connections.

**Q: How do you check what sockets a process owns?**  
```bash
lsof -p <pid>
ss -p | grep <pid>
ls -l /proc/<pid>/fd
```

**Q: Why does lsof show more entries than /proc/<pid>/fd?**  
A:  
Because lsof includes memory-mapped files, executables, and libraries (mem, txt, cwd), not just actual file descriptors.

---

## 🚀 Real Debug Flow (SRE mindset)
```bash
# 1. Identify PID
ps aux | grep <process>

# 2. Check state and memory
cat /proc/<pid>/status

# 3. Inspect open file descriptors
ls /proc/<pid>/fd

# 4. Check detailed resources
lsof -p <pid>

# 5. Monitor FD growth
watch "ls /proc/<pid>/fd | wc -l"
```

---

## 🎯 Key Takeaways
- /proc is the most direct way to see what the kernel knows about a process  
- File descriptors represent all active resources used by a process  
- Only /proc/<pid>/fd shows real open FDs; lsof shows a broader view  
- Monitoring FD count is critical for debugging production issues  
