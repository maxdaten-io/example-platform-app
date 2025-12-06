# Example Platform App

Example downstream application for testing the [platform onboarding workflow](https://github.com/maxdaten-io/gitops/blob/main/docs/howto-project-onboarding.md).

## Purpose

This repository demonstrates:

- Kubernetes manifest structure for platform apps
- OCI artifact publishing via GitHub Actions
- Integration with the GitOps platform
- External Secrets Operator (ESO) integration with GCP Secret Manager

## Structure

```
k8s/
├── kustomization.yaml    # Root kustomization (required)
├── deployment.yaml       # Example deployment with secret display
├── service.yaml          # Example service
├── configmap.yaml        # Example configmap
└── external-secret.yaml  # ESO ExternalSecret for GCP secrets
```

The `k8s/` directory is published as an OCI artifact and automatically reconciled by Flux in the project namespace.

## Secrets Integration

This example demonstrates ESO integration with GCP Secret Manager:

1. **ExternalSecret** (`external-secret.yaml`) fetches `demo-message` from GCP Secret Manager
2. **Deployment** uses an initContainer to inject the secret into the served HTML
3. **SecretStore** (`gcp-secrets`) is automatically created by the platform ResourceSet

### Prerequisites

Enable secrets for your app in `apps.yaml`:
```yaml
example-app:
  enable_secrets: true  # Creates ESO SA + SecretStore
```

Create the secret in GCP:
```bash
gcloud secrets create demo-message --project=<project-id>
echo -n "Hello from GCP!" | gcloud secrets versions add demo-message --data-file=-
```

## Resource Requirements

Platform namespaces have ResourceQuotas enforced. All containers **must** specify both CPU and memory limits:

```yaml
resources:
  requests:
    cpu: 10m
    memory: 32Mi
  limits:
    cpu: 100m      # Required by ResourceQuota
    memory: 64Mi   # Required by ResourceQuota
```

Without `limits.cpu` and `limits.memory`, pods will fail to schedule with:
```
failed quota: small-app-quota: must specify limits.cpu
```

See [Flux CD Workflow](https://github.com/maxdaten-io/gitops/blob/main/docs/agent/kubernetes-flux-workflow.md#resource-quotas) for quota limits.

## How It Works

1. **GitHub Actions** publishes `k8s/` to Artifact Registry as an OCI artifact
2. **OCIRepository** (in project namespace) pulls the artifact
3. **Kustomization** (in project namespace) applies manifests to the namespace

All resources are automatically deployed to your project's dedicated namespace.

## Usage

1. Add this project to `foundation/05-projects/platform-apps/apps.yaml`
2. Apply Terragrunt to provision GCP resources
3. Configure GitHub secrets: `just app-secrets <app-name>`
4. Push to main branch to trigger OCI artifact publish

See [Project Onboarding Guide](https://github.com/maxdaten-io/gitops/blob/main/docs/howto-project-onboarding.md) for details.
