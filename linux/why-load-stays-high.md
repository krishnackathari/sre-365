## Process states and load average

Load average counts processes in specific states:

- **R (Running / Runnable)**  
  Actively running on CPU or ready to run

- **D (Uninterruptible sleep)**  
  Blocked waiting on I/O (disk, network, NFS)  
  These processes **cannot be killed** and **do count toward load**

- **S (Interruptible sleep)**  
  Waiting for events (does NOT count toward load)

- **Z (Zombie)**  
  Exited but not reaped (does NOT count toward load)

Key insight:
- Load average includes **R and D states**
- High load with low CPU is often caused by **D-state processes**
