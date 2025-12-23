# Kubernetes Storage Explained — Volumes, PV, PVC, StorageClass & Dynamic Provisioning
---
## 1️⃣ Why Storage is Needed in Kubernetes

Pods are **ephemeral**:

* Pod restarts → data lost
* Pod rescheduled → data lost

But production applications need **persistent data**:

* Databases
* Logs
* User uploads
* Application state

That’s why Kubernetes provides **Volumes & Persistent Storage**.

---

## 2️⃣ What is a Volume?

A **Volume** is storage attached to a **Pod**.

Simple meaning:

> Volume = directory mounted inside a container

### Example (emptyDir – temporary volume)

```yaml
volumes:
- name: cache
  emptyDir: {}
```

### Important:

* Volume lifecycle = Pod lifecycle
* Pod deleted → data gone

Used for:

* Cache
* Temp files

❌ Not for production data

---

## 3️⃣ What is a Persistent Volume (PV)?

A **PersistentVolume** is **actual storage** provided to the cluster.

Think of it as:

> PV = Real disk created by admin or cloud

Examples:

* AWS EBS
* Azure Disk
* GCP Persistent Disk
* NFS

### Example PV (Static provisioning)

```yaml
apiVersion: v1
kind: PersistentVolume
spec:
  capacity:
    storage: 10Gi
  accessModes:
  - ReadWriteOnce
  awsElasticBlockStore:
    volumeID: vol-123456
```

---

## 4️⃣ What is a PersistentVolumeClaim (PVC)?

A **PVC** is a **request for storage** by an application.

Think of it as:

> PVC = "I need 10Gi disk"

Application teams create PVCs — not PVs.

### Example PVC

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
```

---

## 5️⃣ PV–PVC Binding (How they connect)

```
PVC (request)
   ↓
PV (actual disk)
   ↓
Pod mounts PVC
```

Kubernetes automatically binds:

* PVC → matching PV

Matching based on:

* Size
* Access mode
* StorageClass

---

## 6️⃣ What is a StorageClass?

A **StorageClass** defines:

* Type of storage
* Performance
* Provisioner

Think of it as:

> StorageClass = Template for creating disks

### Example StorageClass (EKS – gp3)

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: gp3
provisioner: ebs.csi.aws.com
parameters:
  type: gp3
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

---

## 7️⃣ Dynamic Provisioning (MOST IMPORTANT)

Dynamic provisioning means:

> Kubernetes creates the disk **automatically** when PVC is created.

No need to manually create PVs.

---

## 8️⃣ Dynamic Provisioning Flow (Production)

```
PVC created
   ↓
StorageClass selected
   ↓
CSI Provisioner
   ↓
Cloud disk created (EBS)
   ↓
PV auto-created
   ↓
PVC bound
   ↓
Pod uses volume
```

This is how **99% of production clusters work**.

---

## 9️⃣ Pod Using PVC Example

```yaml
volumes:
- name: data
  persistentVolumeClaim:
    claimName: app-pvc

containers:
- name: app
  volumeMounts:
  - mountPath: /data
    name: data
```

---

## 🔐 10️⃣ Access Modes Explained

| Mode          | Meaning             |
| ------------- | ------------------- |
| ReadWriteOnce | Mounted by one node |
| ReadOnlyMany  | Read-only by many   |
| ReadWriteMany | Read-write by many  |

---

## 🏭 11️⃣ Real Production Example (EKS)

* StorageClass: gp3
* PVC per microservice
* StatefulSets for DBs
* Dynamic provisioning enabled

Example:

```
user-service → PVC → EBS volume
order-db → PVC → EBS volume
```

---

## 12️⃣ Production Best Practices

### ✅ 1. Always use Dynamic Provisioning

Avoid static PVs.

---

### ✅ 2. Use StorageClass per workload

* gp3 → general
* io2 → databases

---

### ✅ 3. Use StatefulSets for stateful apps

Ensures stable storage.

---

### ✅ 4. Never use emptyDir for databases

Data loss guaranteed.

---

### ✅ 5. Use volumeBindingMode: WaitForFirstConsumer

Prevents wrong AZ placement.

---

### ✅ 6. Backup volumes regularly

Use snapshots.

---

### ✅ 7. Set reclaimPolicy carefully

* Delete → dev
* Retain → prod databases

---

### ✅ 8. Monitor disk usage

Avoid disk-full crashes.

---

## 13️⃣ Common Production Mistakes

❌ Using hostPath in production
❌ Manual PV management
❌ No backups
❌ Same PVC shared across apps

---

