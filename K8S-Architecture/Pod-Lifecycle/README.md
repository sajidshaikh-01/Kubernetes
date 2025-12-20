# Kubernetes Pod Lifecycle Architecture-
✔ API Server → etcd → Scheduler → Kubelet → CRI → CNI → CSI

---

# 🏗️ High-Level Pod Creation Architecture

```
User / kubectl
      |
      | 1. REST Request (YAML / command)
      V
+------------------------+
|     API Server         |
+------------------------+
      |
      | 2. Store PodSpec in etcd
      V
+------------------------+
|        etcd            |
+------------------------+
      |
      | 3. Scheduler watches for "Pending" Pod
      V
+------------------------+
|   Kube-Scheduler       |
+------------------------+
      |
      | 4. Select best node
      |    Bind Pod→Node
      V
+------------------------+
|     API Server         |
+------------------------+
      |
      | 5. Node kubelet watches assigned pods
      V
+------------------------+
|       Kubelet          |
+------------------------+
      |
      | 6. Create Pod Sandbox via CRI
      V
+------------------------+
|  Container Runtime     |
| (containerd / CRI-O)   |
+------------------------+
      |
      | 7. Network Setup via CNI
      V
+------------------------+
|         CNI            |
+------------------------+
      |
      | 8. Storage Setup via CSI
      V
+------------------------+
|         CSI            |
+------------------------+
      |
      | 9. Start Containers
      V
+------------------------+
|     Running Pod        |
+------------------------+
```

---

# 🔥 FULL DETAILED FLOW — HOW A POD ACTUALLY RUNS

Below is the **complete real flow**, exactly as Kubernetes works internally.

---

# ✅ Step 1: User Sends Pod Creation Request

Ways to create a Pod:

* `kubectl apply -f pod.yaml`
* `kubectl run nginx`
* API call from an operator or controller

Request hits the **API Server**.

---

# ✅ Step 2: API Server Validates & Stores in etcd

API Server performs:

1. Authentication
2. Authorization (RBAC)
3. Admission Controllers
4. Schema Validation

Then it stores the Pod object in **etcd** with status:

```
status: Pending
```

---

# ✅ Step 3: Scheduler Detects Pending Pod

Scheduler watches API Server.
When it sees a Pod with no `nodeName` assigned → it starts scheduling cycle.

Scheduler runs:

* Filter plugins → select eligible nodes
* Score plugins → pick best node

Then decides:

```
Pod should run on node: worker-node-2
```

---

# ✅ Step 4: Scheduler Binds Pod to Node

Scheduler sends a **Binding Object** to API Server:

```
Pod → worker-node-2
```

API Server updates etcd.

Now Pod status:

```
status: Pending
nodeName: worker-node-2
```

---

# ✅ Step 5: Kubelet on That Node Sees the Pod

Every kubelet watches the API Server, but **only the kubelet on assigned node** receives the PodSpec.

Kubelet does:

* Download PodSpec
* Prepare containers
* Setup volumes
* Setup networking

---

# ✅ Step 6: Kubelet Calls CRI (Container Runtime Interface)

Kubelet doesn’t run containers directly.

It calls CRI runtime (containerd or CRI-O):

1. `RunPodSandbox` → create Pod sandbox
2. `PullImage` → download images
3. `CreateContainer`
4. `StartContainer`

Runtime creates:

* Pod sandbox
* Network namespace
* Containers

---

# ✅ Step 7: CNI Sets Up Pod Networking

Kubelet triggers the **CNI plugin**.

CNI responsibilities:

* Assign pod IP
* Create veth pair
* Attach pod to bridge (cni0)
* Add routing rules

Result:

```
Pod gets IP: 10.244.x.x
```

---

# ✅ Step 8: CSI Prepares Storage (If Needed)

If Pod uses a PVC:

* kubelet calls CSI driver
* CSI attaches/mounts the volume
* Volume mounted inside container

---

# ✅ Step 9: Containers Start Running

Container runtime finally launches containers.

Kubelet starts health probes:

* livenessProbe
* readinessProbe
* startupProbe

Updates API Server:

```
status: Running
```

---

# 🎉 Final: Pod is Running

Cluster networking (kube-proxy or eBPF) makes Pod reachable via Service.

---

# 🧠 Full Summary Table

| Phase        | Component              | What Happens                    |
| ------------ | ---------------------- | ------------------------------- |
| Request      | User → API Server      | YAML validated & stored in etcd |
| Scheduling   | Scheduler              | Selects node & binds pod        |
| Node Control | Kubelet                | Prepares & runs pod             |
| Runtime      | containerd/CRI-O (CRI) | Creates containers              |
| Network      | CNI plugin             | Assigns IP & connects pod       |
| Storage      | CSI plugin             | Mounts volumes                  |

---

# 🧩 Additional Low-Level Components: runC and cgroups

Below are the missing internal components involved when a container actually *runs* inside a Pod.

## 🔥 runC — The Low-Level Container Runtime

**runC** is the OCI-compliant low-level runtime used underneath containerd and CRI-O.

### What runC does:

* Creates **Linux namespaces** (PID, NET, IPC, UTS, MNT)
* Creates **cgroups** for resource limits
* Starts the container process using the container image root filesystem

### Flow:

```
Kubelet → CRI (containerd/CRI-O) → runC → Start container
```

runC creates the *isolated environment* in which the container runs.

---

## 🧱 cgroups — Resource Isolation in Kubernetes

**cgroups (Control Groups)** are a Linux kernel feature used to control resource usage.

Kubernetes uses cgroups to limit and track:

* CPU usage
* Memory usage
* I/O
* PIDs

### How Kubernetes Uses cgroups

When you define:

```
resources:
  limits:
    cpu: "500m"
    memory: "256Mi"
```

Kubelet enforces these limits through cgroups.

### Example:

* CPU cap → cgroup quota
* Memory limit → cgroup memory.max

### Container Flow with cgroups:

1. runC creates a new cgroup for the container
2. Kubelet sets resource limits
3. Container runs under this cgroup
4. Linux kernel enforces limits

If memory exceeds the limit → **OOMKilled**.

---

## 🧠 Final Low-Level Flow (Very Detailed)

```
API Server → Scheduler → Kubelet
      ↓            ↓
CRI (containerd/CRI-O)
      ↓
runC (Create namespaces + cgroups)
      ↓
CNI (Networking)
      ↓
CSI (Storage)
      ↓
Container starts in isolated environment
```

runC = creates container
cgroups = enforce pod/container resource isolation
