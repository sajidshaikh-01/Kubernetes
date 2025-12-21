# Kubernetes Pods - Explanation, Types, and Examples

## 1. What is a Pod?

A **Pod** is the smallest, basic deployable unit in Kubernetes. It represents **a single instance of a running process** in your cluster.

A pod can contain **one or more containers** that:

* Share the same network namespace (same IP, ports).
* Share storage volumes.
* Communicate using `localhost`.

Pods are **ephemeral**, meaning they are not self-healing. If a pod dies, Kubernetes replaces it using ReplicaSet/Deployment.

---

## 2. Why Pods?

Pods provide a wrapper around containers to offer:

* Shared networking
* Shared storage
* Attached metadata
* Simplified orchestration

Containers inside the same pod are tightly coupled and should run together.

---

## 3. Types of Pods

Kubernetes primarily has 3 types of pod configurations.

### **1. Single-Container Pod**

The most common type. One application per pod.

**Use Case:** Web server, API, microservice.

#### **Example YAML**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
    - name: nginx
      image: nginx:latest
```

---

### **2. Multi-Container Pod**

Pod contains **multiple containers** that share resources and work together.

Two major patterns:

* **Sidecar Pattern** (logging, monitoring, proxy)
* **Adapter Pattern**
* **Ambassador Pattern**

#### **Example YAML (Sidecar)**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: log-sidecar-pod
spec:
  containers:
    - name: app-container
      image: busybox
      command: ["sh", "-c", "echo Hello from app && sleep 300"]
    - name: log-agent
      image: busybox
      command: ["sh", "-c", "tail -f /var/log/app.log"]
```

---

### **3. Init Container Pod**

Pods that have **init containers** which run *before* main containers.

Init containers:

* Run once
* Must complete successfully
* Prepare the environment

#### **Example YAML**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: init-demo
spec:
  initContainers:
    - name: init-setup
      image: busybox
      command: ['sh', '-c', 'echo Preparing environment && sleep 5']
  containers:
    - name: main-app
      image: nginx
```

---

## 4. Pod Lifecycle

Pods go through these phases:

* **Pending**
* **Running**
* **Succeeded**
* **Failed**
* **Unknown**

---

## 5. Pod Use Cases

* Running microservices
* Running system daemons (CNI, CSI)
* Running background jobs
* Running tightly coupled containers

---

## 6. Summary

* Pod = smallest deployment unit in Kubernetes.
* Can run 1 or more containers.
* Types:

  * Single container pod
  * Multi-container pod
  * Init container pod
* Pods are ephemeral and replaced by higher controllers (Deployment, ReplicaSet).

