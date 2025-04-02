# Cosmos Validator Deployment Guide

## Overview
This guide provides step-by-step instructions to deploy a **Cosmos Validator** in a Kubernetes cluster using Helm. It includes:
- **Validator Node Deployment**
- **Horcrux for Key Management**
- **Security Best Practices**
- **Monitoring Setup**

---

## 1️⃣ Prerequisites
Before proceeding, ensure you have:
- A Kubernetes cluster running (EKS, GKE, AKS, or a local cluster)
- `kubectl` and `helm` installed
- A configured KMS or Kubernetes Secret management solution
- Persistent storage available for validator state

---

## 2️⃣ Deployment Steps

### Step 1: Clone the Repository
```bash
git clone https://github.com/Amcalstrings/Cosmos-Validator-Deployment.git
cd Cosmos-Validator-Deployment
```

### Step 2: Customize Values
Edit `values.yaml` to configure the validator:
```yaml
validator:
  chainId: "cosmoshub-4"
  moniker: "my-validator"
  privateKey: "<BASE64_ENCODED_PRIVATE_KEY>"

horcrux:
  signingKey: "<BASE64_ENCODED_SIGNING_KEY>"
```

### Step 3: Deploy the Helm Chart
```bash
helm install cosmos-validator .
```

### Step 4: Verify Deployment
```bash
kubectl get pods -l app=cosmos-validator
kubectl get pods -l app=horcrux
```
Check logs:
```bash
kubectl logs -l app=cosmos-validator
```

### Step 5: Expose API (Optional)
If required, expose RPC API for external queries:
```bash
kubectl port-forward svc/cosmos-validator 26657:26657
```

---

## 3️⃣ Security & Monitoring Integrations

### Security Enhancements
- **Kubernetes Secrets**: Private keys are stored securely and mounted as environment variables.
- **Network Policies**: Restrict communication between validator and Horcrux signer.
- **RBAC**: Limit access to validator pods and secrets.
- **Non-root Execution**: Pods run as non-root users for security.

### Monitoring Setup
- **Liveness & Readiness Probes**: Ensures validator health.
- **Prometheus & Grafana**:
  - Install Prometheus Operator:
    ```bash
    helm install prometheus prometheus-community/kube-prometheus-stack
    ```
  - Add Grafana dashboard for Cosmos metrics.

---

## 4️⃣ Horcrux Key Management
### Step 1: Initialize Horcrux
```bash
horcrux init --home ~/.horcrux
```
### Step 2: Verify Validator Key is Stored in Kubernetes
Horcrux keys are securely stored in a Kubernetes Secret and automatically injected into the Horcrux container. You can verify the key is correctly stored by running:
```bash
kubectl get secret horcrux-secret -o yaml
```
### Step 3: Start Horcrux
```bash
horcrux start --home ~/.horcrux
```

---

## Cleanup (Optional)
To remove the deployment:
```bash
helm uninstall cosmos-validator
```

---

## Conclusion
This setup ensures a **secure, highly available** Cosmos Validator deployment. 

