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
├── kustomization.yaml    # Root kustomization
├── deployment.yaml       # Example deployment
├── service.yaml          # Example service
└── configmap.yaml        # Example configmap
```

## Usage

1. Add this project to `foundation/05-projects/platform-apps/projects.yaml`
2. Apply Terragrunt to provision GCP resources
3. Configure GitHub secrets (from Terraform outputs)
4. Push to main branch to trigger OCI artifact publish

See [Project Onboarding Guide](https://github.com/maxdaten-io/gitops/blob/main/docs/howto-project-onboarding.md) for details.
