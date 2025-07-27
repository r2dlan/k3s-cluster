# Prometheus Operator – Helmfile Deployment Guide

This document describes how to deploy the **Prometheus Operator** using [kube-prometheus-stack](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack) with **Helmfile**. This setup provides full monitoring capabilities including Prometheus, Alertmanager, and Grafana.

---

## Helm Chart Repository

Ensure the Prometheus Community Helm chart is added in your `helmfile.yaml`:

```yaml
repositories:
  - name: prometheus-community
    url: https://prometheus-community.github.io/helm-charts
```

---

## Helmfile Release Configuration

Here is the Helmfile release block to deploy Prometheus Operator:

```yaml
releases:
  - name: prometheus-operator
    namespace: prometheus-operator
    chart: prometheus-community/kube-prometheus-stack
    version: 70.3.0
    createNamespace: true
    wait: true
    atomic: true
    values:
      - values/prometheus.yaml.gotmpl
```

- **Namespace:** `prometheus-operator`
- **Chart:** kube-prometheus-stack from Prometheus Community
- **Version:** `70.3.0`
- **Atomic:** Ensures rollback on failure
- **Templated Values:** Uses Go templating via `prometheus.yaml.gotmpl`

---

## Installation Steps

1. Add the Helm repo:

   ```bash
   helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
   helm repo update
   ```

2. Deploy using Helmfile:

   ```bash
   helmfile apply -e <environment>
   ```

   Replace `<environment>` with the relevant Helmfile environment (`dev`, `prod`, etc.).

---

## Configuration

Customize your configuration in:

```
values/prometheus.yaml.gotmpl
```

Here you can define:

- Alertmanager configuration
- Prometheus scrape configs
- Grafana dashboards
- Storage and retention settings
- ServiceMonitors and PodMonitors

---

## Accessing the Stack

Depending on your service or ingress configuration:

- **Prometheus** UI:  
  `https://prometheus.<your-domain>`

```bash
kubectl -n prometheus-operator get svc
```

---

## ✅ Post-Install Check

Check status:

```bash
helm -n prometheus-operator status prometheus-operator
kubectl -n prometheus-operator get all
```
