# Kubernetes Gateway API – What It Is, Architecture, Features & Ingress vs Gateway API

This README explains:

* What Gateway API is
* Why it was created
* Its architecture & components
* Key features
* How it works in production
* Gateway API vs Ingress (full comparison)

---

# 1. What Is Gateway API?

**Gateway API** is the modern, advanced, and extensible API for handling traffic into and inside Kubernetes clusters.

It is designed to replace or extend **Ingress** with:

* More features
* Better flexibility
* Stronger traffic control
* Better integration with service meshes

### Simple Definition

> **Gateway API is the next-generation Kubernetes networking standard for HTTP, HTTPS, TCP, UDP, TLS, and internal/mesh routing.**

It gives us:

* Gateways
* Routes
* Policies
* Cross-namespace routing

Unlike Ingress, Gateway API is built for real production enterprise needs.

---

# 2. Why Gateway API Was Created

Ingress is too limited:

* Only HTTP/HTTPS
* Only host/path routing
* No header routing
* No traffic splitting
* No canary support
* No native TCP/UDP support
* No mesh support
* No admin/user separation

Modern teams needed:

* More routing power
* Multi-tenant gateways
* Traffic splitting
* mTLS support
* Advanced filters (auth, rate limits, retries)
* A standardized API

Gateway API solves all these problems.

---

# 3. Gateway API Architecture

Gateway API uses several API objects to define traffic flow.

## ✔ 1. **GatewayClass**

Defines the *type* of gateway.
Example:

* nginx
* traefik
* istio
* envoygateway
* aws-lb

Equivalent to “IngressClass”.

## ✔ 2. **Gateway**

Represents the actual gateway endpoint.
Often backed by:

* Cloud Load Balancer (AWS, GCP, Azure)
* Envoy proxy
* NGINX proxy

Gateways expose listeners:

* HTTP
* HTTPS
* TCP
* UDP

## ✔ 3. **Routes**

Routes define how traffic should be forwarded.
Types:

* **HTTPRoute** (most common)
* **TCPRoute**
* **UDPRoute**
* **TLSRoute**
* **GRPCRoute**

Routes attach to Gateways.

## ✔ 4. **ReferencePolicy / ReferenceGrant**

Allows cross-namespace binding (safe multi-tenant routing).

## ✔ 5. **Policies**

Optional objects for configuration like:

* TimeoutPolicy
* RetryPolicy
* TLSPolicy
* AuthPolicy
* RateLimitPolicy

---

# 4. Gateway API Architecture Diagram

```
                +---------------------------+
                |      GatewayClass         |
                +-------------+-------------+
                              |
                              v
                +---------------------------+
                |         Gateway           |
                |  (LoadBalancer / Proxy)   |
                +-------------+-------------+
                              |
                     Listeners: 80, 443
                              |
            +-----------------+-----------------+
            |                 |                 |
            v                 v                 v
       HTTPRoute          TCPRoute         GRPCRoute
            |                 |                 |
            v                 v                 v
       Services → Pods   Services → Pods   Services → Pods
```

---

# 5. Features of Gateway API

## ✔ 1. Multi-protocol Support

* HTTP
* HTTPS
* TCP
* UDP
* TLS
* gRPC

Ingress only supports **HTTP/HTTPS**.

---

## ✔ 2. Traffic Splitting & Canary Deployments

Natively supports:

* Weighted routing
* Canary releases
* A/B testing
* Mirroring

Example:

```
70% → v1
30% → v2
```

---

## ✔ 3. Multi-Tenancy / Admin–User Separation

Admins manage Gateways.
Application teams manage Routes.

Better security and separation of responsibilities.

---

## ✔ 4. Cross-Namespace Routing

Route in one namespace can use Gateway in another namespace.

Ingress cannot do this safely.

---

## ✔ 5. Native Support for Service Mesh

Works seamlessly with:

* Istio
* Linkerd
* Envoy Gateway
* Kuma

Gateway API can act as:

* Ingress gateway
* Mesh gateway
* East–west traffic gateway

---

## ✔ 6. Advanced L7 Features

Supports rules based on:

* Headers
* Cookies
* Query params
* Path rewrites
* Filters
* Authentication
* Authorization
* Timeout / retry
* TLS mode

Ingress only supports basic host/path routing.

---

# 6. How Gateway API Works in Production

### Flow in cloud (EKS/GKE/AKS):

```
Internet
  ↓
Cloud Load Balancer (provisioned by Gateway)
  ↓
Gateway Pod (Envoy/NGINX/Istio)
  ↓
HTTPRoute
  ↓
Service
  ↓
Pods
```

### Key Points:

* Gateway object provisions cloud LB
* Gateway Controller manages Gateway
* Routes attach to Gateways
* Routes forward traffic to Services

This is more structured than Ingress.

---

# 7. Gateway API vs Ingress (Full Comparison)

| Feature              | Ingress              | Gateway API                      |
| -------------------- | -------------------- | -------------------------------- |
| Protocols            | HTTP/HTTPS           | HTTP, HTTPS, TCP, UDP, gRPC, TLS |
| Traffic splitting    | ❌ No                 | ✔ Yes (native)                   |
| Canary routing       | ❌ No                 | ✔ Yes                            |
| Header-based routing | Limited              | ✔ Full support                   |
| Query param routing  | ❌ No                 | ✔ Yes                            |
| Multi-tenancy        | ❌ Weak               | ✔ Strong (admin vs app team)     |
| Cross-namespace      | ❌ No                 | ✔ Yes (ReferenceGrant)           |
| Multiple Gateways    | ❌ Hard               | ✔ Easy                           |
| Config extensibility | ❌ Limited            | ✔ Highly extensible              |
| Service Mesh support | ❌ No                 | ✔ Yes                            |
| TCP/UDP support      | ❌ No                 | ✔ Yes                            |
| Standard for future  | Deprecated direction | ✔ Future of K8s ingress          |

### One-line summary:

> **Ingress = simple**
> **Gateway API = modern, powerful, cloud-native routing standard**

---

# 8. Why Companies Are Moving to Gateway API

* Ingress cannot handle advanced use cases
* Cloud providers support Gateway natively
* Istio and Envoy use Gateway API by default
* Multi-tenant platforms need it
* Canary traffic is required in modern DevOps

Gateway API = future replacement for Ingress.

---

