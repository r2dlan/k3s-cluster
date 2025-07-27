# cert-manager – Helmfile Deployment Guide

This guide describes how to deploy [cert-manager](https://cert-manager.io/) using **Helmfile**.  
`cert-manager` is a powerful Kubernetes add-on to automate the management and issuance of TLS certificates.

---

## Helm Chart Repository

Ensure the Jetstack Helm repository is added in your `helmfile.yaml`:

```yaml
repositories:
  - name: jetstack
    url: https://charts.jetstack.io
```

---

## Helmfile Release Configuration

Below is the Helmfile configuration to install `cert-manager`:

```yaml
releases:
  - name: cert-manager
    namespace: cert-manager
    chart: jetstack/cert-manager
    version: v1.17.1
    wait: true
    atomic: true
```

- **Namespace:** `cert-manager`
- **Chart Version:** `v1.17.1`
- **Wait:** Ensures Helm waits for all resources to become ready
- **Atomic:** Ensures a full rollback if installation fails

---

## Installation Steps

1. Add the Jetstack Helm repository:

   ```bash
   helm repo add jetstack https://charts.jetstack.io
   helm repo update
   ```

2. Apply the Helmfile:

   ```bash
   helmfile apply -e <environment>
   ```

   Replace `<environment>` with your target environment (e.g., `dev`, `prod`).

---

## Verification

Check the cert-manager pods:

```bash
kubectl get pods -n cert-manager
```

Example output:

```
NAME                                       READY   STATUS    RESTARTS   AGE
cert-manager-65c974f967-n9m2l              1/1     Running   0          30s
cert-manager-cainjector-5cb48c5747-sz7t8   1/1     Running   0          30s
cert-manager-webhook-778d79cbb4-9jd4j      1/1     Running   0          30s
```
