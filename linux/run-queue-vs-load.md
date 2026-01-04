### Important clarification about `r`

- `r` does NOT mean processes currently running on CPU
- `r` means **runnable processes**:
  - processes currently running, **plus**
  - processes ready to run but waiting in the run queue

Because of this, `r` can be **greater than the number of CPU cores**.
Only up to `CPU cores` processes can run at once; the rest wait to be scheduled.
