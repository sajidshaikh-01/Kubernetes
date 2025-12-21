# What is kubectl? (Kubernetes CLI) – 

## 1. Introduction

**kubectl** (pronounced *cube-control* or *cube-c-t-l*) is the **command-line interface (CLI)** for interacting with a Kubernetes cluster.

It allows you to **deploy applications, inspect resources, troubleshoot clusters, and manage Kubernetes objects**.

Whenever you run a kubectl command, it communicates with the **Kubernetes API Server**.

---

## 2. How kubectl Works

1. User runs a kubectl command (example: `kubectl get pods`).
2. kubectl reads your **kubeconfig file** (usually `~/.kube/config`).
3. kubeconfig contains:

   * Cluster API endpoint
   * User credentials (cert/token)
   * Namespace
   * Contexts
4. kubectl sends a REST API request to the **API Server**.
5. API Server returns the response.

### **Architecture Flow**

```
kubectl → kubeconfig → API Server → etcd / Controllers → Response
```

---

## 3. kubeconfig File

kubectl uses a file called **kubeconfig** to know:

* Which cluster to connect to
* How to authenticate
* Which namespace to use

Default location:

```
~/.kube/config
```

Example kubeconfig entry:

```yaml
apiVersion: v1
clusters:
- cluster:
    server: https://127.0.0.1:6443
  name: kubernetes
```

---

## 4. Common kubectl Commands (Most Used)

### **Get resources**

```
kubectl get pods
kubectl get nodes
kubectl get svc
kubectl get deployments
```

### **Describe resources**

```
kubectl describe pod nginx
```

### **Create & Apply**

```
kubectl apply -f file.yaml
kubectl create -f file.yaml
```

### **Edit resource**

```
kubectl edit deployment nginx-deploy
```

### **Delete**

```
kubectl delete pod nginx
```

### **Execute inside a Pod**

```
kubectl exec -it nginx -- /bin/bash
```

### **View logs**

```
kubectl logs nginx
kubectl logs nginx -f
```

---

## 5. Namespaces

Use `-n` flag to work with different namespaces.

```
kubectl get pods -n kube-system
```

Set a default namespace:

```
kubectl config set-context --current --namespace=dev
```

---

## 6. Resource Shortcuts

Kubernetes has shortcuts for commonly used resources:

* `po` = pods
* `svc` = services
* `deploy` = deployment
* `rs` = replicaset
* `ns` = namespace

Example:

```
kubectl get po
```

---

## 7. YAML Dry Run (Generate YAML)

Used for creating YAML without applying:

```
kubectl create deployment nginx --image=nginx --dry-run=client -o yaml
```

---

## 8. kubectl Context Management

List contexts:

```
kubectl config get-contexts
```

Switch context:

```
kubectl config use-context dev-cluster
```

---

## 9. Debugging & Troubleshooting with kubectl

```
kubectl logs
kubectl exec
kubectl describe
kubectl get events
kubectl top nodes
kubectl debug
```

These commands are essential for debugging pods, nodes, and workloads.

---

## 10. Advanced kubectl Usage

### **Port Forwarding**

```
kubectl port-forward pod/web 8080:80
```

### **Scaling**

```
kubectl scale deployment nginx --replicas=5
```

### **Rollout Management**

```
kubectl rollout status deployment nginx
kubectl rollout history deployment nginx
kubectl rollout undo deployment nginx
```

