# CKA Mock Exam – Rapid Memorization Cheat Sheet (Mock 8)

Focus: **Cluster config, RBAC, storage, networking, autoscaling, Helm, Gateway API**

RULE FOR EXAM:
• Identify the object being tested  
• Recall the exact YAML / command pattern  
• Apply minimal fix  
• Verify once → move on  

---

## Q1 – kubeadm Network Prerequisites (sysctl)

### Context Question  
What kernel parameters **must** be enabled before installing a CNI?

### Key Pattern  
• ip_forward  
• bridge iptables  

### Fix
```bash
echo 'net.ipv4.ip_forward = 1' >> /etc/sysctl.conf
echo 'net.bridge.bridge-nf-call-iptables = 1' >> /etc/sysctl.conf
sysctl -p
```

Verify:
```bash
sysctl net.ipv4.ip_forward
sysctl net.bridge.bridge-nf-call-iptables
```

---

## Q2 – ServiceAccount + ClusterRole (PV Viewer)

### Context Question  
When does a pod need a **ClusterRole** instead of a Role?

### Key Pattern  
• PersistentVolumes are **cluster-scoped**  
• Use ClusterRole + ClusterRoleBinding  

### Fix
```bash
kubectl create serviceaccount pvviewer
kubectl create clusterrole pvviewer-role \
  --resource=persistentvolumes --verb=list
kubectl create clusterrolebinding pvviewer-role-binding \
  --clusterrole=pvviewer-role \
  --serviceaccount=default:pvviewer
```

Pod:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pvviewer
spec:
  serviceAccountName: pvviewer
  containers:
  - name: pvviewer
    image: redis
```

---

## Q3 – StorageClass (Rancher Local Path)

### Context Question  
How do dynamic local volumes wait for node scheduling?

### Key Pattern  
• WaitForFirstConsumer  

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: rancher-sc
provisioner: rancher.io/local-path
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
```

---

## Q4 – ConfigMap → Deployment Env Vars

### Context Question  
What is the fastest way to inject multiple env vars?

### Key Pattern  
• `kubectl set env --from=configmap`

```bash
kubectl create configmap app-config -n cm-namespace \
  --from-literal=ENV=production \
  --from-literal=LOG_LEVEL=info

kubectl set env deployment/cm-webapp -n cm-namespace \
  --from=configmap/app-config
```

---

## Q5 – PriorityClass + Pod Recreation

### Context Question  
Can PriorityClass be added to a running pod?

❌ No → Pod must be recreated

```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: low-priority
value: 50000
globalDefault: false
```

Pod:
```yaml
spec:
  priorityClassName: low-priority
```

---

## Q6 – NetworkPolicy (Ingress Fix)

### Context Question  
Why does a service fail when NetworkPolicies exist?

### Key Pattern  
• Ingress policy defaults to **deny all**

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: ingress-to-nptest
spec:
  podSelector:
    matchLabels:
      run: np-test-1
  policyTypes:
  - Ingress
  ingress:
  - ports:
    - protocol: TCP
      port: 80
```

---

## Q7 – Taints & Tolerations

### Context Question  
What blocks scheduling at node level?

### Key Pattern  
• Taint blocks  
• Toleration allows  

```bash
kubectl taint node node01 env_type=production:NoSchedule
```

Tolerated pod:
```yaml
tolerations:
- key: env_type
  operator: Equal
  value: production
  effect: NoSchedule
```

---

## Q8 – PVC Not Binding (AccessMode Mismatch)

### Context Question  
What are the **3 most common PVC binding failures**?

• AccessMode mismatch  
• StorageClass mismatch  
• Capacity mismatch  

Fix PVC (not PV):
```yaml
accessModes:
- ReadWriteOnce
```

Recreate:
```bash
kubectl delete pvc app-pvc -n storage-ns
kubectl apply -f pvc.yaml
```

---

## Q9 – Broken kubeconfig

### Context Question  
What port does kube-apiserver listen on?

### Answer  
✅ 6443

Fix:
```bash
vi /root/CKA/super.kubeconfig
```

Verify:
```bash
kubectl cluster-info --kubeconfig=/root/CKA/super.kubeconfig
```

---

## Q10 – Scaling Fails (Controller Manager Down)

### Context Question  
Which component actually scales replicas?

### Answer  
✅ kube-controller-manager  

Fix typo:
```bash
sed -i 's/kube-contro1ler-manager/kube-controller-manager/g' \
/etc/kubernetes/manifests/kube-controller-manager.yaml
```

Verify:
```bash
kubectl get deploy nginx-deploy
```

---

## Q11 – HPA with Custom Metrics

### Context Question  
Will the exam fail if metrics don’t exist?

❌ No — **ignore runtime metric errors**

```yaml
metrics:
- type: Pods
  pods:
    metric:
      name: requests_per_second
    target:
      type: AverageValue
      averageValue: "1000"
```

---

## Q12 – Gateway API Traffic Split

### Context Question  
How do you do canary / weighted routing?

### Key Pattern  
• `weight:` under backendRefs  

```yaml
backendRefs:
- name: web-service
  port: 80
  weight: 80
- name: web-service-v2
  port: 80
  weight: 20
```

---

## Q13 – Helm Upgrade via New Release

### Context Question  
How do you safely replace a Helm release?

### Pattern  
• lint → install new → uninstall old  

```bash
helm lint ./new-version
helm install webpage-server-02 ./new-version
helm uninstall webpage-server-01 -n default
```

---

## Q14 – Cluster Pod CIDR (kubeadm)

### Context Question  
What is the difference between **cluster PodCIDR** and **node PodCIDR**?

• Cluster → big range  
• Node → slice  

```bash
kubectl -n kube-system get cm kubeadm-config -o yaml \
| awk '/podSubnet:/{print $2}' > /root/pod-cidr.txt
```

---

## MOCK 8 – FINAL TAKEAWAYS

• sysctl config = kubeadm prerequisite  
• PV access requires ClusterRole  
• PriorityClass requires pod recreation  
• NetworkPolicy = default deny  
• Controller-manager controls scaling  
• Gateway API = weights, not annotations  
• Cluster PodCIDR ≠ Node PodCIDR  

✅ **Mock 8 recorded and locked**

