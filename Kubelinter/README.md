# KubeLinter – 

## 1. What is KubeLinter?

**KubeLinter** is an open-source **static analysis (linting) tool** for Kubernetes YAML manifests and Helm charts.

It scans your Kubernetes configuration files to detect **misconfigurations, security risks, and best‑practice violations** *before* you deploy them to the cluster.

Developed by **StackRox / Red Hat**, it is widely used in CI/CD pipelines.

---

## 2. Why Use KubeLinter?

Kubernetes YAML files are powerful but easy to misconfigure.
Examples:

* Containers running as root
* Missing resource limits
* Missing liveness/readiness probes
* Insecure capabilities
* Use of `latest` image tags

KubeLinter helps you catch such issues early.

### Benefits:

* Prevents production failures
* Improves security posture
* Enforces best practices (DevOps/SRE)
* Fast CLI tool
* Works well in CI/CD (GitHub Actions, Jenkins, GitLab CI)

---

## 3. How KubeLinter Works

KubeLinter reads:

* Kubernetes YAML files
* Helm charts (values + templates)

Then it compares them against a set of **built-in checks**.

### Flow:

```
KubeLinter → Parse YAML → Run checks → Output warnings/errors
```

If a problem is found, KubeLinter reports:

* The file name
* The object name
* The violated rule/check
* Details on how to fix it

---

## 4. Installing KubeLinter

### macOS/Linux

```
curl -s https://raw.githubusercontent.com/stackrox/kube-linter/main/install.sh | bash
```

### Homebrew

```
brew install kubelinter
```

### Windows

Use chocolatey or scoop:

```
scoop install kubelinter
```

---

## 5. Basic Usage

### Scan YAML manifests

```
kube-linter lint ./manifests
```

### Scan a Helm chart

```
kube-linter lint ./chart
```

### Output as JSON

```
kube-linter lint ./manifests --format json
```

---

## 6. Common Issues Detected by KubeLinter

KubeLinter looks for:

### **Security Issues**

* Containers running as root
* Privileged mode enabled
* Dangerous capabilities
* Insecure host path mounts

### **Best Practices**

* Missing resource limits
* Missing liveness/readiness probes
* Use of `latest` image tag
* Containers without `securityContext`

### **Helm Chart Issues**

* Not using recommended labels
* Missing namespace specifications
* Invalid values

---

## 7. KubeLinter Checks (Popular Built-In Checks)

| Check                       | Description                           |
| --------------------------- | ------------------------------------- |
| `run-as-non-root`           | Ensures containers do not run as root |
| `no-liveness-probe`         | Pod must define a liveness probe      |
| `no-readiness-probe`        | Pod must define a readiness probe     |
| `unset-memory-requirements` | Requires memory limits                |
| `unset-cpu-requirements`    | Requires CPU limits                   |
| `no-privileged`             | Privileged mode should be disabled    |
| `latest-tag`                | Forbids using `latest` image tag      |

---

## 8. Custom Checks

You can write your own custom rules using a `config.yaml`.
Example:

```yaml
checks:
  include:
    - run-as-non-root
    - no-liveness-probe
```

Run with:

```
kube-linter lint -c config.yaml manifests/
```

---

## 9. Using KubeLinter in CI/CD

Most companies run KubeLinter as part of CI to block bad YAML.

### Example GitHub Action

```yaml
- name: Run KubeLinter
  uses: stackrox/kube-linter-action@v1.0.0
  with:
    path: ./manifests
```

### Workflow

```
Push YAML/Helm → CI runs KubeLinter → Fail if violations → Fix → Deploy
```

---

## 10. When DevOps Teams Use KubeLinter

* Before merging Kubernetes YAML in Git repositories
* During Helm chart development
* In CI to validate every PR
* To enforce organizational best practices
* As part of GitOps workflow (ArgoCD/Flux)

---

