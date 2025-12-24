# Sealed Secrets — Architecture, Use Cases, and Production Best Practices
---
## 1️⃣ What is Sealed Secrets?

**Sealed Secrets** is a Kubernetes solution that allows you to **safely store encrypted secrets in Git**.

In simple words:

> **Sealed Secret = Encrypted Kubernetes Secret that is safe to commit to Git**

It is created and managed by the **Sealed Secrets Controller** (originally from Bitnami).

---

## 2️⃣ Why Sealed Secrets Exist (Real Problem)

### ❌ Problems with normal Kubernetes Secrets

* Secrets are only **Base64 encoded**, not encrypted
* Unsafe to commit to Git
* Risk of accidental leaks

### ✅ What Sealed Secrets solve

* Secrets are **encrypted using public-key cryptography**
* Encrypted secrets can be stored in Git
* Only the target cluster can decrypt them

This makes Sealed Secrets perfect for **GitOps workflows**.

---

## 3️⃣ High-Level Architecture

```
Plain Secret
   ↓ (kubeseal)
SealedSecret (encrypted)
   ↓ (stored in Git)
Git Repository
   ↓
Sealed Secrets Controller (in cluster)
   ↓
Kubernetes Secret (decrypted)
   ↓
Pod consumes Secret
```

---

## 4️⃣ Components of Sealed Secrets

### 🟢 1. kubeseal CLI

* Runs on developer / CI machine
* Encrypts Kubernetes Secrets
* Uses cluster’s **public key**

---

### 🟢 2. Sealed Secrets Controller

* Runs inside Kubernetes cluster
* Holds **private key**
* Decrypts SealedSecret objects
* Creates normal Kubernetes Secrets

---

### 🟢 3. SealedSecret CRD

* Custom Resource Definition
* Stores encrypted data
* Safe to commit to Git

---

## 5️⃣ How Sealed Secrets Work (Step-by-Step)

### Step 1️⃣ Create a normal Secret (locally)

```yaml
kind: Secret
metadata:
  name: db-secret
  namespace: prod
data:
  password: cGFzcw==
```

---

### Step 2️⃣ Encrypt it using kubeseal

```bash
kubeseal --format yaml < secret.yaml > sealed-secret.yaml
```

Now `sealed-secret.yaml` is encrypted.

---

### Step 3️⃣ Store SealedSecret in Git

Safe to commit ✅

---

### Step 4️⃣ Controller decrypts in cluster

* Controller reads SealedSecret
* Decrypts it
* Creates normal Kubernetes Secret

---

## 6️⃣ Where Sealed Secrets Are Used in Production

### ✔ GitOps environments

* Argo CD
* Flux

---

### ✔ Teams that want Git as single source of truth

* Infrastructure as Code
* App configs + secrets together

---

### ✔ Medium security environments

* Where Vault or cloud secret managers are not mandatory

---

## 7️⃣ What Sealed Secrets Are NOT

❌ Not a secret manager like Vault
❌ Not dynamic secret rotation
❌ Not external secret source

Sealed Secrets are for **secure storage in Git**, not secret lifecycle management.

---

## 8️⃣ Sealed Secrets vs Kubernetes Secrets vs External Secrets

| Feature         | K8s Secret | Sealed Secret | External Secrets |
| --------------- | ---------- | ------------- | ---------------- |
| Git safe        | ❌          | ✅             | ✅                |
| Encryption      | ❌ (Base64) | ✅ (PKI)       | ✅                |
| Rotation        | ❌          | ❌             | ✅                |
| External source | ❌          | ❌             | ✅                |

---

## 9️⃣ Production Best Practices (VERY IMPORTANT)

### ✅ 1. Use Sealed Secrets only with GitOps

Best fit for ArgoCD / Flux.

---

### ✅ 2. Backup the controller private key

If lost → secrets cannot be recovered.

---

### ✅ 3. Scope Sealed Secrets tightly

Encrypt secrets for:

* Specific namespace
* Specific name

---

### ✅ 4. Avoid using Sealed Secrets for frequently rotating secrets

Use Vault or cloud secret managers instead.

---

### ✅ 5. Protect Sealed Secrets Controller

Restrict access via RBAC.

---

### ✅ 6. Separate secrets per environment

```
sealed-secret-dev.yaml
sealed-secret-prod.yaml
```

---

### ✅ 7. Combine with RBAC and NetworkPolicies

Defense in depth.

---

## 🔟 Common Production Mistakes

❌ Not backing up encryption keys
❌ Using same SealedSecret across namespaces
❌ Assuming Sealed Secrets replace Vault
❌ Storing highly dynamic credentials

---

## 11️⃣ Interview-Ready Summary

```
Sealed Secrets encrypt Kubernetes Secrets
Safe to store in Git
Decrypted only inside cluster
Best for GitOps workflows
```

