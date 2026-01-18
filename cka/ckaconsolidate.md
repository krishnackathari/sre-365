# CKA MASTER ONE-PAGE CHEAT SHEET (FINAL – Mocks 1–8)

PURPOSE:
• Pattern recognition
• Minimal fix
• Verify once
• Move on

EXAM RULE:
👉 Read events first
👉 Fix infra before app
👉 Delete & recreate > patch

────────────────────────────────

## 1️⃣ CLUSTER BOOTSTRAP & NODE PREP (kubeadm, sysctl)

MANDATORY before CNI:
```bash
net.ipv4.ip_forward=1
net.bridge.bridge-nf-call-iptables=1
```

```bash
echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf
echo "net.bridge.bridge-nf-call-iptables=1" >> /etc/sysctl.conf
sysctl -p
```

Cluster Pod CIDR:
```bash
kubectl -n kube-system get cm kubeadm-config -o yaml | grep podSubnet
```

Cluster CIDR ≠ Node CIDR

────────────────────────────────

## 2️⃣ CONTROL PLANE FAILURES (MOST IMPORTANT)

| Symptom | Component |
|------|------|
kubectl hangs | kube-apiserver
Pods Pending | kube-scheduler
Scaling fails | kube-controller-manager

FAST FIX:
```bash
sed -i 's/kube-contro1ler-manager/kube-controller-manager/g' \
/etc/kubernetes/manifests/kube-controller-manager.yaml
```

Static pods auto-restart.

────────────────────────────────

## 3️⃣ RBAC (ServiceAccounts, Roles)

Golden rules:
• Namespace scoped → Role  
• Cluster scoped (PV, nodes, ns) → ClusterRole  
• Pods authenticate via **ServiceAccount**

Pattern:
```bash
kubectl create sa app-sa
kubectl create clusterrole app-role \
--resource=persistentvolumes --verb=get,list
kubectl create clusterrolebinding app-bind \
--clusterrole=app-role \
--serviceaccount=default:app-sa
```

Verify:
```bash
kubectl auth can-i list pv \
--as=system:serviceaccount:default:app-sa
```

────────────────────────────────

## 4️⃣ STORAGE (SC, PV, PVC)  ⭐ HIGH WEIGHT

90% PVC failures:
• accessModes mismatch
• storageClass mismatch
• capacity mismatch

RULE:
👉 Fix PVC, NOT PV  
👉 Delete & recreate PVC

```bash
kubectl get pvc -o yaml > pvc.yaml
kubectl delete pvc <name>
kubectl apply -f pvc.yaml
```

Static SC:
```yaml
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
```

Dynamic local:
```yaml
provisioner: rancher.io/local-path
```

────────────────────────────────

## 5️⃣ POD DESIGN (Sidecar, Init, Priority)

Sidecar sharing:
```yaml
volumes:
- name: shared
  emptyDir: {}
```

InitContainer stuck?
👉 99% = command typo  
👉 Delete & recreate pod

PriorityClass:
❌ Cannot add to running pod  
✅ Recreate pod

```yaml
priorityClassName: high-priority
```

────────────────────────────────

## 6️⃣ SCHEDULING (Taints, Tolerations, Node)

Taint:
```bash
kubectl taint node node01 env=prod:NoSchedule
```

Toleration:
```yaml
tolerations:
- key: env
  operator: Equal
  value: prod
  effect: NoSchedule
```

Manual scheduling:
```yaml
nodeName: node01
```

────────────────────────────────

## 7️⃣ NETWORKING – SERVICES

Broken Service?
| Issue | Cause |
|---|---|
No endpoints | selector mismatch
Conn refused | targetPort wrong

Expose fast:
```bash
kubectl expose pod app --port=80 --name app-svc
```

────────────────────────────────

## 8️⃣ NETWORKPOLICY

RULE:
👉 Policy exists = **default deny**

Minimal ingress allow:
```yaml
policyTypes: [Ingress]
ingress:
- ports:
  - port: 80
```

Selectors matter more than ports.

────────────────────────────────

## 9️⃣ INGRESS & GATEWAY API

Ingress MUST have:
• host
• path + pathType
• service + port

Gateway API:
• Canary = `weight`
• HTTPS = `tls.certificateRefs`

```yaml
backendRefs:
- name: v1
  weight: 80
- name: v2
  weight: 20
```

────────────────────────────────

## 🔟 AUTOSCALING (HPA / VPA)

HPA CPU:
```yaml
averageUtilization: 65
```

Custom metrics?
👉 Ignore runtime errors

VPA modes:
| Mode | Effect |
|----|----|
Auto | Evicts
Initial | New pods only
Recreate | Kill & recreate

────────────────────────────────

## 1️⃣1️⃣ TROUBLESHOOTING MAP (EXAM GOLD)

| Symptom | Think |
|------|------|
Pending | PVC / Quota / Scheduler
CrashLoop | Env / Secret / ConfigMap
OOMKilled | limits too low
503 / 404 | Service / Ingress
Scale stuck | controller-manager

FIRST COMMAND:
```bash
kubectl get events --sort-by=.metadata.creationTimestamp
```

────────────────────────────────

## 1️⃣2️⃣ HELM (ALWAYS SAME FLOW)

```bash
helm ls -A
helm lint ./chart
helm install new ./chart
helm uninstall old
```

Never edit live objects.

────────────────────────────────

## FINAL EXAM MANTRA (MEMORIZE)

• Fix infra before app  
• Delete & recreate beats patch  
• Cluster-scope → ClusterRole  
• NetworkPolicy = deny by default  
• Controller-manager controls replicas  
• PVC binding → accessModes first  

✅ THIS SHEET + YOUR 8 MOCKS = PASS

