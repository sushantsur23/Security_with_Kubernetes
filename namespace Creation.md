### 📦 Kubernetes Namespace Creation & Workload Deployment

This document explains how to:
- Create a Kubernetes cluster using kind
- Create namespaces
- Deploy workloads into specific namespaces
- Verify deployments

🚀 Step 1: Create Kubernetes Cluster

Make sure:
- ✅ Docker is installed and running
- ✅ kind is installed

Then create a cluster:
```bash
kind create cluster --name=k8s-demo
```
This will create a local Kubernetes cluster named k8s-demo.

### 🏷️ Step 2: Create Namespaces

Namespaces help logically isolate workloads within a cluster.

Create Namespaces
```bash
kubectl create ns payments-ns
kubectl create ns scoreboard-ns
```

Verify Namespaces
```bash
kubectl get ns
```

This command gives the list of all namespaces in the cluster.

### 📦 Step 3: Deploy Workload to a Namespace

Let’s understand how workloads are deployed to a namespace.

Example Scenario

DevOps engineer of the payments namespace wants to deploy nginx into the payments-ns namespace.

Create Deployment
```bash
kubectl create deployment nginx-payments --image=nginx -n payments-ns
```

- --image=nginx → Specifies container image
- -n payments-ns → Specifies namespace

### 🔍 Step 4: Verify Deployment
Check Pods Without Namespace
```bash
kubectl get pods
```

This will show no pods, because it checks only the default namespace.

Check Pods in payments Namespace
```bash
kubectl get pods -n payments-ns
```

Now you will see the nginx pod running in the payments-ns namespace.


#### 🧠 Key Takeaways
- Namespaces provide logical isolation.
- Always use -n <namespace> when deploying workloads.
- If no namespace is specified, Kubernetes uses the default namespace.
- Use kubectl get ns to verify namespaces.
- Use kubectl get pods -n <namespace> to check workloads inside a namespace.

