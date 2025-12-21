# TLS Certificates, Why We Secure Websites, What Happens Without TLS, CA, Cert Manager & Certificate Creation Process

This README explains:

* What TLS certificates are
* Why websites need TLS/HTTPS
* What happens if you don’t use TLS
* How Certificate Authorities (CA) work
* How TLS certificate creation works internally
* How cert-manager automates certificates in Kubernetes

---

# 1. What Is a TLS Certificate?

A **TLS certificate** is a digital document that:

* Proves the identity of a website
* Encrypts communication between user and server
* Prevents hackers from reading or modifying data

TLS = Transport Layer Security
HTTPS = HTTP + TLS

### Simple Explanation

> TLS certificate is like an ID card for your website that also locks your data.

---

# 2. Why Do We Secure Websites Using TLS?

## ✔ 1. To Encrypt Data

Without TLS, all data travels in **plain text**.
Anyone on the network can read:

* Passwords
* Credit card details
* Tokens
* Cookies
* Personal data

## ✔ 2. To Prove Website Identity

TLS confirms your website is real and not a fake phishing site.

## ✔ 3. To Prevent Man-in-the-Middle (MITM) Attacks

Without TLS, hackers can:

* Inject malware
* Alter responses
* Steal user data

## ✔ 4. Required for SEO and Modern Browsers

Chrome, Firefox, Safari show:

* “Not Secure” warning
* Block some sites entirely

---

# 3. What Happens If You Don’t Use TLS?

## ❌ Data Is Not Encrypted

Hackers on the same WiFi can steal:

* Login credentials
* Payment details
* Session cookies

## ❌ Website Can Be Spoofed or Hijacked

MITM attack:

* Fake website injected
* Users think it’s real
* Hackers steal everything

## ❌ Browsers Will Warn Users

Chrome shows this red warning:

```
Your connection is not private
Attackers might be trying to steal your information.
```

## ❌ API Calls Will Be Blocked

Modern browsers block insecure API calls.

## ❌ You Cannot Use OAuth, JWT, Cookies Safely

Many auth systems *require TLS*.

---

# 4. How TLS Certificate Works Internally

### Step 1: Browser connects to website (`https://example.com`)

### Step 2: Server sends its TLS certificate

### Step 3: Browser checks certificate using **Certificate Authority (CA)** trust list

### Step 4: If valid → Browser creates an encrypted session key

### Step 5: All communication becomes encrypted

This process is called the **TLS handshake**.

---

# 5. What Is a Certificate Authority (CA)?

A **Certificate Authority (CA)** is a trusted organization that issues digital certificates.
Examples:

* Let’s Encrypt
* DigiCert
* GoDaddy
* GlobalSign
* AWS Certificate Manager

### Why CA is required?

Because only a trusted CA can sign certificates that browsers will trust.

If you create a certificate yourself (self-signed):

* Browsers will show warnings
* Users must manually trust it

---

# 6. Types of TLS Certificates

| Type                             | Usage                                        |
| -------------------------------- | -------------------------------------------- |
| **DV (Domain Validation)**       | Basic websites, auto-issued by Let's Encrypt |
| **OV (Organization Validation)** | Business identity verified                   |
| **EV (Extended Validation)**     | High-security websites, banks                |
| **Wildcard**                     | `*.example.com`                              |
| **SAN Certificates**             | Multiple domains                             |

---

# 7. How TLS Certificate Is Created (Full Process)

## Step 1: You generate a private key

```
server.key
```

This must be kept secret.

## Step 2: You generate a CSR (Certificate Signing Request)

```
server.csr
```

CSR contains:

* Domain name
* Public key
* Company name (for OV/EV)

## Step 3: You send CSR to CA (like Let’s Encrypt)

CA verifies:

* Are you the real owner of the domain?

Verification methods:

* DNS challenge
* HTTP challenge (cert-manager uses this)
* Email verification

## Step 4: CA creates and signs certificate

You receive:

```
server.crt  (certificate)
ca.crt       (CA certificate)
```

## Step 5: Website serves the certificate

When user connects:

* Browser validates the signature using CA root store
* If valid → secure lock icon shows

---

# 8. How We Secure Websites Using Cert-Manager in Kubernetes

Cert-manager automates:

* Certificate request
* Domain ownership verification
* Renewal
* Storage in Kubernetes secrets

### Cert-manager works with:

* Let’s Encrypt (Free certificates)
* Private CA
* AWS PCA
* Venafi

### How it works

```
Ingress → cert-manager → Let’s Encrypt CA → TLS secret → Ingress uses secret
```

---

# 9. Cert-Manager Architecture

### ✔ 1. You install cert-manager in K8s

Creates pods:

* cert-manager
* cert-manager-webhook
* cert-manager-cainjector

### ✔ 2. You create an Issuer or ClusterIssuer

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: admin@example.com
    privateKeySecretRef:
      name: acme-key
    solvers:
    - http01:
        ingress:
          class: nginx
```

### ✔ 3. Cert-manager creates HTTP challenge pod

Example:

```
http://example.com/.well-known/acme-challenge/12345
```

CA verifies domain.

### ✔ 4. Cert-manager obtains certificate

Stores it as a secret:

```
example-tls
```

### ✔ 5. Ingress uses the secret

```yaml
spec:
  tls:
  - hosts:
    - example.com
    secretName: example-tls
```

Your website is now HTTPS-enabled automatically.

---

# 10. What Happens When Certificate Is About to Expire?

Cert-manager automatically:

* Renews certificate
* Updates secret
* Reloads NGINX Ingress

No downtime.



