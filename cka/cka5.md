# CKA Mock Exam – Rapid Memorization Cheat Sheet (Mock 5)

Purpose:
• Ultra-fast recall
• Exam-pattern recognition
• “Fix-first, think-later” troubleshooting mindset

RULE OF THIS SHEET:
👉 Read the **Context Question** first.  
👉 Then skim **Fix Logic**.  
👉 Commands are muscle memory.

---

## Q1 – Multi-Container Pod Crash + Service Not Working

### Context Question
When a Pod has multiple containers, **which container restarts bring the whole Pod down?**

### Root Causes
1. Typo in image tag (`nginx:latst`)
2. Sidecar reading wrong log path
3. Service selector mismatch

### Fix Flow
```bash
ssh cluster1-controlplane
kubectl logs nginx-cka01-trb -c nginx-container
kubectl edit pod nginx-cka01-trb
```

Fix image:
```text
nginx:latst → nginx:latest
```

Fix sidecar log path:
```bash
kubectl get pod nginx-cka01-trb -o yaml > /tmp/pod.yaml
vi /tmp/pod.yaml
```

```text
/var/log/httpd → /var/log/nginx
```

Recreate Pod:
```bash
kubectl delete pod nginx-cka01-trb
kubectl apply -f /tmp/pod.yaml
```

Fix Service selector:
```bash
kubectl edit svc nginx-service-cka01-trb
```

```text
httpd-app-cka01-trb → nginx-app-cka01-trb
```

Verify:
```bash
curl http://cluster1-controlplane:30001
```

---

## Q2 – Highest User-Defined PriorityClass

### Context Question
Why must **system-* PriorityClasses be ignored** in exam questions?

### Fix
```bash
ssh cluster2-controlplane
kubectl get priorityclasses
```

Highest user-defined:
```text
silver-tier → 999
```

Store:
```bash
echo "999" > /root/highest-user-prio.txt
```

---

## Q3 – Kernel Networking Prep for kubeadm

### Context Question
Why does Kubernetes **silently break** if bridge forwarding is disabled?

### Fix
```bash
ssh cluster5-controlplane
vi /etc/sysctl.d/k8s.conf
```

```text
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
net.ipv4.conf.all.forwarding        = 1
```

Apply:
```bash
sysctl --system
```

---

## Q4 – Sidecar Pattern (Log Writer + Log Server)

### Context Question
Why is `emptyDir` the **default sidecar volume choice**?

### Pattern
• Main container → writes logs  
• Sidecar → serves logs  

```yaml
volumes:
- name: shared-logs
  emptyDir: {}
```

Verify:
```bash
kubectl exec -n cka-multi-containers cka-sidecar-pod -c sidecar-container -- curl http://localhost/app.log
```

---

## Q5 – VPA Auto Mode with Limits

### Context Question
What happens if VPA **exceeds container limits**?

### Key Rule
👉 `containerName` MUST match deployment container name

```yaml
updateMode: Auto
minAllowed:
  cpu: 600m
  memory: 600Mi
maxAllowed:
  cpu: "1"
  memory: 1Gi
```

---

## Q6 – Move Env Vars to ConfigMap

### Context Question
Why are env vars moved to ConfigMaps in real systems?

### Fix
```bash
kubectl create cm webapp-wl10-config-map --from-literal=APP_COLOR=red
```

Attach:
```yaml
envFrom:
- configMapRef:
    name: webapp-wl10-config-map
```

---

## Q7 – Helm Repo Update + Upgrade

### Context Question
Why must `helm repo update` be run before upgrades?

### Fix
```bash
helm repo update kubesphere
helm upgrade lvm-crystal-apd kubesphere/nginx \
  -n crystal-apd-ns \
  --version 1.3.4 \
  --set replicaCount=3
```

---

## Q8 – DaemonSet Not Running on Control Plane

### Context Question
Why do DaemonSets **skip controlplanes by default**?

### Fix
```bash
kubectl edit ds logs-cka26-trb -n kube-system
```

Add toleration:
```yaml
- key: node-role.kubernetes.io/control-plane
  operator: Exists
  effect: NoSchedule
```

---

## Q9 – Ingress Creation

### Context Question
Why does `ssl-redirect=false` appear often in exams?

### Fix
```yaml
annotations:
  nginx.ingress.kubernetes.io/ssl-redirect: "false"
```

Verify:
```bash
curl -I <ingress-ip>
```

---

## Q10 – Static PV + Selector-Based PVC

### Context Question
Why does `matchLabels` beat dynamic provisioning here?

### Key Points
• hostPath PV  
• nodeAffinity  
• label-based binding  

```yaml
selector:
  matchLabels:
    storage-tier: gold
```

---

## Q11 – Kustomize RBAC Fix

### Context Question
What RBAC verb is **always forgotten** in dashboards?

### Fix
```yaml
verbs: ["get", "list", "watch"]
```

Apply:
```bash
kubectl kustomize overlays/dev | kubectl apply -f -
kubectl rollout restart deploy web-dashboard
```

---

## Q12 – Multi-Layer Failure (PVC + Init + Probe)

### Context Question
Why do exam questions often hide **multiple failures**?

### Fix Order
1️⃣ PVC capacity mismatch  
2️⃣ Init container typo  
3️⃣ Liveness probe port  

```text
/bin/bsh → /bin/bash
81 → 80
```

---

## Q13 – Header-Based Routing (Gateway API)

### Context Question
Why is Gateway API replacing Ingress?

### Fix
```yaml
matches:
- headers:
  - name: X-Environment
    value: canary
```

Test:
```bash
curl -H 'X-Environment: canary' http://localhost:30080
```

---

## Q14 – HPA Scale-Down Control

### Context Question
Why is `selectPolicy: Min` critical?

### Rule
👉 Remove **fewer pods**, not more.

```yaml
selectPolicy: Min
```

---

## Q15 – Static StorageClass

### Context Question
When should `no-provisioner` be used?

### Answer
👉 Bare-metal / local disks

```yaml
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
```

---

## Q16 – Calico CNI with VXLAN

### Context Question
Why VXLAN instead of IPIP?

### Fix
```yaml
cidr: 172.17.0.0/16
encapsulation: VXLAN
```

Verify:
```bash
kubectl run test --rm -it --image=jrecord/nettools -- curl <pod-ip>
```

---

## FINAL EXAM MANTRA

• Read events first  
• Fix infra before app  
• Delete + recreate beats patching  
• Control plane issues = RBAC / probes / CNI  

✅ **Mock 5 locked**

