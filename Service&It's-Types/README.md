# Kubernetes Services – Types, Explanation & Production Use Cases

A **Service** in Kubernetes is an abstraction that provides **stable networking** to connect applications.
Pods are ephemeral and their IPs change, so Services give a **fixed IP and DNS name** to access a group of Pods.

---

# 1. What Is a Service?

A Kubernetes **Service** is an object that:

* Exposes Pods on a stable IP
* Load-balances traffic across Pods
* Uses **selectors** to discover Pods
* Provides DNS name inside the cluster
* Enables communication between microservices

### Simple Definition

> **Service = stable networking + load balancing for pods**

---

# 2. Why Do We Need a Service?

Pods:

* Restart
* Get recreated
* Move to different nodes
* Get new IP addresses

If app tries to connect directly to Pod IP, connection fails.

Service solves this by giving:

* Permanent IP
* Permanent DNS name
* Automatic load balancing

Example: `myapp-service.default.svc.cluster.local`

---

# 3. How a Service Works

1. Service has a **selector**.
2. It finds all Pods **matching the label**.
3. Service creates an **Endpoints** object with Pod IPs.
4. Traffic sent to Service is load-balanced across these Pods.

```
Service → Endpoints → Pod IPs
```

---

# 4. Types of Services

Kubernetes has **4 main service types**, each used differently in production.

---

# 4.1 ClusterIP (Default)

### What it does:

* Exposes the Service **inside** the cluster only.
* Not accessible from the internet.

### Use Case:

Internal microservice communication.

Example:

* Auth service → User service
* Backend → Database

### Production Example:

```
frontend → (ClusterIP) → backend
backend → (ClusterIP) → database
```

### YAML:

```yaml
spec:
  type: ClusterIP
  selector:
    app: myapp
  ports:
    - port: 80
      targetPort: 8080
```

---

# 4.2 NodePort

### What it does:

* Exposes the service on **each node’s IP** at a static port (30000–32767)
* Accessible **outside cluster**, but not production-friendly.

### How it works:

```
http://<NodeIP>:<NodePort>
```

### Use Case:

* Local testing
* Minikube
* Debugging

### Not recommended for production.

### YAML:

```yaml
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: 8080
      nodePort: 30080
```

---

# 4.3 LoadBalancer

### What it does:

* Exposes service externally using cloud provider’s load balancer.
* Gives a **public IP**.
* Best for production if not using Ingress.

### Production Use Case:

* Expose APIs to public internet
* Expose UI applications

### Common clouds:

* AWS (ELB/NLB)
* Azure (Load Balancer)
* GCP (Cloud Load Balancer)

### YAML:

```yaml
spec:
  type: LoadBalancer
  ports:
    - port: 80
      targetPort: 8080
  selector:
    app: myapp
```

---

# 4.4 ExternalName

### What it does:

Maps a Kubernetes service name → external DNS name.

No port, no selector, no endpoints.

### Use Case:

When accessing:

* External databases
* External APIs
* External legacy systems

### Example:

If you want to access a database at `db.company.com`:

```yaml
spec:
  type: ExternalName
  externalName: db.company.com
```

Apps inside cluster can now use:

```
mysql.externaldb.svc.cluster.local
```

---

# 5. Service Discovery in Kubernetes

DNS automatically assigns:

```
<service-name>.<namespace>.svc.cluster.local
```

Example:

```
backend.default.svc.cluster.local
```

This makes microservice communication simple.

---

# 6. Production Use Cases for Each Service Type

### ClusterIP → Internal microservices

* backend
* auth
* redis
* payment processing

### NodePort → Only for testing

* Minikube
* Local debugging

### LoadBalancer → Internet-facing services

* Node.js API
* React/Angular frontend
* Payment gateway endpoints

### ExternalName → external systems

* RDS databases
* External APIs
* Legacy systems

---

# 7. How Services Provide Load Balancing

Service load balances at **pod IP level** using kube-proxy.
Kube-proxy uses:

* iptables OR
* IPVS (better performance)

### Service balances like this:

```
Service IP
 ↓
Pod 1
Pod 2
Pod 3
```

---

# 8. Important Concepts

### ✔ Services + Labels = Routing

Service uses selector:

```
selector:
  app: myapp
```

Pods must have:

```
labels:
  app: myapp
```

### ✔ Services do NOT talk to Deployments

They talk only to **Pods**.

### ✔ Services are stable

Pods are not.

---

# 9. Summary

* **Service** = stable networking + internal/external access + load balancing.
* **ClusterIP** = internal communication.
* **NodePort** = external testing.
* **LoadBalancer** = production external access.
* **ExternalName** = DNS redirection.

If you want, I can also add:

* Service + Deployment full example
* LoadBalancer with Ingress example
* Troubleshooting (why service not connecting to pods)
* Diagram for each service type

---

## 10. Service Discovery Diagrams

### **1. How a Service Finds Pods (Label Selector Matching)**

```
        +--------------------------+
        |        Deployment        |
        |  (creates Pods w/ label) |
        +-----------+--------------+
                    |
                    v
           +----------------+
           |     Pods       |
           | app=myapp      |
           | Pod A, B, C    |
           +--------+-------+
                    ^
                    |
     selector: app=myapp
                    |
           +--------+-------+
           |     Service    |
           |  ClusterIP     |
           +----------------+
```

➡ Service checks for **app=myapp** label → connects to Pods automatically.

---

### **2. Service → Endpoints → Pods Flow**

```
Client
  |
  v
Service (Stable IP)
  |
  v
Endpoints (List of Pod IPs)
  |
  v
Pods (Changing IPs)
```

➡ Endpoints object acts as a dynamic list that updates when pods change.

---

### **3. ClusterIP Internal Communication Diagram**

```
        [frontend pod]
               |
     (DNS: backend.default.svc)
               |
               v
      [backend ClusterIP Service]
               |
      load balances across
     /       |        \
[backend A] [backend B] [backend C]
```

---

### **4. NodePort External Access Diagram**

```
User → http://NodeIP:30080
            |
            v
        NodePort Service
            |
            v
     Load-balances to Pods
```

Used only for testing.

---

### **5. LoadBalancer External Access (Cloud)**

```
Internet User
     |
     v
Cloud Load Balancer (AWS ELB / ALB)
     |
     v
LoadBalancer Service
     |
     v
Pods (via Endpoints)
```

➡ Best for production external traffic.

---

### **6. Service Discovery using DNS**

Every service gets DNS:

```
<service>.<namespace>.svc.cluster.local
```

Example:

```
backend.default.svc.cluster.local
```

Used by microservices to talk to each other.

---

## 11. Troubleshooting: Service Not Connecting to Pods

### **1. Labels Don’t Match (MOST COMMON)**

```
Service selector: app=backend
Pod label: app=backend-api
```

❌ No match → Service has no endpoints.

Fix by making labels identical.

---

### **2. Pod Not Ready (Readiness Probe Failed)**

Pod will NOT be added to Service until ready.

Run:

```
kubectl describe pod
```

Look for readiness probe failures.

---

### **3. Endpoints Object Is Empty**

Check:

```
kubectl get endpoints <service>
```

If empty → something wrong with labels.

---

### **4. Wrong TargetPort**

Container exposes 8080 but service uses 80 → no connection.

---

### **5. NetworkPolicy Blocking Traffic**

If using NetworkPolicy, ensure traffic allowed.

---

## 12. Service + Deployment Working Example

### **Deployment**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deploy
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

### **Service**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: ClusterIP
  selector:
    app: myapp
  ports:
    - port: 80
      targetPort: 80
```
