# kubeconfig File — What It Is, What It Contains, and How It’s Used in Production
---

## 1️⃣ What is a kubeconfig file?

A **kubeconfig** file is a **configuration file** used by `kubectl` and other Kubernetes clients to:

> **Authenticate to a Kubernetes cluster and talk to its API server**

In simple words:

> kubeconfig tells kubectl **WHERE** the cluster is and **WHO YOU ARE**.

Default location:

```
~/.kube/config
```

---

## 2️⃣ Why kubeconfig exists

Kubernetes supports:

* Multiple clusters (dev, stage, prod)
* Multiple users
* Multiple authentication methods

kubeconfig allows you to:

* Switch clusters
* Switch users
* Work safely with different environments

---

## 3️⃣ What is stored inside kubeconfig?

A kubeconfig file is a **YAML file** that contains **four main sections**:

```
clusters
users
auth-contexts (contexts)
current-context
```

---

## 4️⃣ Clusters section (WHERE is the cluster?)

Stores information about the Kubernetes cluster.

### Example:

```yaml
clusters:
- name: prod-cluster
  cluster:
    server: https://10.0.0.1:6443
    certificate-authority-data: <base64>
```

### Contains:

* API server endpoint
* CA certificate to verify the server

---

## 5️⃣ Users section (WHO are you?)

Stores authentication information.

### Example:

```yaml
users:
- name: dev-user
  user:
    token: eyJhbGciOi...
```

Auth methods stored here:

* Token
* Client certificate & key
* Exec plugins (IAM, OIDC)

---

## 6️⃣ Contexts section (WHICH cluster + WHICH user?)

A **context** binds:

* One cluster
* One user
* One namespace (optional)

### Example:

```yaml
contexts:
- name: prod-context
  context:
    cluster: prod-cluster
    user: dev-user
    namespace: payments-prod
```

Context = Active working environment.

---

## 7️⃣ current-context

Defines which context is **currently active**.

```yaml
current-context: prod-context
```

This is why you can accidentally run commands on prod 😄

---

## 8️⃣ Full kubeconfig Example (Simplified)

```yaml
apiVersion: v1
kind: Config
clusters:
- name: prod-cluster
  cluster:
    server: https://10.0.0.1:6443
    certificate-authority-data: LS0t...

users:
- name: dev-user
  user:
    token: eyJhbGc...

contexts:
- name: prod-context
  context:
    cluster: prod-cluster
    user: dev-user
    namespace: default

current-context: prod-context
```

---

## 9️⃣ How kubeconfig is used (Flow)

```
kubectl command
   ↓
Read kubeconfig
   ↓
Authenticate user
   ↓
Authorize via RBAC
   ↓
Send request to API server
```

---

## 🔐 10️⃣ Security Risks (VERY IMPORTANT)

A kubeconfig file may contain:

* Tokens
* Certificates

If leaked → **full cluster access** possible.

---

## 🏭 11️⃣ Production Best Practices

### ✅ 1. Never share kubeconfig files

Each user must have their own.

---

### ✅ 2. Use OIDC or IAM instead of static tokens

Avoid long-lived tokens.

---

### ✅ 3. Restrict access using RBAC

kubeconfig + RBAC = safe access.

---

### ✅ 4. Use separate kubeconfig per environment

```
config-dev
config-stage
config-prod
```

---

### ✅ 5. Always verify current context

Before running commands:

```
kubectl config current-context
```

---

### ✅ 6. Use read-only access where possible

For monitoring and audits.

---

### ✅ 7. Rotate credentials regularly

Tokens and certificates must expire.

---

### ✅ 8. Protect kubeconfig file permissions

```
chmod 600 ~/.kube/config
```

---

### ✅ 9. Use kubectl exec plugins (cloud IAM)

No secrets stored locally.

---

## 12️⃣ Common Production Mistakes

❌ Using admin kubeconfig everywhere
❌ Committing kubeconfig to Git
❌ Same kubeconfig for CI/CD and humans

---

