# etcd Overview

**etcd** is a **distributed, reliable key-value store** used by Kubernetes to store all cluster data, including:

* Cluster state
* Node information
* ConfigMaps
* Secrets
* API objects

It acts as the **single source of truth** for Kubernetes.

etcd is built to be **consistent and fault-tolerant**, using the **Raft consensus algorithm** to maintain data integrity across multiple nodes.

---

# Why Kubernetes Uses etcd

* Strong consistency guarantees
* Fast reads and writes
* Distributed and highly available
* Secure (TLS communication)
* Supports watch mechanism for real-time updates

---

# etcd Architecture

```
+---------------------+
|   Kubernetes API    |
|      Server         |
+----------+----------+
           |
           | gRPC over TLS
           v
+---------------------------+
|          etcd             |
|---------------------------|
|  Key-Value Store          |
|  Raft Consensus Module    |
|  WAL (Write Ahead Log)    |
|  Snapshots                |
|  Peer Communication       |
+---------------------------+
```

---

# Raft Consensus Algorithm (Used by etcd)

Raft ensures **all etcd nodes agree** on the same data even if:

* A node fails
* Network partitions occur
* Leader changes happen

Raft operates with **three main roles**:

### **1. Leader**

* Handles all writes
* Replicates log entries to followers
* Ensures consistency

### **2. Followers**

* Receive log entries from leader
* Reply with success/failure

### **3. Candidate**

* A follower becomes a candidate if the leader fails
* Starts a new election

---

# Raft Workflow

```
Client → Leader → Followers → Commit
```

### **Step-by-step flow:**

1. Client sends a write request to the **leader**.
2. Leader writes the request to its **WAL** (Write Ahead Log).
3. Leader replicates the entry to all **followers**.
4. Followers append the entry to their WALs.
5. Once majority acknowledges → Leader commits.
6. Leader applies the change to state machine.
7. Followers also commit and apply.

This ensures **strong consistency** (all nodes have the same data).

---

# Why Kubernetes Needs Raft

Raft ensures:

* No split-brain situation
* A consistent state across nodes
* Safe leader elections
* Fault tolerance (cluster stays alive even if a node dies)

---

# WAL (Write Ahead Log) in etcd

**WAL = Write Ahead Log**
A durable log where etcd writes every operation before applying it.

### **Purpose:**

* Ensures no data loss
* Helps recovery after crash
* Ensures Raft replicas stay consistent

### **WAL Characteristics:**

* Append-only
* Stored on disk
* Contains log entries with indexes

### **Crash Recovery using WAL:**

1. etcd restarts
2. Reads WAL entries
3. Reconstructs last known state
4. Applies snapshot + WAL entries

This allows etcd to recover even after an unexpected shutdown.

---

# etcd Snapshots

* Captures compressed copy of current state
* Reduces WAL size
* Faster recovery

etcd periodically takes snapshots + preserves WAL for smaller recent changes.

---

# Summary Table

| Component          | Purpose                                 |
| ------------------ | --------------------------------------- |
| **etcd**           | Stores all Kubernetes state             |
| **Raft Consensus** | Ensures consistent state across nodes   |
| **WAL**            | Durable logging for crash recovery      |
| **Leader Node**    | Writes data and replicates to followers |
| **Followers**      | Replicate data and commit               |
| **Snapshots**      | Efficient state backups                 |

---


