# Kubernetes Deployment Strategies – Blue/Green, Canary, Rolling & Production Use Cases
---
# 1. Why Deployment Strategies?

In production, you **never directly replace** old pods with new pods.
You need controlled, safe, zero‑downtime releases.

Deployment strategies help you:

* Release new versions safely
* Minimize downtime
* Test new versions gradually
* Roll back quickly if something breaks

---

# 2. Rolling Update (Default Kubernetes Strategy)

### What is it?

Kubernetes gradually replaces old pods with new pods **one-by-one**.

### How it works

```
v1 pod → terminate
v2 pod → create
(repeat)
```

### Advantages

* Zero downtime
* Built-in, no special setup needed
* Easy rollback

### Disadvantages

* Old and new versions run together
* Hard to test new version separately

### Production Use Case

* Regular application updates
* API, backend, web apps

---

# 3. Blue/Green Deployment

### What is it?

You run **two full environments**:

* **Blue** = current version
* **Green** = new version

You test the Green version.
If everything is good → switch traffic to Green.

### Flow

```
Blue (v1) ← live traffic
Green (v2) ← tested only
↓ switch traffic
Green (v2) becomes live
Blue kept for rollback
```

### Advantages

* Zero downtime
* Very safe rollbacks (switch back to blue)
* Full environment testing before release

### Disadvantages

* Needs double resources (two environments)
* More expensive

### Production Use Case

* Banking apps
* Payment gateways
* Healthcare systems
* Critical apps where downtime is unacceptable

---

# 4. Canary Deployment

### What is it?

You send a **small percentage of traffic** to the new version first.

If no issues → increase traffic gradually.

### Flow

```
90% → v1
10% → v2  (canary)
↓ if stable
50% → v2
↓ if stable
100% → v2
```

### Advantages

* Very safe testing with real traffic
* Minimal risk
* Great for microservices

### Disadvantages

* Needs traffic routing controller (Istio, NGINX, Envoy, Argo Rollouts)
* More complex setup

### Production Use Case

* High‑traffic apps
* Large microservice architectures
* SaaS & e‑commerce platforms

---

# 5. A/B Testing Deployment

### What is it?

Two versions run in parallel, but routed based on:

* User type
* Region
* Cookie
* Header
* Device

### Example

```
Users from India → v1
Users from US → v2
```

### Advantages

* Real-world testing with targeted users

### Disadvantages

* Complex routing logic

### Production Use Case

* Frontend experiments
* Feature flag systems (LaunchDarkly, Unleash)

---

# 6. Production Examples

### ⭐ E‑commerce Website

* Canary for new checkout API
* Blue/Green for major payment gateway changes

### ⭐ Banking Application

* Always Blue/Green
* Canary for new feature rollout

### ⭐ SaaS Platform

* Rolling updates for daily updates
* Canary for risky changes

### ⭐ Microservices Architecture

* Every service uses Canary or Rolling update
* Istio or Argo Rollouts used for traffic splitting

---

# 10. Tools Used in Production

* **Argo Rollouts** → Canary, Blue/Green
* **Istio / Linkerd** → Traffic splitting
* **NGINX Ingress** → Simple canary routing
* **Service Mesh** → Advanced deployment strategies

---
