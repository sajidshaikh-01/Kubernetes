# Kubernetes Namespaces-

## 1. What is a Namespace?

A **Namespace** in Kubernetes is a **logical partition** inside a cluster that helps you organize and isolate resources.

Think of it like **folders** inside your computer:

* The computer = **Cluster**
* The folders = **Namespaces**
* The files inside folders = **Pods, Services, Deployments, ConfigMaps, Secrets**

Namespaces help with:

* Isolation
* Organizing microservices
* Applying different access controls (RBAC)
* Managing quotas and limits

---

## 2. Why Namespaces Exist?

In production, you may have:

* Many teams
* Many microservices
* Dev/Staging/Prod environments
* Shared cluster

Without namespaces, everything would be mixed up and messy.

Namespaces allow teams to work independently without interfering with each other.

---

## 3. Default Namespaces in Kubernetes

Kubernetes automatically creates some namespaces when the cluster starts.

### ### **1. default**

* Used when you **don’t specify a namespace**.
* Simple, no restrictions.
* Good for small apps or quick testing.

### **2. kube-system**

* Contains **system-level components**:

  * kube-apiserver
  * kube-scheduler
  * kube-dns (CoreDNS)
  * kube-proxy
* Do NOT put your application here.

### **3. kube-public**

* Contains resources that need to be **publicly accessible**.
* Anyone can read from this namespace.
* Rarely used in day-to-day production.

### **4. kube-node-lease**

* Stores lease objects used for **node heartbeat**.
* Helps detect node failures quickly.
* Only for internal cluster operations.

---

## 4. Difference Between Cluster and Namespace

### **Cluster**

* Entire Kubernetes system
* Contains **all nodes + namespaces + workloads**
* One API server controls everything

### **Namespace**

* Logical divider *inside* the cluster
* Used to separate resources for:

  * Teams
  * Environments
  * Projects

### Analogy:

* **Cluster = Entire building**
* **Namespace = Separate offices inside the building**

---

## 5. Production Best Practices for Namespaces

### **1. Use Namespace Per Environment**

```
dev
stage
prod
```

Keeps deployments clean and isolated.

### **2. Namespace Per Team or Project**

```
team-payments
team-orders
team-auth
```

Every team manages their own resources.

### **3. Apply Resource Quotas Per Namespace**

Prevent one team from consuming all CPU/memory.

```
requests.cpu: 10
limits.cpu: 20
```

### **4. Apply RBAC Per Namespace**

Example:

* Dev team can only access `team-dev` namespace.
* Platform team can access all namespaces.

### **5. Separate Monitoring Namespace**

```
monitoring
```

Place:

* Prometheus
* Grafana
* Loki
* Tempo

### **6. Separate Ingress/Gateway Namespace**

```
ingress-nginx
gateway-system
```

Keeps networking clean.

### **7. Separate Namespace for CI/CD tools**

```
jenkins
argo-cd
tekton
```

### **8. Never Put Apps in kube-system**

Reserved only for system pods.

### **9. Keep prod minimal**

Production namespace should only have production apps.
Avoid mixing dev/stage workloads.

### **10. Label Namespaces Properly**

Useful for automation.

```
app=payments
env=prod
team=backend
```

---

## 6. Quick Visual Summary

```
Cluster
├── dev (team-dev apps)
├── stage (pre-production apps)
├── prod (live apps)
├── monitoring (Prometheus, Grafana)
├── ingress-nginx (Ingress controller)
└── kube-system (system pods)
```


