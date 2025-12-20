# CRI, CNI, CSI in Kubernetes

Kubernetes uses a **plug‑in architecture** to manage containers, networking, and storage. These plug-in standards allow Kubernetes to work with many different container runtimes, networking systems, and storage systems.

The three most important interfaces are:

* **CRI – Container Runtime Interface**
* **CNI – Container Network Interface**
* **CSI – Container Storage Interface**

This README explains all three in simple, clear detail.

---

# 🧱 1. CRI — Container Runtime Interface

**CRI defines how Kubelet communicates with container runtimes.**

Kubelet does NOT run containers directly — it talks to a container runtime using CRI.

Supported container runtimes:

* **containerd** (default in many clusters)
* **CRI‑O**
* **Docker** (deprecated in Kubernetes, but Docker uses containerd underneath)

### 🔧 How CRI Works

CRI has two major components:

1. **kubelet** → client
2. **container runtime** (via CRI plugin)

They communicate using:

* **gRPC protocol**
* Over Unix socket (`/run/containerd/containerd.sock`)

### 🔁 CRI Workflow

1. Kubelet gets PodSpec from API Server
2. Kubelet calls CRI:

   * `RunPodSandbox`
   * `CreateContainer`
   * `StartContainer`
3. Runtime pulls image
4. Runtime starts container
5. Runtime reports status back to Kubelet

### 🔍 CRI Responsibilities

* Pull container images
* Start/stop containers
* Manage container lifecycle
* Provide logs & stats

---

# 🌐 2. CNI — Container Network Interface

**CNI defines how container networking is configured.**

Every Pod needs:

* A unique IP address
* Network namespace
* Route to other pods

CNI plugins take care of this.

Common CNI plugins:

* **Calico** → network + security policies
* **Flannel** → simple overlay
* **Cilium** → eBPF-based high performance networking
* **Weave Net**

### 🔧 How CNI Works

When Kubelet starts a pod:

1. It calls the CNI plugin (binary)
2. Plugin assigns pod IP
3. Plugin sets up routes
4. Plugin configures virtual interfaces
5. Plugin connects pod to cluster network

### 📦 CNI Responsibilities

* Pod IP allocation
* Networking between pods
* Network policies
* Managing routes & bridges

---

# 💾 3. CSI — Container Storage Interface

**CSI defines how Kubernetes interacts with storage providers.**

Instead of Kubernetes depending on in-tree drivers, CSI allows external storage vendors to integrate.

### 🔧 What CSI Enables

* Dynamic PV provisioning
* Attach/detach storage volumes
* Mount/unmount volumes
* Cloud disk support

### Examples of CSI Drivers

* AWS EBS CSI
* GCP Persistent Disk CSI
* Azure Disk/File CSI
* CephFS / Rook
* NFS CSI

### 📁 How CSI Works

When a Pod uses a PVC:

1. Kubelet calls CSI driver to attach disk
2. CSI driver mounts disk to node
3. Pod uses the volume
4. On delete:

   * Volume is unmounted
   * Disk is detached

### 📦 CSI Responsibilities

* Volume creation (if StorageClass used)
* Attaching volumes to nodes
* Mounting volumes inside Pod
* Snapshots & clones (if supported)

---

# 🔥 Summary Table

| Interface | Full Form                   | Responsible For    | Examples                |
| --------- | --------------------------- | ------------------ | ----------------------- |
| **CRI**   | Container Runtime Interface | Running containers | containerd, CRI-O       |
| **CNI**   | Container Network Interface | Pod networking     | Calico, Flannel, Cilium |
| **CSI**   | Container Storage Interface | Volumes & storage  | AWS EBS CSI, Ceph, NFS  |

---
