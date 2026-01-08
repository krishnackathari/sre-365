## High Load with Low CPU Usage

### Symptom
- System feels slow
- Load average is high
- CPU usage is low
- No obvious alerts

### What is misleading
High load usually suggests CPU pressure, but CPU is mostly idle.

### Investigation
- `vmstat 1` shows low `r`, high `b`
- CPU idle time is high
- I/O wait (`wa`) is elevated
- Processes observed in D state - `ps -eo pid,state,comm | awk '$2=="D"'`

### Root cause
Processes are blocked waiting on I/O.
Blocked processes contribute to load average even when CPU is idle.

### Resolution
Investigate disk or network I/O bottlenecks instead of scaling CPU.

### Key takeaway
High load does not always mean CPU saturation.
Always compare load with run queue and CPU idle time.

