# CLAUDE.md — keyvoro Config

GitOps deployment configuration for **keyvoro**. ArgoCD watches this repo.

## Rules

1. **DO NOT edit image tags manually.** CI updates them via `kustomize edit set image` after each build.
2. **NO branch-per-environment.** Use `overlays/production/` and `overlays/staging/` directories.
3. **Every change requires PR review.** CODEOWNERS enforces this.
4. **Secrets go in Vault**, referenced via ExternalSecret in `vault.yaml`. Never hardcode.

## Structure

```
services/keyvoro/
├── base/           # Shared manifests (Deployment, Service, Route, etc.)
└── overlays/
    ├── production/ # Production kustomize patches + image tag
    └── staging/    # Staging kustomize patches + image tag
```

## How Changes Flow

| Change Type | Where | Trigger |
|------------|-------|---------|
| Code change | app-keyvoro repo | CI builds image → updates tag here → ArgoCD syncs |
| Config change | This repo (PR) | ArgoCD auto-syncs on merge |
| Secret change | Vault | ExternalSecret operator refreshes on interval |

## Related

- **App code:** [app-keyvoro](https://github.com/Adroiteck-PS/app-keyvoro)
- **CI template:** `central-workflow/templates/ci-pipeline.yml`
- **Scaffolding:** `central-workflow/scripts/new-app-config.sh`
