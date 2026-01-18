CKA Mock Exam – Rapid Memorization Cheat Sheet (Mock 6)

Purpose:
• Lock in **exam-speed muscle memory**
• Recognize **standard patterns instantly**
• Avoid overthinking — fix, verify, move on

RULE:
👉 Read **Context Question**
👉 Apply **Fix Pattern**
👉 Run **minimum commands**

---

## Q1 – Multi-Container Pod + Shared Volume

### Context Question
When two containers need to **share files**, which volume type is always used?

### Key Pattern
• `emptyDir` → non-persistent, pod-scoped  
• One container **writes**, another **reads**

### Fix (YAML)
```yaml
volumes:
- name: shared-volume
  emptyDir: {}
```

Key tricks:
- `NODE_NAME` via `fieldRef`
- Writer → `date >> file`
- Reader → `tail -f file`

---

## Q2 – Install cri-docker Runtime

### Context Question
What service must be **enabled + running** for kubeadm with Docker?

### Fix
```bash
ssh bob@node01
sudo dpkg -i /root/cri-docker_0.3.16.3-0.debian.deb
sudo systemctl start cri-docker
sudo systemctl enable cri-docker
```

Verify:
```bash
systemctl is-active cri-docker
systemctl is-enabled cri-docker
```

---

## Q3 – List VPA CRDs

### Context Question
Which CRDs always exist for VPA?

### Fix
```bash
kubectl get crd -o custom-columns=NAME:.metadata.name | \
grep verticalpodautoscaler > /root/vpa-crds.txt
```

---

## Q4 – Expose Pod Imperatively

### Context Question
What is the **fastest** way to expose a pod internally?

### Fix
```bash
kubectl expose pod messaging --port=6379 --name messaging-service
```

---

## Q5 – Create Deployment (Imperative)

### Context Question
Which command creates deployment **fastest in exams**?

### Fix
```bash
kubectl create deployment hr-web-app \
  --image=kodekloud/webapp-color \
  --replicas=2
```

---

## Q6 – Init Container Command Typo

### Context Question
Why do initContainers block app startup?

### Root Cause
```text
sleeeep → sleep
```

### Fix Pattern
```bash
kubectl edit pod orange
kubectl replace -f /tmp/kubectl-edit-xxxx.yaml --force
```

---

## Q7 – Expose Deployment via NodePort

### Context Question
Why should `--dry-run=client -o yaml` be used?

### Fix
```bash
kubectl expose deployment hr-web-app \
  --type=NodePort \
  --port=8080 \
  --name=hr-web-app-service \
  --dry-run=client -o yaml > hr-web-app-service.yaml
```

Add:
```yaml
nodePort: 30082
```

Apply:
```bash
kubectl apply -f hr-web-app-service.yaml
```

---

## Q8 – Static Persistent Volume

### Context Question
When is `hostPath` acceptable in exams?

### Fix
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-analytics
spec:
  capacity:
    storage: 100Mi
  accessModes:
  - ReadWriteMany
  hostPath:
    path: /pv/data-analytics
```

---

## Q9 – HPA with Stabilization Window

### Context Question
What prevents **scale-down thrashing**?

### Fix
```yaml
behavior:
  scaleDown:
    stabilizationWindowSeconds: 300
```

Apply:
```bash
kubectl create -f /root/webapp-hpa.yaml
```

---

## Q10 – VPA in Recreate Mode

### Context Question
Which VPA mode **kills and recreates pods**?

### Fix
```yaml
updatePolicy:
  updateMode: "Recreate"
```

---

## Q11 – Create Gateway Resource

### Context Question
Gateway API replacement for Ingress?

### Fix
```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: web-gateway
  namespace: nginx-gateway
spec:
  gatewayClassName: nginx
  listeners:
  - name: http
    protocol: HTTP
    port: 80
```

---

## Q12 – Helm Repo Update + Upgrade

### Context Question
What **must** be done before upgrading a Helm chart?

### Fix Order
1️⃣ List releases  
2️⃣ Update repo  
3️⃣ Search versions  
4️⃣ Upgrade  

```bash
helm ls -A
helm repo update
helm upgrade kk-mock1 kk-mock1/nginx \
  -n kk-ns --version 18.1.15
```

Verify:
```bash
helm ls -n kk-ns
```

---

## FINAL EXAM PATTERNS (Mock 6)

• emptyDir = sidecar sharing  
• initContainers fail → command typos  
• VPA modes: Auto / Initial / Recreate  

