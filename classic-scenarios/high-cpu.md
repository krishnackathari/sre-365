## High CPU – First Response

Symptoms:
- System feels slow or unresponsive
- High load average alerts
- Requests timing out or latency increasing

First commands (in order) and what I look for:

1. top
   - Identify which process is consuming CPU
   - Check if CPU usage is user (`us`) or system (`sy`)
   - Look for single process vs many processes

2. uptime
   - Check load average (1, 5, 15 min)
   - Compare load to number of CPU cores
   - High load does NOT always mean high CPU usage

3. ps aux --sort=-%cpu | head
   - Confirm top CPU-consuming processes
   - Determine if one process or multiple processes are responsible

4. vmstat 1
   - `r`: run queue (CPU contention)
   - `us` / `sy`: user vs kernel CPU
   - `wa`: I/O wait (key indicator)
   - High `wa` suggests I/O bottleneck, not CPU

5. iostat -x 1
   - Identify disk causing I/O wait
   - `%util` near 100% indicates disk saturation
   - High `await` indicates slow disk I/O
