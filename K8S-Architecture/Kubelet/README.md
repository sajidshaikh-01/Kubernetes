# Kubelet in Kubernetes

**Kubelet** is the primary agent that runs on every **worker node** in a Kubernetes cluster. Its job is to ensure that the **containers (pods)** scheduled to the node are running, healthy, and match the **desired state** defined by the API Server.

Kubelet communicates with:

* **API Server** (to get pod specs)
* **Container runtime** (Docker, containerd, CRI-O)
* **CNI plugin** (for networking)
* **CSI plugin** (for storage)

It is responsible for pod lifecycle management.

---

# 🏗️ Kubelet Architecture

```
+--------------------------------------+
|            API Server                |
|   (PodSpecs, Secrets, ConfigMaps)    |
+------------------+-------------------+
                   | Watch/Sync
                   V
+--------------------------------------+
|               Kubelet                |
|--------------------------------------|
| Pod Sync Loop                        |
| Pod Lifecycle Manager                |
| Container Runtime Interface (CRI)    |
| Volume Manager (CSI)                 |
| Network Manager (CNI)                |
| Health & Readiness Probes            |
| Node Status Updater                  |
+-----------+------------+-------------+
            |            |   
            V            V
   +--------------+   +----------------+
   |  Container   |   |  CNI / Network |
   |   Runtime    |   |    Plugins     |
   +--------------+   +----------------+
            |
            V
      Running Pods
```

---

# 🔁 How Kubelet Works (Step-by-Step)

Kubelet continuously runs a **control loop**:

```
1. Watch PodSpecs from API server
2. Compare desired state → actual state
3. Start, stop, or restart containers
4. Report status to API server
```

This loop makes Kubernetes **self-healing**.

---

# ⚙️ Detailed Workflow of Kubelet

## 1. **Kubelet registers node**

When a node joins the cluster, kubelet sends a registration request:

* Node name
* Node labels
* CPU/Memory capacity
* Operating System
* Runtime details

API Server stores this information.

---

## 2. **Kubelet watches for PodSpecs**

API Server sends:

* Pod object
* Secrets
* ConfigMaps
* Volumes
* ServiceAccount tokens

Kubelet stores them in a local cache.

---

## 3. **Pod Lifecycle Management**

Kubelet ensures pods are in the correct state:

### Includes:

* Create pod
* Pull container images
* Start containers
* Restart on crash (based on policy)
* Stop/Delete when needed

Kubelet does **not schedule pods** (scheduler does).

---

# 🔌 4. Interaction with Container Runtime (CRI)

Kubelet interacts with container runtime using **CRI (Container Runtime Interface)**.

Supported runtimes:

* containerd (default in most clusters)
* CRI-O
* Docker (via dockershim — deprecated)

### Kubelet asks runtime to:

* Pull images
* Create containers
* Start containers
* Kill containers
* Fetch logs

Example CRI call:

```
RunPodSandbox → CreateContainer → StartContainer
```

---

# 🌐 5. Networking with CNI

Kubelet calls **CNI plugin** to set network for each pod:

* Assigns IP address
* Connects pod to node network
* Mounts network namespaces

Popular CNI plugins:

* Flannel
* Calico
* Cilium
* Weave Net

---

# 💾 6. Storage with CSI

Kubelet manages volumes using **CSI (Container Storage Interface)**.

### Responsibilities:

* Mount/unmount volumes
* Attach/detach disks
* Manage persistent volumes

---

# ❤️ 7. Health Probes

Kubelet runs probes for each container:

### **Liveness Probe**

Checks if container is alive.
If fails → kubelet restarts container.

### **Readiness Probe**

Checks if pod is ready to receive traffic.
If fails → removed from Service endpoints.

### **Startup Probe**

Used for slow-starting apps.

---

# 📤 8. Node & Pod Status Reporting

Kubelet continuously sends heartbeat to API server:

* Node status
* Pod statuses
* Conditions (Ready, DiskPressure, MemoryPressure)

If kubelet stops reporting → Node marked `NotReady`.

---

# 🧠 Summary Table

| Component               | Responsibility                                |
| ----------------------- | --------------------------------------------- |
| **Pod Sync Loop**       | Ensure pod desired state matches actual state |
| **CRI**                 | Interact with container runtime               |
| **CNI**                 | Configure pod networking                      |
| **CSI**                 | Manage storage and volumes                    |
| **Health Probes**       | Restart or mark pods ready/not-ready          |
| **Node Status Updater** | Report node health                            |


