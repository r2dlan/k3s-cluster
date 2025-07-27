# MetalLB – Helmfile Deployment Guide

This guide describes how to deploy [MetalLB](https://metallb.universe.tf/), a load-balancer implementation for bare-metal Kubernetes clusters, using **Helmfile**.

MetalLB provides support for `LoadBalancer` services in clusters that do not run in a cloud environment.

---

## Helm Chart Repository

Ensure the MetalLB Helm chart is declared in your `helmfile.yaml`:

```yaml
repositories:
  - name: metallb
    url: https://metallb.github.io/metallb
```

---

## Helmfile Release Configuration

Here is the Helmfile release configuration used for deploying MetalLB:

```yaml
releases:
  - name: metallb
    namespace: r2dlan-system
    chart: metallb/metallb
    version: 0.14.9
    wait: true
    atomic: true
```

- **Namespace:** `r2dlan-system`
- **Chart Version:** `0.14.9`
- **Wait:** Ensures Helm waits for all resources to become ready
- **Atomic:** Rolls back changes if the deployment fails

---

## Installation Steps

1. Add the MetalLB Helm repository:

   ```bash
   helm repo add metallb https://metallb.github.io/metallb
   helm repo update
   ```

2. Deploy with Helmfile:

   ```bash
   helmfile apply -e <environment>
   ```

   Replace `<environment>` with your environment name (`dev`, `prod`, etc.).

---

## ✅ Post-Install Verification

```bash
kubectl -n r2dlan-system get pods
kubectl -n r2dlan-system get all
```

Check MetalLB logs if troubleshooting:

```bash
kubectl -n r2dlan-system logs deploy/controller
```
