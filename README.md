# 🔐 Kubernetes Security Essentials

This repository focuses on core Kubernetes security practices that every DevOps / DevSecOps engineer should implement in production environments.

Rather than relying on a single control, Kubernetes security is built in layers.
This project demonstrates three foundational layers:

### - 🧱 Namespaces (Isolation)
### - 👤 RBAC (Access Control)
### - 🌐 Network Policies (Traffic Control)

Together, these form the baseline security posture of any Kubernetes cluster.

## 📌 Why This Repository?

Kubernetes by default is very permissive:

- Pods can talk to each other
- Users can access resources broadly
- No strong isolation between teams

This repo shows how to lock things down properly.

## 🧱 1. Namespace – Logical Isolation

Namespaces provide environment and team-level isolation within a cluster.

Without namespaces:

Any team can accidentally modify or delete another team’s resources.

### ✅ Create Namespace
```bash
kubectl create namespace dev
kubectl create namespace prod
```

### 📄 YAML Example

```bash
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

### 💡 Use Case
Separate environments (dev, staging, prod)
Multi-team clusters
Resource quota management
## 👤 2. RBAC – Role Based Access Control

RBAC ensures who can do what inside the cluster.

It is built using:

- ServiceAccount → Identity
- Role → Permissions
- RoleBinding → Mapping
✅ Example: Read-only access to pods
Role
```bash
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: dev
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

RoleBinding
```bash
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: dev
subjects:
- kind: ServiceAccount
  name: default
  namespace: dev
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io

```

### 💡 Why RBAC Matters
- Prevent unauthorized access
- Enforce least privilege
- Protect sensitive resources (Secrets, ConfigMaps)

## 🌐 3. Network Policies – Pod Communication Control

By default:
- All pods can communicate with all other pods.
- This is a major security risk.
- Network Policies act like internal firewalls.


✅ Example: Allow only frontend → backend traffic
```bash
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend
  namespace: dev
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

### 🚫 What This Blocks
- Any pod other than frontend cannot access backend
- Prevents lateral movement in case of attack

### ⚠️ Important Note

Network policies work only if your cluster supports it.

You need a CNI plugin like:
- Calico
- Cilium
- Weave

🧠 Putting It All Together

| Layer            | Purpose             |
| ---------------- | ------------------- |
| Namespaces       | Isolation           |
| RBAC             | Access control      |
| Network Policies | Traffic restriction |


### 📌 Best Practices
- Always use namespaces for team separation
- Never give cluster-admin access unnecessarily
- Follow least privilege principle
- Use network policies to restrict traffic
Combine with:
- - Pod Security Standards
- - Secrets Management
- - Image Scanning


### 🏁 Conclusion

Kubernetes security is not a single feature — it’s a layered approach.

If you implement:
- Namespaces
- RBAC
- Network Policies

You’ve already covered 70% of real-world Kubernetes security risks.

### ⭐ If You Found This Useful

Give this repo a ⭐ and share it with your DevOps community!