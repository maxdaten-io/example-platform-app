# Example Platform App

Example downstream application for testing the [platform onboarding workflow](https://github.com/maxdaten-io/gitops/blob/main/docs/howto-project-onboarding.md).

## Purpose

This repository demonstrates:

- Kubernetes manifest structure for platform apps
- OCI artifact publishing via GitHub Actions
- Integration with the GitOps platform

## Structure

```
k8s/
├── kustomization.yaml    # Root kustomization (required)
├── deployment.yaml       # Example deployment
├── service.yaml          # Example service
└── configmap.yaml        # Example configmap
```

The `k8s/` directory is published as an OCI artifact and automatically reconciled by Flux in the project namespace.

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
