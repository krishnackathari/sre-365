# Day 001 – Systems Engineer Daily Notes

> Date: 2026-02-24  
> Focus: Linux processes, signals, and process lifecycle (SRE fundamentals)

---

## 🎯 One-line takeaway
I can observe and control the Linux process lifecycle using ps/top/kill, explain kernel vs user space, PID/PPID, process states (R/S/D/Z), signals (TERM vs KILL), exit codes, and zombie processes.

---

## 🧠 Core Concepts Learned (max 3–5 bullets)
- Kernel space vs user space and why SREs care about the boundary  
- PID and PPID for tracing process ownership and lifecycle  
- Process states (R, S, D, Z) and what they indicate during incidents  
- Signals: SIGTERM for graceful shutdown vs SIGKILL for force kill  
- Exit codes and how supervisors (systemd/Kubernetes) use them

---

## 🔍 Deep Understanding (Explain it like I’m teaching a junior engineer)
Linux runs applications in user space, but the kernel manages CPU, memory, disk, and networking.  
Every running program is a process with a PID, and each process has a parent (PPID), which helps trace who spawned what during debugging.  
Process states like S (sleeping) are normal, but D (uninterruptible IO wait) often points to storage or kernel-level issues.  
SREs prefer SIGTERM over SIGKILL because graceful shutdowns prevent data corruption and allow services to clean up properly.

---

## 🛠 Commands / Tools I Used
```bash
sleep 9999 &
ps -o pid,ppid,stat,command -p <PID>
top -pid <PID>
kill -TERM <PID>
kill -9 <PID>
false; echo $?
true; echo $?
