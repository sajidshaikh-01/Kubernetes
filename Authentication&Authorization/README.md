# Authentication & Authorization in Kubernetes and Modern Production Systems
---

## 1️⃣ Authentication vs Authorization (Very Simple)

### 🔐 Authentication (AuthN)

> **Who are you?**

Authentication verifies **identity**.
Examples:

* Are you a user or service?
* Are you really `admin`?

---

### 🔑 Authorization (AuthZ)

> **What are you allowed to do?**

Authorization decides **permissions**.
Examples:

* Can you create pods?
* Can you delete namespaces?

---

### 🔥 One-line difference

```
Authentication = Identity check
Authorization  = Permission check
```

---

## 2️⃣ Authentication in Production (Technologies Used)

### 🟢 1. X.509 Certificates (Kubernetes default)

Used for:

* kube-apiserver
* kubelet
* controller-manager

✔ Strong security
✔ Used internally by Kubernetes

---

### 🟢 2. Service Accounts (Most common in-cluster AuthN)

Used by:

* Pods talking to Kubernetes API
* CI/CD tools

```
ServiceAccount → JWT token → API Server
```

✔ Every pod uses a service account
✔ Token mounted automatically

---

### 🟢 3. OIDC (Most common for users)

Used for:

* Developers
* Platform engineers

Examples:

* Google
* Azure AD
* Okta
* Keycloak

Flow:

```
User → OIDC Provider → Token → Kubernetes API
```

✔ Industry standard
✔ Centralized identity

---

### 🟢 4. IAM Integration (Cloud Managed Kubernetes)

Used in:

* AWS EKS (IAM)
* Azure AKS (AAD)
* GKE (GCP IAM)

✔ No static credentials
✔ Cloud-native

---

## 3️⃣ Authorization in Production (Technologies Used)

### 🟠 1. RBAC (Role-Based Access Control) — MOST USED

Defines:

* Who can do what
* In which namespace

Objects:

* Role / ClusterRole
* RoleBinding / ClusterRoleBinding

Example:

```
User X → can read pods in namespace Y
```

---

### 🟠 2. ABAC (Attribute-Based Access Control)

* Uses attributes (user, group, request)
* Rarely used now

❌ Hard to manage
❌ Not recommended

---

### 🟠 3. Webhook Authorization

* External system decides access
* Used with custom security engines

---

### 🟠 4. Policy Engines (OPA / Kyverno)

Used for:

* Advanced authorization rules
* Compliance

Examples:

* Only approved images allowed
* Mandatory labels

---

## 4️⃣ Real Production Architecture (Kubernetes)

```
User / Service
   ↓ (Authentication)
OIDC / IAM / ServiceAccount
   ↓
Kubernetes API Server
   ↓ (Authorization)
RBAC / OPA / Kyverno
   ↓
Action Allowed or Denied
```

---

## 5️⃣ Authentication & Authorization Outside Kubernetes

### 🔹 API Authentication

* OAuth2
* JWT
* mTLS
* API Keys

### 🔹 API Authorization

* Scopes (read, write)
* Roles (admin, user)
* Policies

---

## 6️⃣ Production Best Practices (VERY IMPORTANT)

### ✅ 1. Always separate AuthN and AuthZ

Do not mix identity with permissions.

---

### ✅ 2. Use OIDC for humans, ServiceAccounts for apps

* Humans → OIDC
* Apps → Service Accounts

---

### ✅ 3. Follow Principle of Least Privilege

Give **minimum required access**.

---

### ✅ 4. Never use cluster-admin casually

Restrict cluster-admin to very few users.

---

### ✅ 5. Use Namespace-scoped Roles wherever possible

Avoid ClusterRole unless necessary.

---

### ✅ 6. Rotate credentials regularly

* Service account tokens
* OIDC tokens

---

### ✅ 7. Enable audit logs

Track:

* Who accessed what
* Who deleted resources

---

### ✅ 8. Use OPA/Kyverno for policy enforcement

RBAC controls *who*, policies control *how*.

---

### ✅ 9. Disable anonymous access

Anonymous access = security risk.

---

### ✅ 10. Use mTLS for service-to-service auth

Especially in zero-trust environments.

---

## 7️⃣ Common Production Mistakes

❌ Using cluster-admin for CI/CD
❌ Sharing kubeconfig files
❌ Long-lived static tokens
❌ No audit logging

---


