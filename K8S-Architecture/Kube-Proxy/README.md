# Kube-Proxy in Kubernetes

**kube-proxy** is a network component that runs on **every worker node** in Kubernetes. Its main job is to enable communication between **Pods and Services**. It makes sure that any request to a Kubernetes Service is correctly routed to one of the backend Pods.

kube-proxy works at the **node level** and manages networking rules using Linux networking technologies.

---

# 🧩 What Kube-Proxy Does

* Watches Services & Endpoints from **API Server**
* Maintains routing rules on each node
* Performs **load balancing** across Pod IPs
* Ensures Pods can access ClusterIP, NodePort, and LoadBalancer services

It does *not* handle Pod-to-Pod networking — that's done by CNI.

---

# 🏗️ How Kube-Proxy Works Internally

kube-proxy runs on each worker node and behaves like this:

```
+-------------------------+
|      API Server         |
+------------+------------+
             | Watches
             V
+-------------------------+
|      kube-proxy        |
|-------------------------|
| Service Watcher        |
| Endpoint Watcher       |
| iptables/ipvs Manager   |
| Userspace Mode (old)   |
+-----------+-------------+
            |
            V
+-------------------------+
| Node Networking Stack  |
| iptables / IPVS / eBPF |
+-------------------------+
```

kube-proxy listens for:

* **Service objects** → (ClusterIP, NodePort, LB)
* **Endpoint objects** → list of Pod IPs that belong to the Service

Then it programs routing rules on the node.

---

# ⚙️ Modes of kube-proxy

## 1. **iptables Mode (Default in many clusters)**

* Most common
* Scalable, fast
* Uses NAT and connection tracking

### How it works:

* Creates iptables chains like:

  * `KUBE-SERVICES`
  * `KUBE-NODEPORTS`
  * `KUBE-SEP-*` (Service Endpoints)
  * `KUBE-SVC-*` (Virtual Service)
* Incoming traffic → matched to service → distributed to Pods

### Load Balancing:

```
random-fully, round-robin
```

---

## 2. **IPVS Mode (Better Performance)**

* Uses Linux IP Virtual Server (L4 load balancer)
* More efficient for very large clusters
* Requires kernel modules

### Load balancing algorithms:

* rr (round robin)
* lc (least connection)
* sh (source hashing)

### IPVS Advantages:

* Faster load balancing
* Better performance under heavy load
* More stable connection handling

---

## 3. **Userspace Mode (Legacy)**

* Old, slow
* kube-proxy acted as a TCP/UDP proxy
* Not used now

---

# 🚀 How Traffic Flows Through kube-proxy

A Pod wants to access a service:

```
pod → service ClusterIP:port
```

### Step-by-step:

1. Pod sends request to Service ClusterIP
2. Request hits node's network stack
3. iptables/IPVS rules created by kube-proxy intercept traffic
4. kube-proxy chooses one backend Pod IP
5. Traffic is DNAT'ed to Pod IP:Port
6. Packet delivered to Pod

### Example:

```
Service: myapp
ClusterIP: 10.96.0.15
Backend Pods:
  10.244.1.5
  10.244.2.8
  10.244.3.10
```

kube-proxy rules:

* "If traffic goes to 10.96.0.15:80 → choose one Pod"

---

# 🔄 NodePort and kube-proxy

For NodePort type services:

* kube-proxy opens a port on every node
* Example: NodePort 30080

Traffic flow:

```
Client → NodeIP:30080 → kube-proxy rules → Pod
```

Works even if Pod is on another node.

---

# ☁️ LoadBalancer Services

Cloud LB sends traffic to NodePorts.

Flow:

```
External LB → NodePort (via kube-proxy) → Pod
```

---

# 🧠 Key Features of kube-proxy

* Node-level service router
* L4 load balancing
* Dynamic rule updates
* Handles cluster services (ClusterIP, NodePort, LB)

---

