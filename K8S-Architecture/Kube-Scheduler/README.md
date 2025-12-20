# Kubernetes Scheduler

The **Kube Scheduler** is a core control plane component responsible for deciding **which node** a newly created Pod should run on. It does **not** run the pod itself — it only makes scheduling decisions.

Once a pod is created (Pending state), the scheduler checks all available nodes and finds the **best possible node** based on constraints, rules, and scoring.

---

# 🏗️ Kube Scheduler Architecture

```
                 +--------------------------+
                 |        API Server        |
                 +------------+-------------+
                              |
                        Watch Pods (Pending)
                              |
                              V
                +----------------------------+
                |      Kube Scheduler        |
                |----------------------------|
                | 1. Scheduling Queue        |
                | 2. Scheduling Framework    |
                |    - Filter Plugins        |
                |    - Score Plugins         |
                | 3. Preemption Logic        |
                | 4. Binding                 |
                +--------------+-------------+
                               |
                        Bind Pod to Node
                               |
                               V
                        +--------------+
                        |   API Server |
                        +--------------+
```

---

# ⚙️ Scheduler Key Components

### **1. Scheduling Queue**

All pods waiting to be scheduled are placed in a **PriorityQueue**.
Higher-priority pods move ahead.

### **2. Scheduling Framework (Plugins)**

A modular system containing plugins that perform filtering and scoring.

### **3. Preemption Logic**

If no nodes fit:

* Scheduler may evict lower-priority pods to make room.

### **4. Binding**

After a node is selected, the scheduler **submits a binding object**:

```
Pod → Node
```

This updates API server, and kubelet will then run the pod.

---

# 🔁 Full Scheduling Cycle (Step-by-Step)

The scheduling cycle consists of **two main phases**:

1. **Scheduling Cycle** (find a node)
2. **Binding Cycle** (assign pod to node)

---

# 🟦 1. Scheduling Cycle (Node Selection)

This is where the scheduler decides **which node** is suitable.

### **Step-by-Step Workflow**

1. Pod enters **scheduling queue**.
2. Pop Pod from queue.
3. Run **Filter Plugins** → eliminate unsuitable nodes.
4. Run **Score Plugins** → rate each node.
5. Pick the highest-scored node.
6. Reserve the node (optional plugin phase).
7. Scheduling cycle ends.

### **Filter Plugins (Node Filtering)**

Filters remove nodes that cannot run the pod.
Examples:

* `NodeResourcesFit` → CPU/Memory check
* `NodeAffinity` → match node labels
* `PodTopologySpread` → spread pods evenly
* `Taints & Tolerations`

### **Score Plugins (Node Scoring)**

Score plugins assign numerical weight to select **best** node.
Examples:

* Least loaded node
* Node with max free resources
* Node with matching affinity

### Result:

**One best node is selected.**

---

# 🟩 2. Binding Cycle (Binding the Pod)

After selecting the best node → the scheduler moves to binding.

### **Step-by-Step Workflow**

1. Run **PreBind plugins** → final checks
2. Send a **Bind API call** to API server:

```
Pod is assigned to Node
```

3. Run **PostBind** plugins
4. Kubelet on that node starts pulling container images and running pod

---

# 🔄 Complete Scheduling Workflow

```
          [New Pod Created]
                  |
                  V
    +------------------------------+
    |   Enters Scheduling Queue    |
    +--------------+---------------+
                   |
          Scheduling Cycle
                   |
       - Filtering (Unsuitable nodes removed)
       - Scoring (Best node selected)
                   |
                   V
    +------------------------------+
    |   Node Selected for Pod      |
    +--------------+---------------+
                   |
          Binding Cycle
                   |
       - PreBind → Bind → PostBind
                   |
                   V
    +------------------------------+
    |   Pod Assigned to Node       |
    +------------------------------+
                   |
              Kubelet runs pod
```

---

# 🔥 Summary Table

| Cycle                | Purpose          | Includes                    |
| -------------------- | ---------------- | --------------------------- |
| **Scheduling Cycle** | Select best node | Filtering, Scoring, Reserve |
| **Binding Cycle**    | Bind pod to node | PreBind, Bind, PostBind     |

