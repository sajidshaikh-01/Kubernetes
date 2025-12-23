# Kubernetes RBAC — Explained Simply with Production Usage & Best Practices
---
## 1️⃣ What is RBAC?

RBAC stands for **Role-Based Access Control**.

In simple words:

> **RBAC decides WHO can do WHAT on WHICH Kubernetes resources.**

Without RBAC:

* Anyone with kubeconfig can do anything ❌

With RBAC:

* Permissions are controlled and safe ✅

---

## 2️⃣ Why RBAC is Critical in Production

In production you have:

* Multiple teams
* CI/CD systems
* Monitoring tools
* Different environments

RBAC ensures:

* No accidental deletes
* Least privilege access
* Compliance & security

---

## 3️⃣ Core RBAC Components (Big Picture)

```
User / ServiceAccount
        ↓
     Role / ClusterRole   (WHAT actions?)
        ↓
   RoleBinding / ClusterRoleBinding (WHO gets it?)
```

---

## 4️⃣ What is a Role?

A **Role** defines **permissions inside ONE namespace**.

> Role = "What actions are allowed in THIS namespace only"

### Example:

```yaml
kind: Role
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
```

### Meaning:

* Can read pods
* Only in ONE namespace

### Used for:

* Developers
* Team-level access

---

## 5️⃣ What is a ClusterRole?

A **ClusterRole** defines permissions **across the entire cluster**.

> ClusterRole = "What actions are allowed CLUSTER-WIDE"

### Example:

```yaml
kind: ClusterRole
rules:
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["get", "list"]
```

### Used for:

* Platform team
* Monitoring tools
* Cluster admins

---

## 6️⃣ What is a RoleBinding?

A **RoleBinding** attaches a **Role to a user/service account** in ONE namespace.

> RoleBinding = "WHO gets this Role in this namespace"

### Example:

```yaml
kind: RoleBinding
subjects:
- kind: User
  name: dev-user
roleRef:
  kind: Role
  name: pod-reader
```

---

## 7️⃣ What is a ClusterRoleBinding?

A **ClusterRoleBinding** attaches a **ClusterRole to a user/service account** across the cluster.

> ClusterRoleBinding = "WHO gets this ClusterRole everywhere"

### Example:

```yaml
kind: ClusterRoleBinding
subjects:
- kind: ServiceAccount
  name: prometheus
roleRef:
  kind: ClusterRole
  name: monitoring-role
```

---

## 8️⃣ Simple Comparison Table

| Component          | Scope     | Purpose                   |
| ------------------ | --------- | ------------------------- |
| Role               | Namespace | Define permissions        |
| RoleBinding        | Namespace | Assign Role               |
| ClusterRole        | Cluster   | Define global permissions |
| ClusterRoleBinding | Cluster   | Assign ClusterRole        |

---

## 9️⃣ Real Production Example

### Scenario:

* Dev team should manage pods only in `payments-dev`
* Monitoring tool needs access to all namespaces

### Solution:

* Role + RoleBinding for dev team
* ClusterRole + ClusterRoleBinding for monitoring

---

## 🔐 10️⃣ RBAC for Service Accounts (Very Common)

Pods authenticate as **ServiceAccounts**.

RBAC controls what those pods can do:

* CI/CD tools
* Operators
* Controllers

Never give pods `cluster-admin` ❌

---

## 11️⃣ Production Best Practices (VERY IMPORTANT)

### ✅ 1. Follow Least Privilege

Give minimum permissions required.

---

### ✅ 2. Prefer Role over ClusterRole

Use namespace-scoped access wherever possible.

---

### ✅ 3. Avoid ClusterRoleBinding unless absolutely needed

Cluster-wide access is dangerous.

---

### ✅ 4. Separate RBAC for humans and applications

* Humans → OIDC users
* Apps → ServiceAccounts

---

### ✅ 5. Never use cluster-admin for CI/CD

Create limited roles instead.

---

### ✅ 6. Use Groups with OIDC

Assign roles to groups instead of individuals.

---

### ✅ 7. Audit RBAC permissions regularly

Remove unused bindings.

---

### ✅ 8. Enable Audit Logging

Track who did what and when.

---

### ✅ 9. Use separate namespaces per team

Simplifies RBAC design.

---

### ✅ 10. Combine RBAC with Admission Policies

RBAC controls **who**, Kyverno/OPA controls **how**.

---

## 12️⃣ Common Production Mistakes

❌ Using cluster-admin everywhere
❌ Sharing kubeconfig files
❌ No RBAC for service accounts
❌ Forgetting to remove old users



 RBAC for CI/CD pipelines
