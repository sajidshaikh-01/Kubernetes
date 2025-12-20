# Kubernetes 

## 📘 What is Kubernetes?
kubernetes is a just controller
---
**Kubernetes (K8s)** is an open-source container orchestration platform that automates:

* Deployment
* Scaling
* Management
* Networking
* Monitoring

of containerized applications.

It ensures your applications run reliably across clusters of machines.

---

## 🕰️ Brief History of Kubernetes

* Developed by **Google**, based on their internal container orchestration system **Borg**.
* **2014** → Kubernetes open-sourced.
* **2015** → Donated to CNCF (Cloud Native Computing Foundation).
* **Today** → Industry standard for container orchestration.

Major cloud providers support Kubernetes:

* AWS (EKS)
* Azure (AKS)
* GCP (GKE)
* DigitalOcean Kubernetes
* On-prem: Rancher, OpenShift, VMware

---

## ⭐ Advantages of Kubernetes

### 1. **High Availability (HA)**

Ensures your application is always running even if nodes fail.

### 2. **Auto Scaling**

Scale applications based on CPU, RAM, custom metrics.

### 3. **Self Healing**

Automatically restarts failed containers.

### 4. **Load Balancing**

Distributes traffic efficiently using Services.

### 5. **Rolling Updates & Rollbacks**

Zero-downtime deployments.

### 6. **Infrastructure Abstraction**

Run containers anywhere — cloud, on-prem, hybrid, edge.

### 7. **Efficient Resource Utilization**

Scheduler optimizes pod placement.

### 8. **Extensible & Open Ecosystem**

Supports custom controllers, operators, CRDs.

---

# 🧩 Key Concepts

Below are the core building blocks of Kubernetes.

---

## 🟦 **Cluster**

A **Kubernetes Cluster** is a group of machines (nodes) working together to run containerized apps.

It has two types of nodes:

* **Control Plane (Master Node)** → brain of the cluster
* **Worker Nodes** → where applications run

---

## 🟨 **Node**

A **Node** is a worker machine in Kubernetes. It can be:

* A physical server
* A virtual machine (most common)

Each node runs:

* **Kubelet** → communicates with control plane
* **Container Runtime** (Docker, containerd, CRI-O)
* **Kube-proxy** → networking

Nodes are where your **Pods** actually run.

---

## 🟩 **Pod**

A **Pod** is the smallest deployable unit in Kubernetes.

### Pod contains:

* One or more containers
* Shared network namespace
* Shared storage volumes

### Why multiple containers inside a Pod?

* Logging sidecar
* Monitoring sidecar
* Proxy containers

### Example:

A web app + a log collector → both inside same Pod.




If you want next: *ReplicaSet*, *Deployments*, *Service types*, *Ingress*, etc. just tell me!
