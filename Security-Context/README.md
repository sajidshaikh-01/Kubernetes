<img width="1431" height="861" alt="image" src="https://github.com/user-attachments/assets/98336300-0040-41dd-9f95-f275e69dc9e4" />

# Kubernetes Security Context –
---
## 1. What is Security Context

A **Security Context** defines **security-related settings** for:

* Pods
* Containers

It controls **how a container runs at OS and kernel level**, such as:

* User and group IDs
* Linux capabilities
* Privilege escalation
* File system permissions

In short:

> Security Context decides **"who" a container is and "what" it is allowed to do**.

---

## 2. Why Security Context is Important

Without Security Context:

* Containers may run as root
* Any exploit can damage the host
* Privilege escalation becomes easy

With Security Context:

* Least privilege enforced
* Reduced blast radius
* Compliance with security standards

Production clusters **must always use Security Context**.

---

## 3. Where Security Context Can Be Defined

### Pod-level Security Context

* Applies to all containers in the pod
* Example: user, group, filesystem permissions

### Container-level Security Context

* Applies to a specific container
* Overrides pod-level settings

Priority rule:

> Container-level Security Context overrides Pod-level Security Context.

---

## 4. How Security Context Works Internally

1. Pod spec defines securityContext
2. kubelet reads security settings
3. Container runtime (containerd / CRI-O) applies them
4. Linux kernel enforces permissions using:

   * UID/GID
   * Capabilities
   * Seccomp
   * AppArmor

---

## 5. Important Security Context Fields (MUST KNOW)

### runAsUser

* UID the container runs as
* Avoid UID 0 (root)

### runAsGroup

* Primary GID of container process

### fsGroup

* Group ID for mounted volumes
* Used heavily for persistent storage

### runAsNonRoot

* Forces container to not run as root

---

## 6. Privilege and Capability Controls

### privileged

* Gives container full access to host
* Almost equivalent to root on node
* **Never allow in production**

### allowPrivilegeEscalation

* Controls whether a process can gain more privileges
* Set to false for security

### Linux Capabilities

* Fine-grained permissions instead of full root
* Examples:

  * NET_BIND_SERVICE
  * SYS_TIME

Best practice:

* Drop all capabilities
* Add only what is required

---

## 7. File System Security

### readOnlyRootFilesystem

* Makes root filesystem read-only
* Prevents malware from writing files

### Volume Permissions

* fsGroup ensures shared volume access

---

## 8. Seccomp and AppArmor (Advanced)

### Seccomp

* Filters allowed system calls
* Prevents kernel exploits

### AppArmor

* Mandatory access control
* Limits file, network, and capability usage

---

## 9. Example: Pod-Level Security Context

* Runs container as non-root user
* Uses specific UID/GID
* Applies fsGroup for volumes

---

## 10. Example: Container-Level Security Context

* Drops all Linux capabilities
* Prevents privilege escalation
* Enforces read-only filesystem

---

## 11. Security Context vs Pod Security Standards

Security Context:

* Applied per pod/container
* Fine-grained control

Pod Security Standards:

* Cluster-level enforcement
* Baseline / Restricted / Privileged

They are used **together in production**.

---

## 12. Security Context in Production (REAL USE CASES)

### Web Applications

* runAsNonRoot
* readOnlyRootFilesystem
* Drop all capabilities

### Databases

* fsGroup for volume access
* Non-root execution

### CI/CD Runners

* Strict capability control
* No privilege escalation

---

## 13. Common Mistakes (Interview Traps)

* Running containers as root
* Using privileged: true
* Forgetting fsGroup for volumes
* Relying only on Pod Security Standards

---

## 15. Interview Questions You Must Answer

* What is Security Context?
* Difference between pod-level and container-level security context?
* What happens if fsGroup is not set?
* Why privileged containers are dangerous?
* How Security Context works internally?

---

This README provides **production-level understanding** of Kubernetes Security Context and is fully aligned with **real DevOps and SRE practices**.
