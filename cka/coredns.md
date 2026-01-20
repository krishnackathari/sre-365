## 🧠 COREDNS CONFIG CHANGE (MOCK-PROVEN PATTERN)

### SCENARIO
• DNS resolution not working as expected  
• New domain / rewrite / forward rule added  
• CoreDNS config updated but behavior unchanged  

👉 **ConfigMap edits do NOT auto-apply**

---

### STEP 1️⃣ Edit CoreDNS ConfigMap
```bash
kubectl edit cm coredns -n kube-system
```

You are usually modifying:
```text
.:53 {
    errors
    health
    kubernetes cluster.local in-addr.arpa ip6.arpa {
        pods insecure
        fallthrough in-addr.arpa ip6.arpa
    }
    forward . /etc/resolv.conf
    cache 30
}
```

Examples of exam edits:
• Add new domain
• Add rewrite
• Fix forward rule
• Add stub domain

---

### STEP 2️⃣ RESTART CoreDNS (MANDATORY)
```bash
kubectl rollout restart deployment coredns -n kube-system
```

OR (older clusters):
```bash
kubectl delete pod -n kube-system -l k8s-app=kube-dns
```

---

### STEP 3️⃣ VERIFY
```bash
kubectl get pods -n kube-system
kubectl logs -n kube-system -l k8s-app=kube-dns
```

Optional test:
```bash
kubectl run test --rm -it --image=busybox:1.28 -- nslookup kubernetes.default
```

---

### EXAM MEMORY HOOK 🔒
❗ **ConfigMap change ≠ live reload**  
❗ **CoreDNS ALWAYS needs a restart**

If DNS question appears:
👉 Edit CM  
👉 Restart deployment  
👉 Verify once  
👉 Move on


