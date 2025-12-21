# Kubernetes: Static Pods, DaemonSets, StatefulSets.

## 1. What Are Static Pods?

Static Pods are **pods managed directly by the kubelet** on a node, **without** the involvement of the API Server.

### Key Points:

* Defined as **YAML files placed in a specific directory** on the node (usually `/etc/kubernetes/manifests/`).
* Kubelet **watches this directory** and automatically creates/updates the pod.
* They **do not** use controllers like Deployments or DaemonSets.
* Kubernetes API Server **shows them as mirror pods**, but API Server can't modify them.
* If you delete a static pod from API, it **reappears** because kubelet recreates it.

### When Used in Production?

* Used for **control plane components**:

  * kube-apiserver
  * kube-scheduler
  * kube-controller-manager
  * etcd

These must run even if API server is down. That’s why kubelet handles them.

---

## 2. What Are DaemonSets?

A **DaemonSet** ensures **one pod runs on every node** (or selected nodes) in the cluster.

### Key Points:

* Automatically runs pods on **all nodes**.
* When a new node joins the cluster → DaemonSet automatically runs pod on it.
* If a node is removed → Pod is removed.
* Typically used for node-level services.

### Common Use Cases in Production:

* **Logging agents** (Fluentd, Filebeat, Vector)
* **Monitoring agents** (Prometheus Node Exporter)
* **CNI plugins** (Calico, Weave, Cilium pods)
* **Storage drivers** (CSI Daemon pods)
* **Security agents** (Falco, Sysdig)

DaemonSets = "run everywhere"

---

## 3. What Are StatefulSets?

A **StatefulSet** manages **stateful applications** that require:

* Stable & persistent identity
* Stable network names
* Ordered deployment/termination
* Persistent storage per pod

### Key Features:

* Pods have fixed names: `pod-0`, `pod-1`, `pod-2`...
* Uses **PersistentVolumeClaims** automatically.
* Ensures **ordered startup & shutdown**.
* Great for clustered databases.

### Common Use Cases in Production:

* Databases (MySQL, PostgreSQL)
* Distributed systems (Kafka, Zookeeper, MongoDB)
* Caching systems (Redis cluster, Cassandra)
* Any app requiring persistent volume per pod

StatefulSets = "run apps that need identity + storage"

---

## 4. Difference Between DaemonSets & StatefulSets

| Feature          | DaemonSet                   | StatefulSet                                  |
| ---------------- | --------------------------- | -------------------------------------------- |
| Purpose          | Run pod on every node       | Run pods that need stable identity & storage |
| Pod count        | One per node                | As many replicas as defined                  |
| Pod identity     | No stable identity          | Stable unique identity (`pod-0`, `pod-1`)    |
| PVC              | Not created automatically   | Automatically created for each pod           |
| Ordering         | No ordering                 | Ordered startup & shutdown                   |
| Node-level?      | Yes                         | No                                           |
| Typical use case | Agents, monitoring, logging | Databases, clusters                          |

---

## 5. How Static Pods, DaemonSets, StatefulSets Work in Production

### **Static Pods in Production**

* Used only for **control plane components**.
* kubelet manages them even when API is not responding.
* Ensures critical services run always.
* Managed via `/etc/kubernetes/manifests/`.

### **DaemonSets in Production**

DaemonSets are essential for:

* Logging (FluentD/Filebeat)
* Monitoring (Node Exporter)
* Security agents (Falco)
* CNI drivers (Calico, Cilium)

Workflow:

```
DevOps writes DaemonSet → Applied in cluster → Automatically runs on each node → Ensures observability/security.
```

### **StatefulSets in Production**

Used for databases, messaging systems, clusters.

Example workflow:

```
StatefulSet created → Each pod gets its own PVC → Ordered startup (pod-0 first) → Persistent storage maintained.
```

Production requirements:

* Stable DNS names
* Persistent storage
* Pod identity during restarts

---


* Interview Q&A for these topics
