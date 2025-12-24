# Kubernetes Secrets — What They Are, Types, and How They’re Used in Production
---
## 1️⃣ What is a Secret?

A **Secret** is a Kubernetes object used to store **sensitive information**.

In simple words:

> **Secret = secure place to store passwords, tokens, and keys**

Examples of sensitive data:

* Database passwords
* API tokens
* OAuth secrets
* TLS certificates
* SSH keys

---

## 2️⃣ Why Secrets Exist (Real Problem)

Without Secrets:

* Passwords hardcoded in code ❌
* Secrets committed to Git ❌
* Anyone with access sees credentials ❌

With Secrets:

* Sensitive data separated from code ✅
* Access controlled via RBAC ✅
* Safer configuration management ✅

---

## 3️⃣ Important Truth About Kubernetes Secrets

⚠️ Kubernetes Secrets are:

* **Base64 encoded**, NOT encrypted by default
* Stored in etcd

👉 Base64 ≠ encryption

For real security in production:

* Enable etcd encryption
* Or use external secret managers

---

## 4️⃣ Types of Kubernetes Secrets

Kubernetes provides **built-in Secret types**.

---

## 🟢 1. Opaque Secret (Most Common)

Used for **generic key-value secrets**.

### Example:

```yaml
apiVersion: v1
kind: Secret
type: Opaque
data:
  DB_USER: YWRtaW4=
  DB_PASSWORD: cGFzc3dvcmQ=
```

Used for:

* App credentials
* API keys

---

## 🟢 2. kubernetes.io/dockerconfigjson

Used for **private container registry authentication**.

### Used when:

* Pulling images from private Docker registry

Example:

```yaml
type: kubernetes.io/dockerconfigjson
```

---

## 🟢 3. kubernetes.io/tls

Used to store **TLS certificates**.

### Used for:

* HTTPS
* Ingress TLS

Contains:

* tls.crt
* tls.key

---

## 🟢 4. kubernetes.io/service-account-token

Automatically created by Kubernetes.

Used by:

* Pods to authenticate to Kubernetes API

You usually **do not create this manually**.

---

## 🟢 5. Basic Auth Secret (Legacy)

Stores:

* username
* password

Rarely used now.

---

## 5️⃣ How Secrets Are Used in Pods

Secrets can be consumed in **two ways**:

---

### 🟠 Method 1: As Environment Variables

```yaml
envFrom:
- secretRef:
    name: db-secret
```

✔ Simple
✔ Common

---

### 🟠 Method 2: As Files (Mounted Volume)

```yaml
volumes:
- name: secret-vol
  secret:
    secretName: tls-secret
```

✔ Used for certificates and keys

---

## 6️⃣ Secrets vs ConfigMap

| Feature        | ConfigMap  | Secret |
| -------------- | ---------- | ------ |
| Sensitive data | ❌ No       | ✅ Yes  |
| Encoding       | Plain text | Base64 |
| Security       | Low        | Medium |

---

## 7️⃣ Secrets in Real Production (IMPORTANT)

### ✔ Used for:

* DB credentials
* API tokens
* TLS certs

### ❌ Not used for:

* Large secrets
* Highly sensitive long-term secrets (use Vault)

---

## 8️⃣ External Secret Management (Production Grade)

Most companies use **external secret managers**:

* AWS Secrets Manager
* AWS SSM Parameter Store
* HashiCorp Vault
* Azure Key Vault
* Google Secret Manager

Integrated via:

* External Secrets Operator
* CSI Secret Store

---

## 9️⃣ Production Best Practices (VERY IMPORTANT)

### ✅ 1. Never commit Secrets to Git

Use CI/CD or secret managers.

---

### ✅ 2. Enable encryption at rest for etcd

Protect secrets stored in cluster.

---

### ✅ 3. Use RBAC to restrict secret access

Only required pods/users can read.

---

### ✅ 4. Prefer external secret managers

For compliance and rotation.

---

### ✅ 5. Rotate secrets regularly

Avoid long-lived credentials.

---

### ✅ 6. Use Secrets as files for certificates

Avoid exposing via env vars when possible.

---

### ✅ 7. Separate secrets per namespace

Avoid sharing secrets across apps.

---

## 🔟 Common Production Mistakes

❌ Storing secrets in ConfigMaps
❌ Hardcoding passwords in images
❌ Giving cluster-admin access to secrets
❌ Not rotating secrets

---

