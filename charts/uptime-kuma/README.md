# Uptime Kuma – Helmfile Deployment

This document explains how to deploy [Uptime Kuma](https://github.com/louislam/uptime-kuma) – a self-hosted monitoring tool – using **Helmfile**.

The deployment is configured via the [uptime-kuma Helm chart](https://github.com/dirsigler/uptime-kuma-helm) and uses a declarative setup suitable for GitOps workflows.

---

## Chart Source

The Helm chart used is hosted at:

```yaml
repositories:
  - name: uptime-kuma
    url: https://dirsigler.github.io/uptime-kuma-helm
```

Make sure your `helmfile.yaml` includes this repository definition.

---

## Release Configuration

Uptime Kuma is deployed as follows:

```yaml
releases:
  - name: uptime-kuma
    namespace: uptime-kuma
    chart: uptime-kuma/uptime-kuma
    version: 2.21.3
    createNamespace: true
    wait: true
    atomic: true
    values:
      - values/kuma.yaml.gotmpl
```

- **Namespace:** `uptime-kuma` (created if it doesn't exist)
- **Chart Version:** `2.21.3`
- **Atomic Install:** Rollbacks on failure
- **Templated values file:** Supports `gotmpl` templating via Helmfile

---

## Installation

1. Add the repo (if not already defined globally):

   ```bash
   helm repo add uptime-kuma https://dirsigler.github.io/uptime-kuma-helm
   helm repo update
   ```

2. Apply the Helmfile:

   ```bash
   helmfile apply -e <environment>
   ```

   Replace `<environment>` with the name of your Helmfile environment (e.g., `dev`, `prod`).

---

## Configuration

Edit the values in:

```
values/kuma.yaml.gotmpl
```

Typical settings include:

- Service type (`LoadBalancer`, `ClusterIP`, etc.)
- Persistent storage (PVC)
- Port customization
- Ingress configuration

---

## 📍 Accessing Uptime Kuma

After installation, access Uptime Kuma based on your service or ingress settings:

- If using LoadBalancer:  
  `http://<external-ip>:<port>`
- If using Ingress:  
  `https://uptime.<your-domain>`

You can get the service IP using:

```bash
kubectl -n uptime-kuma get svc
```

---

## Status Check

To verify the release:

```bash
helm -n uptime-kuma status uptime-kuma
kubectl -n uptime-kuma get all
```
