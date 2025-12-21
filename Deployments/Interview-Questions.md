# Kubernetes Deployments & Deployment Strategies – Scenario-Based Interview Q&A
---
# 1. What is a Deployment in Kubernetes?

**Answer:**
A Deployment is a controller that manages stateless applications. It ensures the desired number of pods run continuously, supports rolling updates, rollbacks, scaling, and provides self-healing.

**Key points for interview:**

* Manages ReplicaSets
* Ensures high availability
* Handles version updates
* Autoscaling supported

---

# 2. Scenario: A pod inside a Deployment crashes. What happens?

**Answer:**

* Kubelet tries to restart the container inside the pod.
* If the pod becomes unhealthy or deleted, ReplicaSet creates a new pod.
* Deployment ensures replica count stays the same.

**Why?**
Because pods are ephemeral, but Deployment + ReplicaSet gives self‑healing.

---

# 3. What is the difference between Deployment and ReplicaSet?

**Answer:**

* ReplicaSet maintains pod replicas.
* Deployment manages ReplicaSets and provides rolling updates & rollbacks.

**Simple explanation:**
Deployment = High-level controller
ReplicaSet = Low-level controller

---

# 4. Scenario: Your deployment update failed and new pods are crashing. What do you do?

**Answer:**
Rollback:

```
kubectl rollout undo deployment/myapp
```

Check history:

```
kubectl rollout history deployment/myapp
```

Identify root cause using:

* `kubectl describe pod`
* `kubectl logs`

---

# 5. Explain Rolling Update Strategy.

**Answer:**
Rolling update replaces old pods with new pods gradually, ensuring zero downtime.

Controls:

* maxSurge
* maxUnavailable

Example:

```
maxSurge: 1
maxUnavailable: 0
```

---

# 6. Scenario: You need zero downtime deployment. Which strategy do you choose?

**Answer:**

* Blue/Green
* Canary
* Rolling updates

### Best for critical systems: Blue/Green

### Best for microservices: Canary

### Best for simple apps: Rolling update

---

# 7. Explain Blue/Green deployment.

**Answer:**
Two environments run side-by-side:

* Blue = current production
* Green = new version

Traffic switches only when Green is fully tested.

**Advantages:** Safe rollbacks, zero downtime.

**Disadvantage:** Double resource usage.

---

# 8. Scenario: You deployed a new version using Blue/Green and users report issues. What do you do?

**Answer:**
Switch traffic back to Blue immediately.

Rollback is instant because Blue environment is still running.

---

# 9. Explain Canary deployment.

**Answer:**
A small percentage of traffic is routed to the new version (v2).
If stable → increase traffic gradually until 100%.

**Advantages:** Low-risk, real-traffic testing.

**Tools:**

* Argo Rollouts
* Istio
* NGINX Ingress

---

# 10. Scenario: Your canary deployment is receiving errors at 10% traffic. What should you do?

**Answer:**

* Stop the rollout
* Route all traffic back to stable (v1)
* Analyze logs, metrics
* Fix & retry rollout

---

# 11. What is Recreate deployment strategy?

**Answer:**
Stops all old pods → starts new pods.

**Downside:** Causes downtime.
**Use Case:** When apps cannot run two versions together.

---

# 12. Scenario: Your application cannot run two versions simultaneously. Which strategy will you use?

**Answer:**
Recreate strategy.

Example: Database schema incompatible between versions.

---

# 13. How do you roll out a new image version?

```
kubectl set image deployment/myapp myapp=app:v2
```

---

# 14. How do you monitor deployment progress?

```
kubectl rollout status deployment/myapp
```

---

# 15. Scenario: Deployment is stuck in "Progressing" state. What do you check?

**Check:**

* `kubectl describe deployment`
* Image pull errors
* Liveness/readiness probes
* Resource limits causing OOMKilled
* Node capacity
* ReplicaSet events

---

# 16. What is `maxSurge` and `maxUnavailable`?

**maxSurge:** How many extra pods can be added during update.
**maxUnavailable:** How many pods can be taken down during update.

Example:

```
maxSurge: 1
maxUnavailable: 0
```

Means:

* Never reduce pod count
* Add one extra pod during rollout

---

# 17. Scenario: You want to ensure absolutely no downtime. Which values do you choose?

```
maxSurge: 1
maxUnavailable: 0
```

This ensures full capacity at all times.

---

# 18. Scenario: Deployment created duplicate pods during rollout. Why?

Possible reasons:

* maxSurge is high
* Rolling update creates extra pods temporarily
* Autoscaler may have scaled replicas before rollout

---

# 19. How do you pause and resume a rollout?

```
kubectl rollout pause deployment/myapp
kubectl rollout resume deployment/myapp
```

Useful during debugging or manual control in canary releases.

---

# 20. Scenario: Traffic should be routed 80% to v1 and 20% to v2. Which strategy?

**Answer:** Canary deployment.

Tools:

* Istio VirtualService
* Argo Rollouts traffic-split

---

# 21. Explain A/B Testing deployment.

**Answer:**
Direct a portion of traffic to different versions based on:

* Location
* Device type
* Cookies
* Headers

Used for UI/UX experiments.

---

# 22. Scenario: You want to test feature only for US users. Which deployment strategy?

**Answer:** A/B Testing.

---

# 23. Explain Shadow deployment.

**Answer:**
New version receives a **copy of production traffic**, but does not impact users.

Used for:

* ML model testing
* Backend refactoring

---

# 24. Scenario: You want to test your ML model without affecting real users. Which strategy?

**Answer:** Shadow deployment.

---

# 25. What causes a rolling update to fail?

Common causes:

* Image pull error
* CrashLoopBackoff
* Readiness probe failing
* Insufficient node resources
* Missing configMap/secret
* Wrong environment variables

---

# 26. Scenario: Readiness probe failing during rollout. What happens?

**Answer:**

* Pod is NOT added to Service
* Rolling update waits (does not continue)
* Deployment stays in "Progressing"

Fix the application or probe.

---

# 27. How do you see events inside a Deployment?

```
kubectl describe deployment myapp
```

---

# 28. Scenario: You want to run 3 versions of your app at same time for testing. What strategy?

**Answer:**

* A/B Testing
* Shadow testing

---

# 29. How do Deployments ensure High Availability?

* Running multiple replicas
* Spreading pods across nodes
* Restarting failed pods
* Load balancing through Service

---

# 30. Scenario: Node goes down. What happens to Pods in Deployment?

ReplicaSet replaces missing pods on available nodes.
Service continues routing only to healthy pods.

---

