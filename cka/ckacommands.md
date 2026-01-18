# CKA COMMAND-FIRST MINDMAP (ALL MOCKS 1–8)

PURPOSE:
• No explanations
• Only scenarios → exact commands
• Memorize typing, not concepts

RULE:
👉 If you recognize the scenario, type the block under it

═══════════════════════════════════════════

## 🧠 POD NOT RUNNING / PENDING

### Pod Pending
```bash
kubectl describe pod <pod>
kubectl get events --sort-by=.metadata.creationTimestamp
```

### PVC Pending
```bash
kubectl describe pvc <pvc>
kubectl describe pv <pv>
kubectl get pvc <pvc> -o yaml > pvc.yaml
kubectl delete pvc <pvc>
kubectl apply -f pvc.yaml
```

### Scheduler issue
```bash
kubectl get pods -n kube-system
kubectl logs kube-scheduler-<node> -n kube-system
```

═══════════════════════════════════════════

## 🔁 CRASHLOOP / RESTARTS

### CrashLoopBackOff
```bash
kubectl logs <pod>
kubectl describe pod <pod>
```

### InitContainer stuck
```bash
kubectl edit pod <pod>
kubectl replace -f /tmp/kubectl-edit-*.yaml --force
```

### OOMKilled
```bash
kubectl describe pod <pod>
kubectl edit deploy <deploy>
```

═══════════════════════════════════════════

## 📦 MULTI-CONTAINER / SIDECAR

### Shared volume (ALWAYS emptyDir)
```yaml
volumes:
- name: shared
  emptyDir: {}
```

### Log reader
```bash
tail -f /path/to/log
```

═══════════════════════════════════════════

## 🧱 STORAGE (VERY HIGH WEIGHT)

### Create StorageClass (static)
```yaml
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
```

### Create StorageClass (dynamic local)
```yaml
provisioner: rancher.io/local-path
```

### PVC not binding (FIX PVC ONLY)
```bash
kubectl get pvc <pvc> -o yaml > pvc.yaml
kubectl delete pvc <pvc>
kubectl apply -f pvc.yaml
```

═══════════════════════════════════════════

## 🧩 SERVICE / NETWORKING

### Service not reachable
```bash
kubectl describe svc <svc>
kubectl get endpoints <svc>
```

### Expose Pod quickly
```bash
kubectl expose pod <pod> --port=80 --name <svc>
```

### Expose Deployment (NodePort)
```bash
kubectl expose deploy <deploy> \
--type=NodePort --port=8080 \
--dry-run=client -o yaml > svc.yaml
```

═══════════════════════════════════════════

## 🔐 RBAC (VERY COMMON)

### ServiceAccount
```bash
kubectl create sa <sa>
```

### ClusterRole (cluster-scoped)
```bash
kubectl create clusterrole <role> \
--resource=persistentvolumes --verb=get,list
```

### ClusterRoleBinding
```bash
kubectl create clusterrolebinding <bind> \
--clusterrole=<role> \
--serviceaccount=default:<sa>
```

### Verify permissions
```bash
kubectl auth can-i list pv \
--as=system:serviceaccount:default:<sa>
```

═══════════════════════════════════════════

## 🧲 TAINTS & TOLERATIONS

### Taint node
```bash
kubectl taint node node01 env=prod:NoSchedule
```

### Pod toleration
```yaml
tolerations:
- key: env
  operator: Equal
  value: prod
  effect: NoSchedule
```

═══════════════════════════════════════════

## 🚦 NETWORKPOLICY

### Policy exists = default deny
```bash
kubectl get netpol
```

### Minimal allow ingress
```yaml
policyTypes: [Ingress]
ingress:
- ports:
  - port: 80
```

═══════════════════════════════════════════

## 🌐 INGRESS

### Mandatory fields
```yaml
host:
path:
pathType:
service:
port:
```

### Test
```bash
curl http://<host>
```

═══════════════════════════════════════════

## 🌉 GATEWAY API

### Weighted traffic
```yaml
backendRefs:
- name: v1
  weight: 80
- name: v2
  weight: 20
```

### HTTPS
```yaml
protocol: HTTPS
port: 443
tls:
  certificateRefs:
```

═══════════════════════════════════════════

## 📈 AUTOSCALING

### HPA (FASTEST)
```bash
kubectl autoscale deploy <deploy> \
--cpu-percent=70 --min=2 --max=10
```

### HPA YAML apply
```bash
kubectl create -f hpa.yaml
```

### VPA modes
```yaml
updateMode: Auto
updateMode: Initial
updateMode: Recreate
```

═══════════════════════════════════════════

## ⚙️ CONTROL PLANE (EXAM KILLER)

### kubectl hangs
```bash
kubectl get pods -n kube-system
```

### Fix controller-manager typo
```bash
sed -i 's/kube-contro1ler-manager/kube-controller-manager/g' \
/etc/kubernetes/manifests/kube-controller-manager.yaml
```

### Static pod path
```bash
/etc/kubernetes/manifests/
```

═══════════════════════════════════════════

## 📦 HELM (ALWAYS SAME FLOW)

```bash
helm ls -A
helm lint ./chart
helm install new ./chart
helm uninstall old
```

═══════════════════════════════════════════

## 🔄 DEPLOYMENTS

### Rollback
```bash
kubectl rollout undo deploy <deploy>
```

### History
```bash
kubectl rollout history deploy <deploy>
```

### Resume paused deploy
```bash
kubectl rollout resume deploy <deploy>
```

═══════════════════════════════════════════

## 🧪 DNS

### Service DNS
```bash
nslookup <service>
```

### Pod DNS
```bash
nslookup <ip-with-dashes>.<ns>.pod
```

═══════════════════════════════════════════

## 🔚 FINAL MEMORY RULES

• PVC issues → accessModes FIRST  
• Scaling issues → controller-manager  
• NetworkPolicy → deny by default  
• Delete & recreate beats patch  
• Helm → never edit live objects  

✅ THIS IS YOUR **EXAM BRAIN MAP**

