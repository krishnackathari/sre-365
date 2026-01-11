## OOM kills in Kubernetes

### Two types of OOM

1) Container OOM  
Occurs when a pod exceeds its memory limit.  
The node may still have free memory.

2) Node OOM  
Occurs when the entire node runs out of memory.  
The kernel kills processes across pods.

### How to detect

In Kubernetes:
kubectl describe pod <pod>
Reason: OOMKilled

On the node:
dmesg | grep -i oom

### Why apps die
The kernel kills the largest memory users first
(Java, Python, databases).

### Key takeaway
Pods can be OOMKilled even when the node is healthy.
Always size memory limits larger than the application’s real footprint.

