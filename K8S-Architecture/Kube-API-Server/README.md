# Kubernetes API Server (kube-apiserver)

The **Kube API Server** is the central control-plane component of Kubernetes. It acts as the **front door** for all cluster operations and is responsible for handling **all API requests** (kubectl, controllers, operators, UI, etc.).

It exposes the Kubernetes API, validates requests, enforces security, and updates the cluster state stored in **etcd**.

---

# 🏗️ Architecture of Kube API Server

Below is the internal architectural flow:

```
Client / Component
    |
    |  REST API Request (kubectl, kubelet, controllers)
    V
+----------------------+
|   API Server         |
|----------------------|
| 1. Request Reception |
| 2. Authentication    |
| 3. Authorization     |
| 4. Admission Control |
| 5. Validation        |
| 6. Etcd Interaction  |
+----------------------+
    |
    V
Etcd (Cluster State)
```

---

# 🔐 1. Authentication

Authentication verifies **WHO** is making the request.

Supported authentication mechanisms:

* **Certificates** (X.509 client certificates)
* **Bearer Tokens** (Static token files, ServiceAccount tokens)
* **Bootstrap Tokens**
* **OIDC (OpenID Connect)** — for integrating with cloud IAM
* **Webhook Token Authentication**

If the user is not authenticated → request is rejected.

---

# 🛂 2. Authorization

Authorization verifies **WHAT actions** the authenticated user can perform.

Supported authorization modes:

### **RBAC (Role-Based Access Control)** → Most widely used

* Uses Roles & RoleBindings
* Example: A user can read pods but not delete them.

### **ABAC (Attribute-Based Access Control)**

* JSON policy-based access control.

### **Node Authorization**

* Only for kubelets.

### **Webhook Authorization**

* External custom authorization.

If user is not authorized → **403 Forbidden**.

---

# 🧩 3. Admission Control

Admission Controllers are plugins that run **after authentication & authorization**, but **before the API request is stored in etcd**.

They can **modify** or **reject** the request.

Types:

### **Mutating Admission Controllers**

Modify the request.

* Example: Add default values, inject sidecars.

### **Validating Admission Controllers**

Validate the request.

* Example: Ensure resource quotas, limit ranges.

Examples of admission plugins:

* `NamespaceLifecycle`
* `LimitRanger`
* `ResourceQuota`
* `DefaultStorageClass`
* `MutatingAdmissionWebhook`
* `ValidatingAdmissionWebhook`

---

# 🧵 4. Internal Components of API Server

### **a) API Handler

**Receives HTTP requests and routes them.

### **b) API Aggregation Layer

**Used to extend Kubernetes API using CRDs.

### **c) Storage Layer

**Converts API objects → etcd storage format.

### **d) Watch Mechanism

**Allows controllers and kubelets to watch API changes.

---

# 🌐 Protocols Used by Kubernetes API Server

## **🔸 External Communication (Clients → API Server)**

**HTTPS (HTTP/2)** is used for all external communication.

* kubectl → API server
* kubelet → API server
* dashboard → API server

Uses TLS certificates for encryption.

---

## **🔸 Internal Communication (API Server → Etcd)**

**gRPC/HTTP2 over TLS**

* Etcd communicates using **gRPC** protocol
* Secured using mutual TLS certificates

---




