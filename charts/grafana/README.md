# 📊 Grafana – Helmfile Deployment Guide

This document outlines how to deploy [Grafana](https://grafana.com/) using **Helmfile**. Grafana is an open-source analytics and monitoring platform, commonly used to visualize time-series data such as system metrics and logs.

---

## Helm Chart Repository

Ensure the Grafana Helm chart is added to your `helmfile.yaml`:

```yaml
repositories:
  - name: grafana
    url: https://grafana.github.io/helm-charts
```

---

## Helmfile Release Configuration

Below is the release configuration for deploying Grafana:

```yaml
releases:
  - name: grafana
    namespace: grafana
    chart: grafana/grafana
    version: 9.3.0
    wait: true
    atomic: true
    values:
      - values/grafana.yaml.gotmpl
```

- **Namespace:** `grafana` (created if necessary)
- **Chart Version:** `9.3.0`
- **Wait:** Ensures Helm waits until deployment is complete
- **Atomic:** Rolls back on failure
- **Values File:** Go-templated config in `values/grafana.yaml.gotmpl`

---

## Installation Steps

1. Add the Helm repository:

   ```bash
   helm repo add grafana https://grafana.github.io/helm-charts
   helm repo update
   ```

2. Deploy using Helmfile:

   ```bash
   helmfile apply -e <environment>
   ```

   Replace `<environment>` with the appropriate Helmfile environment (e.g. `dev`, `prod`).

---

## Configuration

Customize your deployment in:

```
values/grafana.yaml.gotmpl
```

Settings may include:

- Admin credentials
- Datasource configuration
- Dashboards as config
- Ingress and service setup
- PVCs or emptyDir for persistence

---

## Accessing Grafana

Depending on your service type:

- **LoadBalancer:**  
  `http://<external-ip>:<port>`

- **Ingress (preferred):**  
  `https://grafana.<your-domain>`

Default login is typically:

- **Username:** `admin`
- **Password:** retrieved via:

  ```bash
  kubectl get secret --namespace grafana grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
  ```

---

## Post-Install Check

Verify the deployment with:

```bash
helm -n grafana status grafana
kubectl -n grafana get all
```
