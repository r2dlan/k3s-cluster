# InfluxDB 2 – Helmfile Deployment Guide

This document explains how to deploy **InfluxDB 2** using **Helmfile**, ideal for time-series storage of metrics, logs, and events.

---

## Helm Chart Repository

Add the official InfluxData Helm repository in your `helmfile.yaml`:

```yaml
repositories:
  - name: influxdata
    url: https://helm.influxdata.com/
```

---

## Helmfile Release Configuration

The following Helmfile configuration deploys InfluxDB 2:

```yaml
releases:
  - name: influxdata
    namespace: databases
    chart: influxdata/influxdb2
    version: 2.1.2
    createNamespace: true
    wait: true
    atomic: true
    values:
      - values/influxdb.yaml.gotmpl
```

- **Namespace:** `databases`
- **Chart Version:** `2.1.2`
- **Create Namespace:** Automatically creates it if missing
- **Atomic:** Ensures rollback on failed installs
- **Templated Values:** Uses Go templating via `influxdb.yaml.gotmpl`

---

## Installation Steps

1. Add the Helm repo:

   ```bash
   helm repo add influxdata https://helm.influxdata.com/
   helm repo update
   ```

2. Apply with Helmfile:

   ```bash
   helmfile apply -e <environment>
   ```

   Replace `<environment>` with your target environment like `dev` or `prod`.

---

## Configuration

Edit the values in:

```
values/influxdb.yaml.gotmpl
```

You can configure:

- Admin user and password
- Initial bucket and organization
- Retention policies
- Storage and persistence
- Ingress, TLS, and service exposure

---

## Accessing InfluxDB

Typical service endpoints:

- **Web UI:**  
  `https://influxdb.<your-domain>` or via LoadBalancer IP

- **CLI & API:**  
  `http://<influxdb-service>:8086`

You can retrieve the password like this:

```bash
kubectl -n databases get secret influxdata-auth -o jsonpath="{.data.admin-password}" | base64 --decode
```

---

## Post-Install Check

Verify the deployment:

```bash
helm -n databases status influxdata
kubectl -n databases get all
```
