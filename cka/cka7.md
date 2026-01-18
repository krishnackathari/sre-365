# CKA Mock Exam – Rapid Memorization Cheat Sheet (Mock 7)

Purpose:
• Lock in **exam-speed muscle memory**
• Recognize **standard patterns instantly**
• Avoid overthinking — fix, verify, move on

RULE FOR EXAM:
👉 Identify the Kubernetes object  
👉 Recall the exact pattern  
👉 Apply minimal commands  
👉 Verify once and move on  

---

## Q1 – StorageClass (Default, No Provisioner)

### Context Question
How do you create a **local/static storage class** and mark it as default?

### Key Pattern
• `kubernetes.io/no-provisioner`  
• `WaitForFirstConsumer`  
• Default SC via annotation  

### Fix
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: local-sc
  annotations:
    storageclass.kubernetes.io/is-default-class: "true"
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
```

Apply:
```bash
kubectl apply -f local-sc.yaml
kubectl get sc
```

---

## Q2 – Sidecar Logging Pattern (Deployment)

### Context Question
When one container **writes logs** and another **reads**, what must they share?

### Key Pattern
• `emptyDir`  
• Both containers mount same path  

⚠️ Exam Note: **Sidecar should be a normal container, NOT initContainer**

### Correct Pattern (Concept)
```yaml
volumes:
- name: log-volume
  emptyDir: {}
```

• Writer → app-container  
• Reader → log-agent (`tail -f`)  

---

## Q3 – Ingress (Host + Path Routing)

### Context Question
What are the 3 mandatory ingress fields in exams?

### Must-Have
• host  
• path + pathType  
• backend service + port  

### Fix
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: webapp-ingress
  namespace: ingress-ns
spec:
  ingressClassName: nginx
  rules:
  - host: kodekloud-ingress.app
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: webapp-svc
            port:
              number: 80
```

Test:
```bash
curl http://kodekloud-ingress.app/
```

---

## Q4 – Rolling Update Deployment

### Context Question
How do you **upgrade image version safely**?

### Exam Pattern
```bash
kubectl create deployment nginx-deploy \
  --image=nginx:1.16 \
  --dry-run=client -o yaml > deploy.yaml

kubectl apply -f deploy.yaml --record
kubectl set image deployment/nginx-deploy nginx=nginx:1.17 --record
```

Check history:
```bash
kubectl rollout history deployment nginx-deploy
```

---

## Q5 – User Access via CSR + RBAC

### Context Question
What 3 things are always required for user auth?

### Required
1️⃣ CSR with `signerName`  
2️⃣ Approve cert  
3️⃣ Role + RoleBinding  

### Fix Flow
```bash
kubectl apply -f john-csr.yaml
kubectl certificate approve john-developer
kubectl create role developer \
  --resource=pods \
  --verb=create,list,get,update,delete \
  -n development

kubectl create rolebinding developer-role-binding \
  --role=developer \
  --user=john \
  -n development
```

Verify:
```bash
kubectl auth can-i update pods --as=john -n development
```

---

## Q6 – DNS Resolution Inside Cluster

### Context Question
How does Kubernetes resolve **service vs pod DNS**?

### Patterns
• Service DNS → `svc-name`  
• Pod DNS → `IP-with-dashes.namespace.pod`

### Fix
```bash
kubectl run nginx-resolver --image=nginx
kubectl expose pod nginx-resolver \
  --name=nginx-resolver-service \
  --port=80 --type=ClusterIP
```

Service lookup:
```bash
kubectl run test --rm -it --image=busybox:1.28 --restart=Never \
  -- nslookup nginx-resolver-service > /root/CKA/nginx.svc
```

Pod lookup:
```bash
kubectl get pod nginx-resolver -o wide
kubectl run test --rm -it --image=busybox:1.28 --restart=Never \
  -- nslookup <POD-IP-with-dashes>.default.pod > /root/CKA/nginx.pod
```

---

## Q7 – Static Pod (Node-Level)

### Context Question
What restarts even if kube-apiserver is down?

### Answer
✅ Static Pods

### Fix Pattern
```bash
kubectl run nginx-critical --image=nginx \
  --dry-run=client -o yaml > static.yaml
```

On node:
```bash
mkdir -p /etc/kubernetes/manifests
cp static.yaml /etc/kubernetes/manifests/
```

Verify from controlplane:
```bash
kubectl get pods
```

---

## Q8 – HPA on Memory

### Context Question
How is memory-based scaling configured?

### Fix
```yaml
metrics:
- type: Resource
  resource:
    name: memory
    target:
      type: Utilization
      averageUtilization: 65
```

Apply:
```bash
kubectl create -f webapp-hpa.yaml
```

---

## Q9 – Gateway HTTPS + TLS

### Context Question
What changes when switching HTTP → HTTPS?

### Must Change
• protocol  
• port  
• tls.certificateRefs  

### Fix
```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: web-gateway
  namespace: cka5673
spec:
  gatewayClassName: kodekloud
  listeners:
  - name: https
    protocol: HTTPS
    port: 443
    hostname: kodekloud.com
    tls:
      certificateRefs:
      - name: kodekloud-tls
```

---

## Q10 – Helm Uninstall Vulnerable Image

### Context Question
How do you find a bad image deployed via Helm?

### Pattern
1️⃣ `helm ls -A`  
2️⃣ Inspect deployments  
3️⃣ `helm uninstall`

```bash
helm ls -A
kubectl get deploy -n <ns> <deploy> -o json | jq -r '.spec.template.spec.containers[].image'
helm uninstall <release> -n <namespace>
```

---

## Q11 – NetworkPolicy Selection

### Context Question
What is the **most restrictive** policy?

### Exam Logic
• Allow frontend  
• Deny databases  
• No wildcards  

### Fix
```bash
kubectl apply -f /root/net-pol-3.yaml
kubectl get netpol -n backend
```

---

## FINAL MOCK 7 EXAM TAKEAWAYS

• Default StorageClass = annotation  
• emptyDir = sidecar sharing  
• Static Pods live on nodes  
• CSR **must** include signerName  
• Pod DNS ≠ Service DNS  
• Gateway HTTPS = TLS config  
• Helm cleanup = uninstall release  
• Pick **most restrictive** NetworkPolicy  

✅ **Mock 7 recorded**

