## System slow with idle CPU and free disk

### Symptom
- System feels slow
- CPU mostly idle
- Disk not full
- No obvious alerts

### What is misleading
Low utilization suggests plenty of capacity.

### Investigation
- `vmstat` shows low CPU usage
- `iostat -x` shows high `await`
- `%util` may be low
- Some processes observed in D state

### Root cause
High I/O latency causes applications to block,
even when disk capacity and throughput are available.

### Key takeaway
Utilization measures capacity.
Latency determines responsiveness.


#iostat -x 1
Key disk signals:
- await: I/O latency (ms)
- aqu-sz: queued I/O (pressure)
- %util: disk saturation

Low %util does NOT mean healthy storage.
High latency alone can stall applications.

