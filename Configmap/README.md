# Kubernetes ConfigMap — What It Is, Why We Use It, and How It’s Used in Production

---

## 1️⃣ What is a ConfigMap?

A **ConfigMap** is a Kubernetes object used to **store non-sensitive configuration data**.

In simple words:

> **ConfigMap = external configuration for your application**

Examples of configuration:

* Application properties
* Environment variables
* Feature flags
* URLs
* Log levels

---

## 2️⃣ Why ConfigMap Exists (Real Problem)

Without ConfigMap:

* Config is hardcoded inside Docker image ❌
* Any config change needs:

  * Code change
  * Image rebuild
  * Redeploy

With ConfigMap:

* Change config **without rebuilding image** ✅
* Same image works in **dev / stage / prod** ✅

---

## 3️⃣ What Should Go in ConfigMap?

✔ Allowed (Non-sensitive):

* Application ports
* Database host (NOT password)
* Log level
* Feature flags
* API endpoints

❌ Not Allowed (Sensitive data):

* Passwords
* Tokens
* API keys

Sensitive data should go to **Secrets**, not ConfigMaps.

---

## 4️⃣ How ConfigMap is Used (High-Level Flow)

```
ConfigMap
   ↓
Mounted into Pod
   ↓
Application reads config
```

ConfigMap is **read by Pods**, not directly by users.

---

## 5️⃣ Ways to Use ConfigMap

There are **two main ways** to use ConfigMap:

---

## 🟢 Method 1: ConfigMap as Environment Variables (MOST COMMON)

### Example ConfigMap

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_ENV: "prod"
  LOG_LEVEL: "INFO"
```

### Use in Pod

```yaml
envFrom:
- configMapRef:
    name: app-config
```

✔ Simple
✔ Clean
✔ Common in microservices

---

## 🟢 Method 2: ConfigMap as Files (Config Files)

### Example ConfigMap

```yaml
data:
  application.properties: |
    server.port=8080
    logging.level=INFO
```

### Mount as volume

```yaml
volumeMounts:
- mountPath: /config
  name: config

volumes:
- name: config
  configMap:
    name: app-config
```

✔ Used for:

* Nginx configs
* Spring Boot configs
* YAML/JSON files

---

## 6️⃣ Where ConfigMaps Are Used in Production

### ✔ Application Configuration

* Environment-based settings

---

### ✔ Platform Tools

* Nginx config
* Prometheus config
* Fluentd config

---

### ✔ Feature Flags

* Enable/disable features without redeploy

---

### ✔ CI/CD & Jobs

* Runtime parameters

---

## 7️⃣ Real Production Example

### Scenario:

Same Docker image runs in different environments.

### Solution:

```
values-dev → dev ConfigMap
values-prod → prod ConfigMap
```

Application reads config dynamically.

---

## 8️⃣ ConfigMap vs Secret (IMPORTANT)

| Feature        | ConfigMap  | Secret            |
| -------------- | ---------- | ----------------- |
| Sensitive data | ❌ No       | ✅ Yes             |
| Base64 encoded | ❌ No       | ✅ Yes             |
| Use case       | App config | Passwords, tokens |

---

## 9️⃣ Production Best Practices

### ✅ 1. Never store secrets in ConfigMaps

Use Kubernetes Secrets or external secret managers.

---

### ✅ 2. One ConfigMap per application

Avoid giant shared ConfigMaps.

---

### ✅ 3. Use immutable ConfigMaps for safety

Prevents accidental changes.

```yaml
immutable: true
```

---

### ✅ 4. Version your ConfigMaps

Use labels or names:

```
app-config-v1
app-config-v2
```

---

### ✅ 5. Restart pods after config change

ConfigMap update does NOT auto-restart pods.

---

### ✅ 6. Use Helm for ConfigMap management

Helm templates + values.yaml work well.

---

### ✅ 7. Separate config per environment

Never reuse prod ConfigMap in dev.

---

## 🔟 Common Production Mistakes

❌ Storing passwords in ConfigMaps
❌ Editing ConfigMaps manually in prod
❌ One ConfigMap shared by many apps
❌ Assuming pod auto-reloads config

---


