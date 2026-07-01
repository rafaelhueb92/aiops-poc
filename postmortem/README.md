# Postmortem Reports

This folder contains postmortem reports documenting incidents, errors, and issues encountered during the CI/CD pipeline and deployment process.

## Structure

- `template.md` - Template for creating new postmortem reports
- `YYYY-MM-DD_incident_name/` - Individual incident folders with detailed analysis

## Guidelines

When an incident occurs:
1. Create a new folder with the date and incident name: `YYYY-MM-DD_incident_description`
2. Use the template.md as a starting point
3. Document the timeline, root cause, and remediation steps
4. Include logs, error messages, and screenshots when applicable
5. Reference any related GitHub issues or PRs

## Quick Links

- GitHub Actions Workflow: `.github/workflows/build-and-deploy.yml`
- Kubernetes Manifests: `./k8s/manifest.yaml`
- Application Source: `./src/`

## Common Issues & Solutions

See individual postmortem folders for specific incident details.
