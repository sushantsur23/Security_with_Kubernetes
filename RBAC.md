# RBAC Role Based Access Control


# 🔐 Kubernetes RBAC (Role-Based Access Control)
## 📌 What is RBAC?

Suppose I want to give specific access to my pods — for example, I want a pod to only read config maps or only list pods.

How do we grant permissions to a Kubernetes pod?

In Kubernetes, RBAC is implemented using three main resources:
- 1. Service Account
- 2. Role
- 3. Role Binding

### 🧾 1️⃣ Service Account

A Service Account is just like a user account.

Just like any device requires a login account before performing tasks, every pod in Kubernetes runs with a service account.

If we do not explicitly mention a service account in the pod definition, Kubernetes automatically assigns the default service account.

#### ⚠️ However, using the default service account is not a good practice.

Best practice:
- Always create a dedicated service account.
- Assign only the required permissions.

🛡 2️⃣ Role

A Role defines what a service account or user account can do.

Permissions can include:
- Read pods
- Write config maps
- Read secrets
- Delete resources
- List deployments

Roles are defined within a specific namespace.

Example permissions:
- get
- list
- create
- delete
- update

Roles control:
- Which resources
- What actions
- In which namespace

#### 🔗 3️⃣ Role Binding

Role Binding connects:

👉 Service Account + Role

Administrators:
- Create service accounts
- Create roles
- Bind them together using RoleBinding

RoleBinding maps a role to a service account.


### 🧪 Practical Demo – RBAC Implementation
### 🎯 Scenario

We want to create a service account that can:
- ✅ List pods
- ✅ Get pods
- ❌ Cannot create/delete pods

🟢 Step 1: Create Service Account
```bash
kubectl create serviceaccount payments-user -n payments-ns
```

🟢 Step 2: Create Read-Only Role for Pods

Grant get and list permissions on pods.
```bash
kubectl apply -f - <<EOF
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: payments-ns
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
EOF
```


This Role allows:
- kubectl get pods
- kubectl list pods

Only within payments-ns.

🟢 Step 3: Bind Role to Service Account

```bash
kubectl apply -f - <<EOF
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods-binding
  namespace: payments-ns
subjects:
- kind: ServiceAccount
  name: payments-user
  namespace: payments-ns
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
EOF
```
This binds:
- Service Account → payments-user
- Role → pod-reader

🟢 Step 4: Verify Permissions
```bash
kubectl auth can-i list pods \
  --as=system:serviceaccount:payments-ns:payments-user \
  -n payments-ns

```

✅ Output should be: Yes
Now test restricted actions:
```bash
kubectl auth can-i create pods \
  --as=system:serviceaccount:payments-ns:payments-user \
  -n payments-ns
```

❌ Output should be: No

🌍 Cluster Role & Cluster Role Binding

If I want to grant payments-user permission to read pods across all namespaces in the cluster, then:

We use:
- ClusterRole
- ClusterRoleBinding


Difference:
| Role                   | ClusterRole                       |
| ---------------------- | --------------------------------- |
| Limited to a namespace | Applies across the entire cluster |

Use ClusterRole when:
- You want access across multiple namespaces
- You need cluster-wide permissions

🧠 Key Takeaways
- Every pod should use a dedicated Service Account.
- Roles define permissions.
- RoleBinding connects Role and Service Account.
- Use ClusterRole for cluster-wide access.
- Never rely on the default service account in production.

