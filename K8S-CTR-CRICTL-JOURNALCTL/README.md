# CTR, CRICTL, and JOURNALCTL (Kubernetes & Linux Tools)

## 1. **ctr** (Containerd CLI)

**ctr** is a low-level command-line tool provided by **containerd**. It is mainly used for debugging or interacting directly with containerd.

### 📌 Key Points

* Low-level tool (not recommended for day-to-day Kubernetes use)
* Talks directly to containerd socket
* Used for debugging images, containers, namespaces inside containerd
* Kubernetes does **not** use `ctr` internally

### 🔧 Common Commands

* List containers: `ctr -n k8s.io containers list`
* List images: `ctr -n k8s.io images list`
* Shell inside container: `ctr -n k8s.io tasks exec -t <task-id> /bin/sh`

### ❗ When to use?

* When CRI (ContainerRuntime Interface) is failing
* When kubelet cannot talk to containerd
* For deep debugging environments

---

## 2. **crictl** (Container Runtime CRI CLI)

`crictl` is the **recommended** command-line tool for debugging Kubernetes container runtime.

### 📌 Key Points

* Official CLI for Kubernetes CRI (Container Runtime Interface)
* Works with **containerd**, **CRI-O**, and other CRI-supported runtimes
* Lightweight & kube-friendly
* Kubelet communicates with CRI; `crictl` lets you debug that communication

### 🔧 Common Commands

* Check runtime: `crictl info`
* List pods: `crictl pods`
* List containers: `crictl ps`
* Stream logs: `crictl logs <container-id>`
* Inspect container: `crictl inspect <container-id>`

### ❗ When to use?

* When `kubectl` shows Pod stuck at **ContainerCreating**
* When container image pull fails
* When kubelet can’t start runtime

### 🆚 `ctr` vs `crictl`

| Feature              | ctr               | crictl                      |
| -------------------- | ----------------- | --------------------------- |
| Level                | Low-level         | High-level (K8s compatible) |
| Works via            | containerd socket | CRI plugin                  |
| Recommended for K8s  | ❌ No              | ✅ Yes                       |
| Debug Kubelet issues | ❌ Hard            | ✅ Best                      |

---

## 3. **journalctl** (Linux logs viewer)

`journalctl` is a tool to view logs managed by **systemd journal**.

### 📌 Key Points

* Shows logs for system services
* Useful for debugging kubelet, containerd, docker, etc.
* Works on systemd-based Linux systems (Ubuntu, CentOS, RHEL)

### 🔧 Common Commands

* View kubelet logs: `journalctl -u kubelet -f`
* View containerd logs: `journalctl -u containerd -f`
* View logs since 10 min: `journalctl --since "10 min ago"`
* View boot logs: `journalctl -b`
* View logs by priority: `journalctl -p err`

### ❗ When to use?

* Kubelet not starting
* Node not joining cluster
* Containerd failing
* Network plugin failing

