# K9s – 

## 1. What is K9s?

**K9s** is a **terminal-based UI (TUI)** tool used for managing and interacting with Kubernetes clusters.
It provides a real-time, interactive dashboard inside your terminal to navigate and manage Kubernetes resources much faster than using kubectl commands.

Think of it as a **top-like interface for Kubernetes** — fast, live, and easy to use.

---

## 2. Why Use K9s?

K9s improves productivity by allowing you to:

* Quickly browse Kubernetes resources
* View pods, logs, deployments, services, events, nodes, and more
* Debug workloads faster
* Switch namespaces easily
* Execute commands inside pods
* Apply YAML changes directly

It reduces the need to type long kubectl commands repeatedly.

---

## 3. Key Features

### **1. Real-Time Resource Monitoring**

K9s continuously watches your Kubernetes cluster and shows live updates.

### **2. Fast Navigation**

Use simple keyboard shortcuts to jump between:

* Pods
* Deployments
* ReplicaSets
* Services
* ConfigMaps
* Nodes
* Namespaces
* Events

### **3. Logs and Exec**

You can:

* View logs of any container
* Stream logs (`follow mode`)
* Run exec shell inside a container

### **4. YAML Editing**

You can edit any Kubernetes resource directly from K9s.

```
:e   →  Edit resource YAML
```

K9s internally uses `kubectl apply`.

### **5. Context & Namespace Switching**

Change context:

```
:ctx
```

Change namespace:

```
:ns
```

### **6. Filter and Search**

K9s supports powerful filters to find resources quickly.

```
/keyword
```

---

## 4. How K9s Works Internally

K9s communicates with the Kubernetes cluster using your **kubeconfig** file, similar to kubectl.

### Flow:

```
K9s UI → kubeconfig → Kubernetes API Server → Cluster Resources
```

K9s is essentially a visual wrapper around kubectl + API calls.

---

## 5. Installing K9s

### Linux/macOS

```
curl -sS https://webinstall.dev/k9s | bash
```

Or using brew:

```
brew install k9s
```

### Windows

Use scoop or chocolatey:

```
scoop install k9s
```

---

## 6. Basic Usage

Start K9s:

```
k9s
```

### Useful Commands in K9s

* `:pods` → list pods
* `:deploy` → list deployments
* `:svc` → services
* `:nodes` → nodes
* `l` → view logs of selected pod
* `e` → edit YAML
* `ctrl + a` → show all resources
* `:` → enter command mode

---

## 7. K9s Shortcuts

| Action            | Shortcut           |
| ----------------- | ------------------ |
| Quit K9s          | `:q` or `Ctrl + c` |
| Describe resource | `d`                |
| Logs              | `l`                |
| Edit YAML         | `e`                |
| Exec into pod     | `s`                |
| Delete resource   | `dd`               |
| Switch namespace  | `:ns`              |
| Switch context    | `:ctx`             |

---

## 8. When DevOps Engineers Use K9s

K9s is extremely helpful for:

* Debugging pod issues
* Quickly looking at events
* Monitoring resource health
* Checking logs without long kubectl commands
* Managing multiple clusters
* Real-time troubleshooting in production

---

## 9. Advantages of K9s

* Faster than typing kubectl
* Real-time analytics
* Terminal-based → works everywhere
* Very lightweight
* Useful for interviews and production debugging

---

