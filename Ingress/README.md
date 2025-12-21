# Kubernetes Ingress – What It Is, How It Works in Production, Ingress Controller & IngressClass
---
# 1. What Is Ingress?

**Ingress** is a Kubernetes API object that manages **external access** to your applications using **HTTP/HTTPS**.

Ingress provides:

* Host-based routing (example: api.example.com)
* Path-based routing (example: /api → backend service)
* TLS/HTTPS termination
* One LoadBalancer for multiple services
* Reverse proxy rules

### Simple Definition

> **Ingress = Rules for routing external HTTP/HTTPS traffic into Kubernetes services.**

---

# 2. Why Do We Need Ingress?

Without ingress:

* Every app needs its own LoadBalancer (expensive in cloud)
* NodePort is not production friendly
* No central TLS management

Ingress solves this by:

* Using **one LoadBalancer** for all apps
* Managing multiple domains/paths
* Handling automatic TLS certificates

---

# 3. How Ingress Works (Simple Flow)

When a user makes a request:

```
User → LoadBalancer (Cloud) → Ingress Controller → Service → Pods
```

### Step-by-step:

1. User requests: [https://myapp.com](https://myapp.com)
2. Cloud LoadBalancer sends the request to **Ingress Controller**
3. Ingress Controller reads Ingress rules
4. Controller forwards traffic to correct **Service** in the cluster
5. Service forwards to **Pods**

---

# 4. What Is an Ingress Controller?

Ingress itself is **just rules**.
It does NOT perform routing.

To actually route traffic, Kubernetes needs an **Ingress Controller**, such as:

* **NGINX Ingress Controller (most popular)**
* **Traefik**
* **HAProxy**
* **Istio Gateway** (when using service mesh)
* **AWS ALB Ingress Controller**
* **GCP Ingress**

### Simple Definition

> **Ingress Controller = the real software that reads Ingress rules and routes traffic.**

Without an Ingress Controller → Ingress does nothing.

---

# 5. How Ingress Controller Works Internally

### 1. Watches the Kubernetes API Server

It keeps watching for new or updated Ingress objects.

### 2. Reads Ingress rules and generates configuration

Example: NGINX config, Envoy config, Traefik config.

### 3. Applies rules dynamically

No restart required.

### 4. Routes traffic to the correct Service

Using ClusterIP

### 5. Self-healing and reloads when needed

If pod deleted → controller updates routing rules.

---

# 6. What Is IngressClass?

IngressClass defines **which Ingress Controller will handle which Ingress**.

Example:
You might have:

* One controller for public traffic
* One controller for internal traffic

IngressClass decides:

> “This Ingress belongs to THIS controller.”

### Example

```yaml
metadata:
  annotations:
    kubernetes.io/ingress.class: nginx
```

OR the modern way:

```yaml
spec:
  ingressClassName: nginx
```

---

# 7. Types of Routing in Ingress

### 📌 1. Host-Based Routing

```
api.example.com → backend-api-service
web.example.com → frontend-service
```

### 📌 2. Path-Based Routing

```
/app → app-service
/api → api-service
```

---

# 8. Ingress YAML Example

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: myapp-ingress
spec:
  ingressClassName: nginx
  rules:
  - host: myapp.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: myapp-service
            port:
              number: 80
```

---

# 9. TLS/HTTPS with Ingress

Ingress supports HTTPS termination.

```yaml
spec:
  tls:
  - hosts:
    - myapp.com
    secretName: myapp-tls
```

If using **cert-manager**, certificates are automatically created.

---

# 10. How Ingress Works in Production

### 🔵 Step 1: Cloud LoadBalancer receives traffic

Example in AWS: ELB / ALB

### 🔵 Step 2: Traffic forwarded to Ingress Controller

Usually running on multiple nodes for HA.

### 🔵 Step 3: Ingress Controller matches rules

* Check host rules
* Check path rules
* Apply annotations

### 🔵 Step 4: Ingress Controller routes request to correct service

```
Ingress Controller → ClusterIP Service → Pods
```

### 🔵 Step 5: Service load-balances traffic to multiple pods

Works with EndpointSlices.

---

# 11. Production Features of Ingress

## ✔ TLS termination

All HTTPS traffic ends at Ingress.

## ✔ Auto certificates (with cert-manager)

Cert-manager + Ingress = automatic Let’s Encrypt.

## ✔ Canary / Blue-Green routing

With annotations or service mesh.

## ✔ Path rewriting

Useful for legacy apps.

## ✔ Rate limiting, WAF, IP allow/deny

Supported via Ingress Controller annotations.

---

# 12. Production Architecture Diagram

```
User
  ↓
Cloud LoadBalancer (AWS ELB / Azure LB / GCP LB)
  ↓
Ingress Controller (Nginx / Traefik)
  ↓
Ingress Rules
  ↓
ClusterIP Service
  ↓
Pods
```

---

