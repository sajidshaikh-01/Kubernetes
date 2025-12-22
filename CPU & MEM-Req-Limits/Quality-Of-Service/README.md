# Kubernetes Quality of Service (QoS) Classes — Complete README

Kubernetes assigns each Pod a **Quality of Service (QoS) Class** based on how you set **CPU & Memory Requests and Limits**.
This helps the kubelet decide **which pods are killed first** when a node runs out of memory.

There are **3 QoS Classes**:

1. **Guaranteed**
2. **Burstable**
3. **BestEffort**

---

# 🟩 1. Guaranteed QoS Class

A pod is **Guaranteed** ONLY IF:

* Every container has BOTH **requests & limits defined**, AND
* **CPU request = CPU limit** AND **Memory request = Memory limit**

### Example:

```yaml
resources:
  requests:
    cpu: "500m"
    memory: "512Mi"
  limits:
    cpu: "500m"
    memory: "512Mi"
```

### Meaning:

* Pod gets strong CPU & memory guarantees.
* Pod is **least likely to be evicted**.

### Used in production for:

* Databases
* StatefulSets
* Critical services
* Cash registers, auth service, payment service

### Pros:

* Predictable performance
* Strongest protection from eviction

---

# 🟧 2. Burstable QoS Class

A pod is **Burstable** when:

* It has **requests**, but **limits are higher** OR
* Some containers have limits but not all

### Example:

```yaml
resources:
  requests:
    cpu: "200m"
    memory: "256Mi"
  limits:
    cpu: "800m"
    memory: "1Gi"
```

### Meaning:

* Pod is guaranteed 200m CPU & 256Mi RAM
* It can burst up to its limits if available

### Used for:

* Majority of microservices
* Web backends
* Internal APIs

### Pros:

* Flexible
* Good balance between performance & efficiency

### Cons:

* Can be throttled
* Can be evicted before Guaranteed pods

---

# 🟥 3. BestEffort QoS Class

A pod is **BestEffort** when:

* **NO requests or limits** are set for ANY container

### Example:

```yaml
resources: {}
```

### Meaning:

* Pod has *no guaranteed resources*
* Uses whatever is leftover

### Used for:

* Debug pods
* Non-critical services
* Background jobs

### Cons:

* First to be evicted
* Most unstable
* Not recommended for production workload

---

# 🔥 Eviction Priority (Most Important)

When a node runs out of memory, Kubernetes kills pods in this order:

### 1️⃣ **BestEffort** (killed first)

### 2️⃣ **Burstable** (medium priority)

### 3️⃣ **Guaranteed** (killed last)

This is why setting **requests/limits carefully** is critical.

---

# 🧠 How QoS Is Calculated (Big Picture)

```
If no requests/limits → BestEffort
If requests < limits → Burstable
If requests == limits → Guaranteed
```

---

# 🏭 Production Best Practices

### ✔ 1. Critical services → Guaranteed

Examples:

* Payment
* Auth
* Database
* Message brokers

### ✔ 2. Normal microservices → Burstable

* Most apps fall here
* Good balance of cost & performance

### ✔ 3. Never deploy BestEffort in prod

Unless it’s:

* Debug
* One-time tools
* Utility pods

### ✔ 4. Use LimitRange to force minimum resource requests

Prevents teams from accidentally creating BestEffort pods.

### ✔ 5. Monitor OOMKills and evictions

Guaranteed pods usually survive.

### ✔ 6. Review QoS class during code deployments

Make sure pods didn’t slip into BestEffort accidentally.

### ✔ 7. Test with load tools

Ensure pod does not OOMKill under stress.






