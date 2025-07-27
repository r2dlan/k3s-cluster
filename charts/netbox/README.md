# 🧠 NetBox – Helmfile Deployment Guide

This document describes how to deploy [NetBox](https://github.com/netbox-community/netbox), a powerful infrastructure resource modeling (IPAM/DCIM) tool, using **Helmfile**.

The deployment is managed using the official Helm chart provided by NetBox Labs and is intended for GitOps-style setups with Helmfile.

---

## Chart Repository

Make sure the following Helm chart repository is included in your `helmfile.yaml`:

```yaml
repositories:
  - name: netbox
    url: https://charts.netbox.oss.netboxlabs.com/
```

---

## Helmfile Release Definition

Here’s the release configuration for NetBox:

```yaml
helmDefaults:
  timeout: 1200

releases:
  - name: netbox
    namespace: netbox
    chart: netbox/netbox
    version: 5.0.57
    wait: true
    atomic: true
    values:
      - values/netbox.yaml.gotmpl
```

- **Namespace:** `netbox` (should exist or will be created manually)
- **Chart Version:** `5.0.57`
- **Timeout:** 20 minutes for initial installation
- **Atomic:** Ensures rollback on failure
- **Values File:** Go-templated `values/netbox.yaml.gotmpl`

---

## Installation Steps

1. Add the Helm repository:

   ```bash
   helm repo add netbox https://charts.netbox.oss.netboxlabs.com/
   helm repo update
   ```

2. Deploy using Helmfile:

   ```bash
   helmfile apply -e <environment>
   ```

   Replace `<environment>` with the appropriate Helmfile environment (`dev`, `prod`, etc.).

---

## Configuration

All custom settings (database, ingress, secrets, redis, etc.) should be configured in:

```
values/netbox.yaml.gotmpl
```

This file allows advanced templating with environment-specific overrides.

---

## Accessing NetBox

Depending on your ingress or service type configuration, you can access NetBox via:

- **LoadBalancer:**  
  `http://<external-ip>:<port>`

- **Ingress (preferred):**  
  `https://netbox.<your-domain>`

Use the following to get the service details:

```bash
kubectl -n netbox get svc
```

---

## Post-Install Check

Verify deployment status with:

```bash
helm -n netbox status netbox
kubectl -n netbox get all
```
