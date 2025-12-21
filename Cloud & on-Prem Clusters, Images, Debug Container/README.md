# Kubernetes: Cloud & On‑Prem Clusters, Images, Debug Containers

## 1. Cloud Clusters vs On‑Prem Clusters

### **Cloud Kubernetes Clusters**

Cloud providers offer managed Kubernetes services.
Examples: **EKS (AWS)**, **AKS (Azure)**, **GKE (Google Cloud)**.

#### **Features**

* Control Plane is managed by cloud provider.
* Automatic control-plane upgrades & patching.
* Built‑in integrations (Load Balancers, IAM, Storage, VPC, Monitoring).
* High availability by default.
* Easy to scale node groups.

#### **When to Use**

* When you want reliability.
* When you don’t want to manage etcd/control-plane.
* When your apps run mostly in cloud.

#### **Architecture**

* **Cloud manages:** API Server, etcd, Scheduler, Controller Manager.
* **You manage:** Worker nodes, apps, deployments.

---

### **On‑Prem Kubernetes Clusters (Self‑Managed)**

Examples: **Kubeadm**, **RKE2**, **OpenShift**, **VMware Tanzu**.

#### **Features**

* You control entire cluster.
* Full customization of networking, storage.
* Requires managing etcd, control-plane, upgrades.
* Needs monitoring, backup, disaster recovery setup.

#### **When to Use**

* For strict data residency rules.
* When workloads must run inside your data center.
* For cost control in large-scale environments.

#### **Architecture**

* **You manage:** Control-plane + Worker nodes.
* Need to configure: CNI, CSI, load balancers, storage, ingress.

---

## 2. Kubernetes Images

Container images used for running pods.

### **Key Points**

* Stored in container registries: Docker Hub, ECR, ACR, GCR, Harbor.
* Downloaded by container runtime (containerd).
* Described in Pod/Deployment YAML under `spec.containers.image`.

### **Image Format**

* Layers: Immutable and shared.
* Built using Dockerfile or BuildKit.
* Stored and cached on node.

### **How Kubernetes Pulls Images**

1. Kubelet checks if image exists on node.
2. If not, asks containerd to pull it.
3. containerd talks to registry.
4. Image pulled → stored locally → container starts.

### **Image Pull Policies**

* **IfNotPresent** (default): Pull only if not available locally.
* **Always**: Always pull from registry.
* **Never**: Never pull.

### **Image Best Practices**

* Use smaller base images (alpine, distroless).
* Do not run as root.
* Multi‑stage Docker builds.
* Tag with versions, not `latest`.

---

## 3. Debug Containers (Ephemeral Containers)

Debug containers help troubleshoot running pods **without restarting them.**

### **Why Needed?**

* Sometimes a container doesn’t have `bash`, `curl`, or debugging tools.
* You need access *inside the pod’s namespace*.

### **Commands for Debug Containers**

Requires Kubernetes 1.18+.

#### **View pod for debugging**

```
kubectl debug -it <pod-name> --image=busybox --target=<container-name>
```

#### **Explaination**

* `kubectl debug` → creates an **ephemeral container**.
* `--image=busybox` → debug image with tools.
* `--target` → attaches to process namespace of an existing container.

### **Capabilities**

* Share PID/Network/IPC namespaces with existing container.
* Run debugging commands like:

  * `ps aux`
  * `netstat -tnlp`
  * `curl` to check connectivity
  * `cat /var/log/...`

### **Debug Container Use Cases**

* Application not responding → inspect running processes.
* Network issues → use `curl`, `ping`, `nslookup`.
* File permission issues → read container file system.
* CrashLoopBackoff troubleshooting.

---

## 4. Summary

### **Cloud Clusters**

* Managed control-plane, easy scaling, integrations.

### **On‑Prem Clusters**

* Full control but need to manage everything.

### **Images**

* Pulled by containerd, stored locally, follow best practices.

### **Debug Containers**

* Ephemeral containers for runtime debugging without restarts.

