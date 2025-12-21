# Kubernetes Service: **port** vs **targetPort** 
---
# 1. What Is a Service Port?

In Kubernetes Service, the **port** is the port number *exposed by the Service itself*.

This is the port your clients (other pods, ingress, users) will connect to.

### Example

```yaml
port: 80
```

This means:

> "The Service listens on port 80 inside the cluster."

---

# 2. What Is targetPort?

**targetPort** is the port inside the **Pod's container** where the actual application is running.

It tells the Service:

> "Inside the pod, send traffic to this container port."

### Example

```yaml
targetPort: 8080
```

Means the application inside the container listens on port **8080**.

---

# 3. Simple Diagram

```
Client → Service:80 → Pod container:8080
```

* Service receives request on port **80**
* Forwards to Pod's container on **8080**

---

# 4. Complete Example

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: ClusterIP
  selector:
    app: myapp
  ports:
    - port: 80        # Service's port
      targetPort: 8080  # Container's port
```

### Inside the pod (container runs on 8080):

```yaml
containers:
  - name: myapp
    image: nginx
    ports:
      - containerPort: 8080
```

---

# 5. Why Do We Need Both?

Because sometimes:

* Your **service** exposes one port
* Your **container** listens on a different port

Example:

* frontend talks to `service:80`
* container runs on `app:8080`

Service maps port 80 → 8080.

### Analogy

Like calling a phone number **(service port)** that forwards to the actual person’s extension **(targetPort)**.

---

# 6. What If port and targetPort Are the Same?

Example:

```yaml
port: 80
targetPort: 80
```

This is common for NGINX, Apache, NodePort, etc.

This means:

* Service listens on 80
* Pod's container also listens on 80

Nothing wrong with this.

---

# 7. Common Use Cases

## ✔ Different port for container and service

Container runs on 5000 but you want service on port 80.

```yaml
port: 80
targetPort: 5000
```

This makes it easy for other apps to call the service.

## ✔ Same port in both

```yaml
port: 443
targetPort: 443
```

Used for TLS apps.

## ✔ When using NodePort or LoadBalancer

Example:

```yaml
port: 80
nodePort: 30080
targetPort: 8080
```

Meaning:

* User hits Node:30080
* NodePort forwards to Service:80
* Service forwards to Pod:8080

---

# 8. Production Example

Backend service:

* Internally exposed on `port 80`
* Container actually runs on `port 8080`
* Other microservices communicate using `backend:80`

This keeps things consistent even if backend team changes internal container port.



\
