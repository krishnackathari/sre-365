## CPU signals: us, sy, wa

- us: application CPU time
- sy: kernel CPU time
- wa: CPU idle, waiting on disk I/O

High wa with low us/sy indicates an I/O-bound system,
not CPU saturation.

## Understanding vmstat CPU fields

In `vmstat`, CPU fields are **percentages of CPU time**, not counts
of processes.

- us: percentage of CPU time spent running user-space code
- sy: percentage of CPU time spent running kernel-space code
- wa: percentage of CPU time the CPU is idle while waiting on I/O

Important distinction:
- `r` and `b` are **counts of processes**
- `us`, `sy`, and `wa` are **percentages of CPU time**

Example:
High `b` with high `wa` indicates many processes are blocked
and the CPU is waiting on I/O, not executing work.
