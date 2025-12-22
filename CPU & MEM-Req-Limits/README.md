# Kubernetes CPU & Memory — Requests, Limits, OOMKill & Production Best Practices
---
# 🧠 1. What is CPU in Kubernetes?

* CPU is measured in **millicores**.
* `1000m = 1 CPU core`.
* Example:

  * `500m` = half CPU core
  * `250m` = quarter core

CPU is **compressible**:

* A pod can use more CPU than requested **if available**.
* CPU overuse does **NOT kill the pod**.

---

# 🧠 2. What is Memory in Kubernetes?

* Memory is measured in **bytes**, usually Gi or Mi.
* Example:

  * `512Mi`
  * `2Gi`

Memory is **non-compressible**:

* If a pod tries to use more memory than its **limit**, it gets **OOMKilled**.

---

# ⚙️ 3. What are Requests?

Requests = **minimum guaranteed resources** for a pod.

```
requests:
  cpu: "200m"
  memory: "256Mi"
```

Meaning:

* Scheduler will only place this pod on a node that can guarantee **200m CPU + 256Mi RAM**.
* Pod **may use more** than request but **not less**.

---

# 🔐 4. What are Limits?

Limits = **maximum resources a pod is allowed to use**.

```
limits:
  cpu: "500m"
  memory: "512Mi"
```

Meaning:

* Pod cannot use more than **500m CPU**.
* Pod cannot use more than **512Mi Memory**.

---

# 💥 5. What is OOM Kill?

OOM = "Out Of Memory".

If a pod tries to use **more memory than limit**, Kubernetes will:

* Kill the container instantly
* Restart it
* Mark event: `OOMKilled`

**Memory limit breach = pod crash.**

### Example:

Pod has:

```
memory:
  request: 256Mi
  limit:   512Mi
```

App tries to use 600Mi → **OOM Kill happens**.

### Important:

CPU limit breach = throttling, NOT crash
Memory limit breach = crash

---

# 📝 6. Full Example of Requests & Limits

```
resources:
  requests:
    cpu: "300m"
    memory: "256Mi"
  limits:
    cpu: "1000m"
    memory: "512Mi"
```

### Meaning:

* Guaranteed 300m CPU, can burst to 1 CPU.
* Guaranteed 256Mi RAM, max allowed 512Mi.

---

# 🧪 7. How the Scheduler Uses Requests

* Scheduler only cares about **requests**, not limits.
* It finds a node where **requested resources** are available.

Limits only come into effect **after** the pod is running.

---

# 🚨 8. What Happens When Limits Are Too Low?

* Frequent OOMKills
* Application crashes under load
* API latency increases
* Pods restarting repeatedly

---

# 🚨 9. What Happens When Requests Are Too High?

* Pod may never get scheduled
* Wastes cluster capacity
* Autoscaling behaves poorly

---

# 🏭 10. Production Best Practices

## ✔ 1. Always set both **requests & limits**

Avoid running pods without resource constraints.

---

## ✔ 2. Never set memory limit too close to real usage

Apps often spike.

Example:

* Normal usage: 300Mi
* Limit: 600Mi (not 350Mi)

---

## ✔ 3. Avoid overly strict CPU limits

High CPU limits cause throttling.

If app needs CPU bursts → keep CPU limit = request

```
requests:
  cpu: 500m
limits:
  cpu: 500m   # this avoids throttling
```

---

## ✔ 4. Use Vertical Pod Autoscaler (VPA) for auto-tuning

VPA can suggest or auto-set resource values.

---

## ✔ 5. Use HPA with CPU/Memory/Custom Metrics

HPA uses CPU or memory to scale replicas.

---

## ✔ 6. Monitor OOMKill events in production

Use:

* Prometheus
* Grafana
* Loki logs
* Kubernetes events

---

## ✔ 7. Add buffer for memory usage

General rule:

```
Limit = 2 × Request
```

---

## ✔ 8. Stress test your application before production

Use:

* K6
* Locust
* JMeter

Identify memory spikes.

---

## ✔ 9. Use PodPriority to protect critical workloads

Ensures critical pods always get resources.

---

## ✔ 10. Don’t oversubscribe memory

CPU oversubscription is fine.

Memory oversubscription = guaranteed crashes.

---

# 📊 11. Quick Summary Table

| Concept         | CPU            | Memory          |
| --------------- | -------------- | --------------- |
| Compressible    | Yes            | No              |
| Exceeding Limit | Throttling     | OOMKill         |
| Scheduler uses  | Requests       | Requests        |
| Good practice   | Same req/limit | Limit=2×Request |

---


