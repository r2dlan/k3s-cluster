# 🌐 NGINX Ingress Controller – Helmfile Deployment Guide

This guide explains how to deploy the [NGINX Ingress Controller](https://kubernetes.github.io/ingress-nginx/) using **Helmfile**. It enables routing external HTTP(S) traffic into your Kubernetes cluster.

---

## Helm Chart Repository

Ensure this Helm repository is configured in your `helmfile.yaml`:

```yaml
repositories:
  - name: ingress-nginx
    url: https://kubernetes.github.io/ingress-nginx
```

---

## Helmfile Release Configuration

Here's the Helmfile release block for deploying the ingress controller:

```yaml
releases:
  - name: nginx-ingress
    namespace: r2dlan-system
    chart: ingress-nginx/ingress-nginx
    version: 4.13.0
    createNamespace: true
    wait: true
    atomic: true
    values:
      - values/nginx.yaml.gotmpl
```

- **Namespace:** `r2dlan-system`
- **Chart Version:** `4.13.0`
- **Atomic Install:** Ensures clean rollback on failure
- **Templated Values:** Uses Go template file for customization

---

## Installation Steps

1. Add the repository:

   ```bash
   helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
   helm repo update
   ```

2. Deploy the release using Helmfile:

   ```bash
   helmfile apply -e <environment>
   ```

   Replace `<environment>` with your target environment (e.g., `dev`, `prod`).

---

## Configuration

Edit the templated values here:

```
values/nginx.yaml.gotmpl
```

You can configure:

- Controller service type (`LoadBalancer`, `NodePort`)
- Annotations for cloud-specific settings
- TLS settings and cert-manager integration
- Custom ports, logs, and security context

---

## Accessing Ingress Controller

The ingress controller exposes a service which typically becomes your main gateway into the cluster.

Check the external IP:

```bash
kubectl -n r2dlan-system get svc nginx-ingress-ingress-nginx-controller
```

Ingress rules defined in other apps will use this controller to route traffic.

---

## Post-Install Verification

```bash
helm -n r2dlan-system status nginx-ingress
kubectl -n r2dlan-system get all
```
