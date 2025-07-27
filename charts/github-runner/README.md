# GitHub Actions Runner Controller – Helmfile Deployment Guide

This guide explains how to deploy the [GitHub Actions Runner Controller (ARC)](https://github.com/actions/actions-runner-controller) using **Helmfile**. The setup includes:

- The **Controller**, which manages scale sets
- The **Runner Scale Set**, which provisions actual GitHub self-hosted runners

---

## Helm Chart Source

Both charts are hosted on GitHub Container Registry (OCI):

- `gha-runner-scale-set-controller`
- `gha-runner-scale-set`

Ensure Helm v3.8+ is installed to support OCI-based charts.

---

## Helmfile Configuration

Here’s how your `helmfile.yaml` should define both components:

```yaml
releases:
  - name: gha-rs-controller
    namespace: gha-runner
    createNamespace: true
    chart: "oci://ghcr.io/actions/actions-runner-controller-charts/gha-runner-scale-set-controller"
    version: 0.12.1
    wait: true
    atomic: true
    values:
      - values/gha-rs-controller.yaml.gotmpl

  - name: "actions-runner"
    needs:
      - gha-rs-controller
    namespace: gha-runner
    chart: "oci://ghcr.io/actions/actions-runner-controller-charts/gha-runner-scale-set"
    version: 0.12.1
    wait: true
    atomic: true
    values:
      - values/gh-runner.yaml.gotmpl
```

---

## Installation Instructions

1. **Authenticate to the GitHub Container Registry (if needed):**

   ```bash
   export HELM_EXPERIMENTAL_OCI=1
   echo $CR_PAT | helm registry login ghcr.io -u <your-username> --password-stdin
   ```

2. **Run Helmfile Apply:**

   ```bash
   helmfile apply -e <environment>
   ```

   Replace `<environment>` with your desired environment.

---

## Configuration

Your configuration is defined in the templated values files:

- `values/gha-rs-controller.yaml.gotmpl`: Configures controller logic and cluster-level behavior
- `values/gh-runner.yaml.gotmpl`: Defines the runner scale set (labels, repo scopes, replica count, etc.)

Make sure your runner scale set has the correct GitHub token and repo access defined.

---

## Dependency Handling

The runner release includes:

```yaml
needs:
  - gha-rs-controller
```

This ensures that the controller is installed **before** the runner scale set is rendered and deployed.

If you experience issues with timing or CRDs not existing, consider deploying in two steps:

```bash
helmfile apply -l name=gha-rs-controller
helmfile apply -l name=actions-runner
```

---

## Verification

After deployment:

```bash
kubectl -n gha-runner get pods
kubectl -n gha-runner get runners
kubectl -n gha-runner get ghactionscalesets
```

You can also validate with GitHub under:
**Settings → Actions → Runners** for your org or repo.
