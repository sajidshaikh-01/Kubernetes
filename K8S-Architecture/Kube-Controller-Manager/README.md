# Kubernetes Controller Manager

The **Kube Controller Manager** is a core control-plane component responsible for running multiple controllers that ensure the actual cluster state matches the **desired state** defined in Kubernetes objects.

It is essentially a **collection of control loops**, each watching the API server and making changes when needed.

---

# 🏗️ Architecture of Kube Controller Manager

```
                   +--------------------------+
                   |      API Server          |
                   | (Cluster State Storage)  |
                   +------------+-------------+
                                |
                     Watches Cluster State
                                |
                                V
                +---------------------------------+
                |   Kube Controller Manager       |
                |---------------------------------|
                |  Node Controller                |
                |  Replication Controller         |
                |  Deployment Controller          |
                |  ReplicaSet Controller          |
                |  StatefulSet Controller         |
                |  DaemonSet Controller           |
                |  Job / CronJob Controllers      |
                |  EndpointSlice Controller       |
                |  Service Account Controller     |
                |  PV/PVC Controllers             |
                |  Namespace Controller           |
                +------------------+--------------+
                                   |
                           Updates Cluster State
                                   |
                                   V
                            +--------------+
                            |   API Server |
                            +--------------+
```

---

# 🔁 How Controllers Work (Control Loop)

Each controller runs a continuous loop:

```
1. Watch API Server for changes
2. Compare Desired State vs Actual State
3. If mismatch → Take corrective action
```

Example:

* Deployment says **3 replicas**
* Controller sees only **1 running**
* It instructs scheduler to create **2 more pods**

This is the foundation of Kubernetes **self-healing**.

---

# 🧩 Types of Kubernetes Controllers

Below are the major controllers managed by the **kube-controller-manager**.

---

## 1. **Node Controller**

Responsible for monitoring node health.

* Detects node failures
* Marks node as `NotReady`
* Evicts pods from unhealthy nodes

---

## 2. **Replication Controller** (older)

Ensures a specified number of pod replicas are always running.
Mostly replaced by **ReplicaSet**.

---

## 3. **ReplicaSet Controller**

Ensures that the correct number of pod replicas are running at all times.
Used by Deployments.

---

## 4. **Deployment Controller**

Handles the lifecycle of Deployments:

* Rolling updates
* Rollbacks
* Scaling

---

## 5. **StatefulSet Controller**

Manages StatefulSets:

* Stable identities
* Ordered deployment
* Persistent storage

---

## 6. **DaemonSet Controller**

Ensures a pod is running on **every node** (or specific nodes).

Examples:

* Logging agents
* Monitoring agents (Prometheus node exporter)
* CNI plugins

---

## 7. **Job Controller**

Manages Jobs that run tasks until successful completion.

---

## 8. **CronJob Controller**

Creates Jobs on a scheduled time (like Linux cron).

---

## 9. **Service Account & Token Controllers**

Automatically creates:

* ServiceAccounts
* API tokens

Used for pod authentication.

---

## 10. **EndpointSlice Controller**

Maintains list of pods that back a service.

Improves scalability over older **Endpoints**.

---

## 11. **Persistent Volume (PV) Controller**

Manages lifecycle of PVs.

* Attach / Detach volumes
* Bind PV ↔ PVC

---

## 12. **Persistent Volume Claim (PVC) Controller**

Handles PVC creation and binding.

If `StorageClass` is present → dynamic provisioning.

---

## 13. **Namespace Controller**

When a namespace is deleted:

* Cleans all resources inside it

---

## 14. **Horizontal Pod Autoscaler (HPA) Controller**

Scales pods based on metrics (CPU, memory, custom metrics).

---

## 15. **Certificate Signing Request (CSR) Controller**

Approves or denies certificate requests.

Used for TLS in cluster components.

---

# 🧵 Summary Table

| Controller                | Purpose                   |
| ------------------------- | ------------------------- |
| Node Controller           | Node health, eviction     |
| Deployment Controller     | Rollouts, scaling         |
| ReplicaSet Controller     | Maintains replica count   |
| StatefulSet Controller    | Ordered & persistent pods |
| DaemonSet Controller      | Pod per node              |
| Job / CronJob             | Batch & scheduled jobs    |
| PV/PVC Controllers        | Storage management        |
| HPA Controller            | Autoscaling               |
| Namespace Controller      | Cleanup                   |
| Endpoints / EndpointSlice | Service backend tracking  |

