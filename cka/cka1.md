# CKA Mock Exam – Ultra-Fast Cheat Sheet (with Context)

---

## Q1 – HPA (CPU based autoscaling)
**Context:** Scale a deployment based on CPU (70%), min 2 pods, max 10.

```bash
kubectl autoscale deployment frontend-deployment \
  --cpu-percent=70 --min=2 --max=10
```

---

## Q2 – RBAC: ServiceAccount can only GET namespaces
**Context:** Restrict an existing ServiceAccount to read namespaces only.

```bash
kubectl edit clusterrole green-role-cka22-arch
```

```yaml
- apiGroups: [""]
  resources: ["namespaces"]
  verbs: ["get"]
```

Verify:
```bash
kubectl auth can-i get namespaces \
--as=system:serviceaccount:default:green-sa-cka22-arch
```

---

## Q3 – Deployment DESIRED ≠ CURRENT
**Context:** Deployment won’t scale because controller manager is broken.

```bash
kubectl get pods -n kube-system
```

```bash
vi /etc/kubernetes/manifests/kube-controller-manager.yaml
```

```text
kube-controller-manage  ❌
kube-controller-manager ✅
```

```bash
kubectl delete pod kube-controller-manager-<node> -n kube-system
```

---

## Q4 – Resize PVC (Static PV)
**Context:** Increase PVC from 50Mi → 80Mi (static volume).

```bash
kubectl patch pv papaya-pv-cka09-str --type=json \
-p='[
{"op":"remove","path":"/spec/claimRef"},
{"op":"remove","path":"/status"}
]'
```

```bash
kubectl patch pvc papaya-pvc-cka09-str --type=json \
-p='[{"op":"replace","path":"/spec/resources/requests/storage","value":"80Mi"}]'
```

---

## Q5 – Helm chart fix & deploy
**Context:** Fix broken Helm templates and deploy with given specs.

```bash
kubectl create ns frontend-apd
```

Edit:
- `Chart.yaml` → `appVersion: 1.20.0`
- `values.yaml`
```yaml
replicaCount: 3
service:
  type: NodePort
```

Template fixes:
```text
Deployment apiVersion → apps/v1
Service template → {{ .Values.service.name }}
```

```bash
helm lint /opt/webapp-color-apd
helm install webapp-color-apd -n frontend-apd /opt/webapp-color-apd
```

---

## Q6 – Gateway API (HTTPRoute)
**Context:** Route traffic from Gateway → backend service.

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: web-route
  namespace: nginx-gateway
spec:
  hostnames: ["cluster2-controlplane"]
  parentRefs:
  - name: web-gateway
  rules:
  - backendRefs:
    - name: web-service
      port: 80
```

---

## Q7 – RBAC: Create SA, Role, Binding
**Context:** Allow ServiceAccount to GET deployments.

```bash
kubectl create sa deploy-cka20-arch
kubectl create clusterrole deploy-role-cka20-arch \
--resource=deployments --verb=get
kubectl create clusterrolebinding deploy-role-binding-cka20-arch \
--clusterrole=deploy-role-cka20-arch \
--serviceaccount=default:deploy-cka20-arch
```

---

## Q8 – Service & Pod DNS
**Context:** Verify service and pod name resolution.

```bash
kubectl run nginx-resolver-cka06-svcn --image=nginx
kubectl expose pod nginx-resolver-cka06-svcn \
--name=nginx-resolver-service-cka06-svcn \
--port=80 --type=ClusterIP
```

```bash
kubectl run test --rm -it --image=busybox:1.28 -- \
nslookup nginx-resolver-service-cka06-svcn
```

```bash
IP=$(kubectl get pod nginx-resolver-cka06-svcn -o wide --no-headers | awk '{print $6}' | tr '.' '-')
kubectl run test --rm -it --image=busybox:1.28 -- \
nslookup $IP.default.pod
```

---

## Q9 – Pod stuck in Pending
**Context:** Scheduler is crashing → pods never schedule.

```bash
kubectl get pods -n kube-system
kubectl logs kube-scheduler-<node> -n kube-system
```

```bash
vi /etc/kubernetes/manifests/kube-scheduler.yaml
```

```text
/etc/kubernetes/scheduler.config ❌
/etc/kubernetes/scheduler.conf   ✅
```

---

## Q10 – Rollback Deployment
**Context:** Bad rollout → revert and scale.

```bash
kubectl rollout undo deploy webapp-wl07 -n dev-wl07
kubectl describe deploy webapp-wl07 -n dev-wl07 | grep Image
echo "kodekloud/webapp-color" > /root/rolling-back-record.txt
kubectl scale deploy webapp-wl07 -n dev-wl07 --replicas=5
```

---

## Q11 – CreateContainerConfigError (Secrets)
**Context:** Pod fails due to wrong secret keys.

```bash
kubectl logs <pod-name>
kubectl get events --field-selector involvedObject.name=<pod-name>
kubectl edit deploy db-deployment-cka05-trb
```

```text
Fix env key names to match Secret keys
Do NOT edit Secrets
```

---

## Q12 – RollingUpdate + Rollback
**Context:** Control rollout behavior and rollback.

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 40%
    maxSurge: 55%
```

```bash
kubectl set image deploy ocean-tv-wl09 webapp-color=kodekloud/webapp-color:v2
kubectl rollout history deploy ocean-tv-wl09
echo "2" > /opt/revision-count.txt
kubectl rollout undo deploy ocean-tv-wl09
```

---

## Q13 – Ingress (nginx)
**Context:** Expose service via ingress.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-ingress-cka04-svcn
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
spec:
  ingressClassName: nginx-cka04
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx-service-cka04-svcn
            port:
              number: 80
```

---

## Q14 – Deployment paused
**Context:** Deployment shows 0 UP-TO-DATE.

```bash
kubectl rollout status deploy black-cka25-trb
kubectl rollout resume deploy black-cka25-trb
```

---

## Q15 – Service targetPort mismatch
**Context:** App unreachable due to wrong port.

```bash
kubectl edit svc purple-svc-cka27-trb
```

```text
targetPort: 8080 ❌
targetPort: 80   ✅
```

---

## Q16 – StorageClass
**Context:** Create local storage class.

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: banana-sc-cka08-str
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
```

---

## 🔑 Exam Pattern Memory Hooks
- Pending pod → **Scheduler**
- Replicas not updating → **Controller Manager**
- 0/1 Ready → **Secrets / env mismatch**
- Service broken → **targetPort**
- Deployment stuck → **rollout resume**
- Static PVC resize → **claimRef**

