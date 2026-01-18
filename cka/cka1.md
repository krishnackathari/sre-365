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

