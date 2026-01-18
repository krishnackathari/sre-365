# CKA Mock Exam – Rapid Memorization Cheat Sheet (Mock 3)

---

## Q1 – Fix Broken Liveness Probe (Troubleshooting)

### Context Question
Why does Kubernetes reject a pod when the probe type doesn’t match the probe fields?

### Goal
Fix an invalid liveness probe and ensure the pod is stable.

### Steps

```bash
ssh cluster1-controlplane
```

```bash
kubectl apply -f red-probe-cka12-trb.yaml
```

**Error insight**
- `httpGet` was used with `command`
- `httpGet` **requires port**, not command

### Fix
Edit probe type and timing:

```bash
vi red-probe-cka12-trb.yaml
```

Changes:
- `httpGet` → `exec`
- `initialDelaySeconds: 1` → `5`

```bash
kubectl delete pod red-probe-cka12-trb
kubectl apply -f red-probe-cka12-trb.yaml
kubectl get pod red-probe-cka12-trb -w
```

---

## Q2 – Find Bitnami NGINX Helm Repo URL

### Context Question
How do you distinguish official vendor Helm charts from community charts?

### Goal
Find and store Bitnami nginx Helm repo URL.

```bash
ssh cluster3-controlplane
```

```bash
helm search hub nginx --list-repo-url | grep bitnami
```

```bash
echo "https://charts.bitnami.com/bitnami" > /root/nginx-helm-url.txt
```

---

## Q3 – kubectl Intermittently Failing (Troubleshooting)

### Context Question
Why does kube-controller-manager instability break kubectl commands?

### Goal
Fix control plane instability.

```bash
ssh cluster4-controlplane
kubectl get pods -n kube-system
```

```bash
kubectl logs kube-controller-manager-cluster4-controlplane -n kube-system
```

**Root cause**
- kube-apiserver liveness probe using wrong port (6444)

```bash
vi /etc/kubernetes/manifests/kube-apiserver.yaml
```

Fix:
- Change liveness port → `6443`

```bash
kubectl get pods -n kube-system -w
```

---

## Q4 – NodePort Website Not Working

### Context Question
What are the most common reasons NodePort services fail?

### Goal
Restore access to static site.

```bash
ssh cluster1-controlplane
curl http://cluster1-controlplane:30002
```

```bash
kubectl describe svc nginx-dp-cka04-trb
kubectl get endpoints
```

**Fix**
- Ensure correct selector labels
- Ensure pod is running and exposed

---

## Q5 – Shared Volume Between Containers

### Context Question
Why is `emptyDir` ideal for sidecar log sharing?

### Goal
Create pod with shared volume.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: grape-pod-cka06-str
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - name: grape-vol-cka06-str
      mountPath: /var/log/nginx
  - name: busybox-sidecar
    image: busybox
    command: ["sh","-c","sleep 7200"]
    volumeMounts:
    - name: grape-vol-cka06-str
      mountPath: /usr/src
  volumes:
  - name: grape-vol-cka06-str
    emptyDir: {}
```

```bash
kubectl apply -f grape-pod.yaml
```

---

## Q6 – PVC Stuck in Pending

### Context Question
What must match exactly for PVC → PV binding?

### Goal
Bind PVC successfully.

```bash
ssh cluster1-controlplane
kubectl describe pvc orange-pvc-cka13-trb
```

**Fix**
- Match accessModes with PV
- Recreate PVC

---

## Q7 – Node CPU & Memory Capacity

### Context Question
What’s the difference between allocatable and capacity?

```bash
ssh cluster2-controlplane
kubectl describe node cluster2-node01
```

```bash
echo "CPU: $(kubectl describe node cluster2-node01 | grep -m1 'cpu')" > /root/cluster2-node01-cpu.txt
echo "Memory: $(kubectl describe node cluster2-node01 | grep -m1 'memory')" > /root/cluster2-node01-memory.txt
```

---

## Q8 – Pod Crashing While Writing Time Logs

### Context Question
Why do file permission issues commonly crash cron-style pods?

```bash
ssh cluster1-controlplane
kubectl logs check-time-cka03-trb
kubectl describe pod check-time-cka03-trb
```

**Fix**
- Correct file path / permissions
- Ensure writable volume

---

## Q9 – Restore etcd Backup

### Context Question
Why must etcd restore use a **new empty data directory**?

```bash
ssh cluster1-controlplane
```

```bash
ETCDCTL_API=3 etcdctl snapshot restore /opt/cluster1_backup_to_restore.db \
  --data-dir=/root/default.etcd
```

---

## Q10 – HPA with Stabilization Window

### Context Question
Why do stabilization windows prevent scaling thrash?

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: webapp-hpa
spec:
  minReplicas: 1
  maxReplicas: 3
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300
```

---

## Q11 – Memory Request > Limit

### Context Question
Why does Kubernetes reject pods when requests exceed limits?

```bash
ssh cluster3-controlplane
cd /root/app-wl03
```

```bash
vi app-wl03.yaml
```

Fix:
```yaml
requests:
  memory: 100Mi
limits:
  memory: 100Mi
```

```bash
kubectl create -f app-wl03.yaml
```

---

## Q12 – Service Has No Endpoints

### Context Question
Why do Services fail silently when selectors mismatch?

```bash
kubectl describe svc curlme-cka01-svcn
```

```bash
kubectl delete svc curlme-cka01-svcn
kubectl expose pod curlme-cka01-svcn --port=80
```

---

## Q13 – Sidecar for Log Shipping

### Context Question
Why is a sidecar preferred over log scraping from host?

```yaml
volumes:
- name: varlog
  emptyDir: {}
```

```bash
kubectl replace -f elastic-app-cka02-arch.yaml --force
```

---

## Q14 – Extend CoreDNS Domain

### Context Question
How does CoreDNS support multiple cluster domains?

```bash
kubectl edit cm coredns -n kube-system
```

```bash
kubectl rollout restart deploy coredns -n kube-system
```

---

## Q15 – Replace Ingress with Gateway API

### Context Question
Why is Gateway API preferred over Ingress?

```bash
kubectl apply -f nginx-gateway.yaml
kubectl apply -f external-route.yaml
kubectl delete ingress external-ingress -n external
```

```bash
curl localhost:30080
```

---

## Q16 – Install Flannel CNI

### Context Question
Why must Pod CIDR match CNI network?

```bash
wget https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
```

```bash
kubectl apply -f kube-flannel.yml
```

---

## Q17 – Install cri-dockerd & Enable IP Forwarding

### Context Question
Why is IP forwarding mandatory for Kubernetes networking?

```bash
dpkg -i cri-dockerd_0.3.16.3-0.ubuntu-jammy_amd64.deb
systemctl enable --now cri-docker.service
```

```bash
echo "net.ipv4.ip_forward=1" > /etc/sysctl.d/k8s.conf
sysctl -p
```

---

✅ **End of Set**

