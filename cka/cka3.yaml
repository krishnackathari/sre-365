# CKA Mock Exam – Rapid Memorization Cheat Sheet (Mock 3)

---

## Q1 – Pod Pending due to wrong memory units
**Context:** Pod requests memory in Gi instead of Mi → Pending.

```bash
kubectl describe pod nginx-wl06
kubectl get pod nginx-wl06 -o yaml > nginx-fixed.yaml
```

```yaml
resources:
  requests:
    memory: 100Mi
```

```bash
kubectl delete pod nginx-wl06
kubectl apply -f nginx-fixed.yaml
```

---

## Q2 – Uninstall Helm release using vulnerable image
**Context:** Find Helm release using kodekloud/click-counter:latest.

```bash
helm ls -A
```

```bash
kubectl get deploy -n security-alpha-01 security-alpha-apd -o json \
| jq -r '.spec.template.spec.containers[].image'
```

```bash
helm uninstall security-alpha-apd -n security-alpha-01
```

---

## Q3 – Service with specific pods + save pod IPs
**Context:** Expose only pod-21 & pod-23 and record pod IPs.

```bash
kubectl get pods --show-labels -n spectra-1267
kubectl get pod -l mode=exam,type=external -n spectra-1267
```

```bash
kubectl create svc clusterip service-3421-svcn -n spectra-1267 \
--tcp=8080:80 --dry-run=client -o yaml > svc.yaml
```

```yaml
selector:
  mode: exam
  type: external
```

```bash
kubectl apply -f svc.yaml
kubectl get pods -n spectra-1267 \
-o=custom-columns='POD_NAME:metadata.name,IP_ADDR:status.podIP' \
--sort-by=.status.podIP > /root/pod_ips_cka05_svcn
```

---

## Q4 – List VPA CRDs
**Context:** Extract only VerticalPodAutoscaler CRDs.

```bash
kubectl get crd | grep verticalpodautoscaler
```

```bash
echo verticalpodautoscalercheckpoints.autoscaling.k8s.io > /root/vpa-crds.txt
echo verticalpodautoscalers.autoscaling.k8s.io >> /root/vpa-crds.txt
```

---

## Q5 – HPA with custom scale behavior
**Context:** CPU-based HPA with scale up/down rules.

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: web-ui-hpa
  namespace: ck1967
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: web-ui-deployment
  minReplicas: 2
  maxReplicas: 12
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 65
  behavior:
    scaleUp:
      policies:
      - type: Percent
        value: 20
        periodSeconds: 45
    scaleDown:
      policies:
      - type: Percent
        value: 10
        periodSeconds: 60
```

---

## Q6 – Pod crashing due to OOMKilled
**Context:** Pod restarts because memory limit too low.

```bash
kubectl logs green-deployment-cka15-trb-<pod>
kubectl describe pod green-deployment-cka15-trb-<pod>
```

```bash
kubectl edit deploy green-deployment-cka15-trb
```

```text
limits.memory: 256Mi ❌
limits.memory: 512Mi ✅
```

---

## Q7 – VPA Initial mode (no eviction)
**Context:** Apply VPA only to new pods.

```yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: cache-vpa
  namespace: caching
spec:
  targetRef:
    apiVersion: apps/v1
    kind: StatefulSet
    name: cache-statefulset
  updatePolicy:
    updateMode: Initial
```

---

## Q8 – PVC + mount into Pod
**Context:** Bind PVC and mount at /var/www/html.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: peach-pvc-cka05-str
spec:
  volumeName: peach-pv-cka05-str
  accessModes: [ReadWriteOnce]
  resources:
    requests:
      storage: 100Mi
```

```yaml
volumeMounts:
- mountPath: /var/www/html
  name: nginx-volume
```

```bash
kubectl apply -f /root/peach-pod-cka05-str.yaml
```

---

## Q9 – HTTPRoute weighted traffic split
**Context:** 80% → v1, 20% → v2.

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: web-portal-httproute
  namespace: cka3658
spec:
  parentRefs:
  - name: nginx-gateway
    namespace: nginx-gateway
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /
    backendRefs:
    - name: web-portal-service-v1
      port: 80
      weight: 80
    - name: web-portal-service-v2
      port: 80
      weight: 20
```

---

## Q10 – Pod Pending due to PVC accessMode
**Context:** PVC accessMode mismatch blocks pod.

```bash
kubectl get pvc demo-pvc-cka29-trb
kubectl get pv demo-pv-cka29-trb
```

```bash
kubectl get pvc demo-pvc-cka29-trb -o yaml > /tmp/pvc.yaml
```

```text
ReadWriteOnce ❌
ReadWriteMany ✅
```

```bash
kubectl delete pvc demo-pvc-cka29-trb
kubectl apply -f /tmp/pvc.yaml
```

---

## Q11 – PVC + sidecar with readOnly mount
**Context:** Share volume with busybox (read-only).

```yaml
- name: busybox
  image: busybox
  command: ["sh","-c","sleep 10000"]
  volumeMounts:
  - name: python-data
    mountPath: /usr/src
    readOnly: true
```

---

## Q12 – Disable scheduler + manual scheduling
**Context:** Schedule pod manually, then restore scheduler.

```bash
mv /etc/kubernetes/manifests/kube-scheduler.yaml /tmp/
```

```yaml
nodeName: cluster2-controlplane
```

```bash
kubectl apply -f onyx-pod.yaml
mv /tmp/kube-scheduler.yaml /etc/kubernetes/manifests/
kubectl run ember-pod --image=redis --restart=Never
```

---

## Q13 – kube-apiserver liveness probe wrong port
**Context:** API flaps due to wrong liveness probe port.

```bash
kubectl logs kube-apiserver-cluster4-controlplane -n kube-system
```

```bash
vi /etc/kubernetes/manifests/kube-apiserver.yaml
```

```text
port: 6444 ❌
port: 6443 ✅
```

---

## Q14 – Service without endpoints (external app)
**Context:** Service points to external IP → manual EndpointSlice.

```bash
ping student-node
```

```yaml
apiVersion: discovery.k8s.io/v1
kind: EndpointSlice
metadata:
  name: external-webserver-cka03-svcn
  namespace: kube-public
  labels:
    kubernetes.io/service-name: external-webserver-cka03-svcn
addressType: IPv4
ports:
- port: 9999
  protocol: TCP
endpoints:
- addresses:
  - <student-node-ip>
```

---

## Q15 – Create ConfigMap from file
**Context:** Load file contents into ConfigMap.

```bash
kubectl create cm db-user-pass-cka17-arch \
--from-file=/opt/db-user-pass
```

---

## Q16 – NetworkPolicy too restrictive
**Context:** Allow access only from one pod in default namespace.

```bash
kubectl edit networkpolicy cyan-np-cka28-trb -n cyan-ns-cka28-trb
```

```yaml
ports:
- port: 80
  protocol: TCP
```

```yaml
ingress:
- from:
  - namespaceSelector:
      matchLabels:
        kubernetes.io/metadata.name: default
    podSelector:
      matchLabels:
        app: cyan-white-cka28-trb
```

---

## 🔑 Exam Pattern Hooks
- Pod Pending → **Resources / PVC / Scheduler**
- Restart loop → **OOMKilled**
- Helm cleanup → **helm ls -A**
- External service → **EndpointSlice**
- API flapping → **livenessProbe**
- NetworkPolicy → **ports + selectors**

