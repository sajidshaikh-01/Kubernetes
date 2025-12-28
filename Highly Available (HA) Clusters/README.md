# Highly Available (HA) Clusters – 
---

## 1. Fundamentals of High Availability

### What is High Availability (HA)

* Ability of a system to remain operational despite failures
* Focuses on minimizing downtime
* Achieved via redundancy, failover, and automation

### HA vs Fault Tolerance vs Disaster Recovery

* HA: short disruption allowed
* Fault Tolerance: zero disruption
* DR: recovery after major failure (region-level)

### SLAs, SLOs, SLIs

* SLA: contractual uptime
* SLO: internal reliability target
* SLI: actual measurement (latency, error rate)

---

## 2. Kubernetes Control Plane High Availability

### HA Control Plane Architecture

* Multiple kube-apiserver instances
* controller-manager with leader election
* scheduler with leader election
* etcd cluster (odd number of members)

### Load Balancer for API Server

* Single endpoint for kubectl and components
* Health checks on kube-apiserver

### Leader Election

* controller-manager and scheduler use leader locks
* Ensures only one active leader at a time

---

## 3. etcd Deep Dive (Critical Topic)

### What is etcd

* Distributed key-value store
* Source of truth for Kubernetes

### etcd High Availability

* Always use odd number of nodes (3, 5, 7)
* Spread across Availability Zones

### Quorum (VERY IMPORTANT)

* Quorum = majority of members must agree
* Formula: (N/2) + 1

Examples:

* 3 nodes → quorum = 2
* 5 nodes → quorum = 3

If quorum is lost:

* Writes stop
* Cluster becomes read-only or unusable

### etcd Failure Scenarios

* Loss of 1 node in 3-node cluster → OK
* Loss of 2 nodes in 3-node cluster → cluster down

### etcd Backup & Restore

* Snapshot backups
* Scheduled automated backups
* Restore for disaster recovery

---

## 4. Worker Node High Availability

### Multi-Node Strategy

* Multiple worker nodes
* Spread across AZs

### Node Failure Handling

* kubelet heartbeat failure
* Node marked NotReady
* Pods rescheduled automatically

### Node Auto-Scaling

* Cluster Autoscaler
* Auto-replace unhealthy nodes

---

## 5. Application-Level High Availability

### Replicas and Deployments

* Always run more than one replica
* Stateless apps scale easily

### StatefulSets HA

* Persistent volumes
* Application-level replication (DB replicas)

### Pod Disruption Budgets (PDB)

* Controls voluntary disruptions
* Prevents all replicas going down during maintenance

---

## 6. Networking and Traffic HA

### Service High Availability

* kube-proxy / eBPF-based load balancing
* EndpointSlices

### Ingress / Gateway HA

* Multiple ingress controller replicas
* External Load Balancer

### DNS High Availability

* CoreDNS replicas
* Pod anti-affinity

---

## 7. Storage High Availability

### Persistent Volume HA

* Network-backed storage (EBS, Azure Disk, PD)
* Zonal vs Regional storage

### Storage Failure Scenarios

* Node failure with attached volume
* Re-attach volume to new node

### Stateful App Considerations

* Database replication
* Backup + restore strategy

---

## 8. Scheduling and Placement (Very Important)

### Pod Anti-Affinity

* Prevents replicas from running on same node

### Topology Spread Constraints

* Even distribution across nodes/AZs

### Taints and Tolerations

* Protect critical nodes

---

## 9. Cluster Upgrades (Production Critical)

### Control Plane Upgrade Strategy

* Upgrade one control plane node at a time
* Maintain etcd quorum

### Worker Node Upgrade Strategy

* Rolling upgrades
* Drain node → upgrade → uncordon

### Zero Downtime Upgrades

* Sufficient replicas
* Proper PDB configuration

### Managed Kubernetes Upgrades

* EKS / AKS / GKE handle control plane HA
* You manage worker node upgrades

---

## 10. Downgrading Kubernetes Clusters

### Kubernetes Downgrade Reality

* Control plane downgrade: NOT supported
* etcd downgrade: risky and discouraged

### Safe Downgrade Approach

* Restore from etcd snapshot
* Recreate cluster with older version

### Best Practice

* Always test upgrades in staging
* Never rely on downgrade in production

---

## 11. Cluster Benchmarking & Performance

### What is Benchmarking

* Measure cluster capacity and limits

### Benchmarking Areas

* API server performance
* etcd latency
* Pod startup time
* Network throughput

### Common Tools

* kube-burner
* kube-bench (security baseline)
* kubemark

### Why Benchmarking Matters

* Capacity planning
* Detect bottlenecks
* Prevent overload failures

---

## 12. Observability for HA

### Metrics

* Node health
* Pod restarts
* API server latency
* etcd leader changes

### Logs

* kube-apiserver
* etcd logs
* controller-manager logs

### Alerts

* Node NotReady
* etcd quorum loss
* High error rates

---

## 13. Security and HA

### RBAC and API Availability

* Prevent accidental lockouts

### Certificate Rotation

* kubelet certs
* control plane certs

### Secure etcd

* TLS encryption
* Restricted access

---

## 14. Failure Scenarios You Must Know (Interview Gold)

* Control plane node failure
* Worker node failure
* AZ outage
* etcd quorum loss
* Network partition
* Bad deployment causing outage

---

## 15. Production Best Practices Summary

* Use odd-numbered etcd clusters
* Multi-AZ deployment
* Minimum 3 replicas for critical apps
* Always configure PDBs
* Test upgrades regularly
* Monitor quorum and control plane health

---

## 16. Interview Preparation Focus

You must be able to explain:

* How quorum works
* What happens when etcd loses quorum
* How Kubernetes upgrades without downtime
* Why downgrades are dangerous
* Difference between HA and DR
* Real failure handling scenarios

---

This README covers **everything required** to confidently design, operate, and explain **Highly Available Kubernetes clusters** at a production level.
