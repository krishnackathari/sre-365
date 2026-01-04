## Run queue vs CPU cores vs load average

- CPU cores define how many processes can run in parallel
- `r` shows how many processes want CPU
- `b` shows how many processes are blocked
- Load average reflects both over time

Load alone is meaningless.
It must be interpreted relative to CPU cores and run queue.

High load with low `r` indicates blocked work,
not CPU saturation.

### Important clarification about `r`

- `r` does NOT mean processes currently running on CPU
- `r` means **runnable processes**:
  - processes currently running, **plus**
  - processes ready to run but waiting in the run queue

Because of this, `r` can be **greater than the number of CPU cores**.
Only up to `CPU cores` processes can run at once; the rest wait to be scheduled.
