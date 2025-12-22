# Kubernetes ResourceQuota & LimitRange — How They Work in Production
---

# 🔹 1. What is ResourceQuota?

ResourceQuota sets **hard limits** on how many resources a **namespace** can use.

Think of it as the **total budget** for a namespace.

### Example ResourceQuota

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: prod-quota
  namespace: payments-prod
spec:
  hard:
    requests.cpu: "10"
    limits.cpu: "20"
    requests.memory: "20Gi"
    limits.memory: "40Gi"
    pods: "50"
    services: "20"
```

### What this means:

* Namespace **cannot exceed** 20 CPU in limits
* Cannot exceed 40Gi memory in limits
* Cannot create more than 50 pods
* Cannot create more than 20 services

If a deployment tries to go beyond these values → **Kubernetes rejects it**.

---

# 🔹 2. What is LimitRange?

LimitRange sets **default** and **minimum/maximum** CPU & memory for **each container**.

Think of it as a **rulebook** for new pods.

### Example LimitRange

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: default-limits
  namespace: payments-prod
spec:
  limits:
  - default:
      cpu: "500m"
      memory: "1Gi"
    defaultRequest:
      cpu: "200m"
      memory: "512Mi"
    min:
      cpu: "100m"
      memory: "256Mi"
    max:
      cpu: "2"
      memory: "4Gi"
    type: Container
```

### What this means:

* If developer **forgets** requests/limits → defaults apply automatically.
* A container **cannot request less** than 100m CPU.
* A container **cannot request more** than 2 CPU.

LimitRange protects the cluster from:

* Overloaded apps
* Misconfigured deployments
* No-limit pods

---

# 🔹 3. How ResourceQuota & LimitRange Work Together

They work like **Budget (Quota)** + **Rules (LimitRange)**.

### Example Flow

1. Developer deploys a new pod.
2. LimitRange checks:

   * Are requests/limits defined?
   * If not → assign defaults.
   * If too high/low → reject.
3. ResourceQuota checks:

   * Does this namespace have enough total resources left?
   * If yes → pod allowed
   * If no → reject

---

# 🔹 4. Real Production Example

Namespace: `payments-prod`

### ✳️ Step 1: Platform team sets ResourceQuota

```
requests.cpu: 16
limits.cpu:   32
requests.memory: 32Gi
limits.memory:   64Gi
pods: 100
```

### ✳️ Step 2: Platform team sets LimitRange

```
defaultRequest: 200m / 512Mi
defaultLimit:   500m / 1Gi
max: 2 CPU / 4Gi
min: 100m / 256Mi
```

### ✳️ Step 3: Dev team deploys services

If they forget to add requests/limits → LimitRange auto-fills them.

If they set huge values like:

```
limits:
  cpu: 10
  memory: 20Gi
```

ResourceQuota blocks the deployment.

This ensures:

* No team can accidentally break the cluster
* All apps get minimum guaranteed resources

---

# 🔹 5. Why Companies Use Both in Production

### ResourceQuota solves:

* Overconsumption by one team
* Fair distribution of cluster resources
* Budget allocation per namespace

### LimitRange solves:

* Badly configured pods
* Developers forgetting requests/limits
* Preventing huge resource spikes
* Ensuring consistent performance

---

# 🔹 6. Production Best Practices

## ✔ 1. Always combine ResourceQuota + LimitRange

This ensures both:

* Namespace-level budget control
* Pod-level resource control

## ✔ 2. Set Reasonable Defaults With LimitRange

Example defaults:

```
defaultRequest: 200m CPU / 256Mi Memory
defaultLimit:   500m CPU / 512Mi Memory
```

## ✔ 3. Set Hard Max Values

Prevent developers from requesting too much.

```
max: 2 CPU / 4Gi Memory
```

## ✔ 4. Use separate namespaces for each team

Easier to apply ResourceQuota per team.

## ✔ 5. Adjust quotas based on team workload

Payments team may need more CPU than internal tools.

## ✔ 6. Monitor quota usage with Prometheus

Alert when:

* Namespace reaches 80% quota usage

## ✔ 7. Review quotas quarterly

Teams grow → workloads grow

## ✔ 8. Never leave namespaces without quotas in shared clusters

Without quotas → a single bug can overload entire cluster.

## ✔ 9. Avoid too strict quotas

Strict quotas cause deployment failures.

## ✔ 10. Use ResourceQuota for DB-heavy namespaces

Example:

```
storage: 500Gi
persistentvolumeclaims: 10

