# Crossplane vCluster Platform Workflow

## Purpose
This repository is organized by responsibility domains and synced by Argo CD using a platform app-of-apps model.

Top-level domains:
- `platform-infra/`
- `platform-security/`
- `platform-delivery/`
- `apps/`
- `environments-config/`
- `docs/`

## Argo CD Wiring
1. Root app: `environments.yaml`
- Syncs `environments/envs` into `argo-cd`.

2. Platform apps from `environments/envs/`
- `platform-infra.yaml` (sync-wave `1`)
- `platform-security.yaml` (sync-wave `2`)
- `platform-delivery.yaml` (sync-wave `3`)

3. Delivery app (`platform-delivery/`)
- Deploys ApplicationSets from `platform-delivery/argocd/appsets/`:
  - `env-management-components`
  - `env-green-apps`
  - `env-yellow-apps`

4. ApplicationSets generate env-specific Applications
- Management components from `platform-security/identity/*`
- Green and Yellow apps from `apps/*/*/k8s`

## Repository Structure
```text
.
├── environments.yaml
├── environments/
│   └── envs/
│       ├── platform-infra.yaml
│       ├── platform-security.yaml
│       └── platform-delivery.yaml
├── platform-infra/
│   ├── kustomization.yaml
│   ├── clusters/{management,green,yellow}/
│   ├── network/
│   ├── dns/
│   ├── ingress/
│   ├── storage/
│   └── observability/
├── platform-security/
│   ├── kustomization.yaml
│   ├── identity/keycloak/
│   ├── policies/kyverno/
│   └── rbac/{baseline,exceptions}/
├── platform-delivery/
│   ├── kustomization.yaml
│   ├── argocd/{bootstrap,projects,appsets}/
│   ├── templates/{service-helm,oidc,ingress-auth}/
│   └── pipelines/reusable/
├── apps/
│   └── <team>/<service>/
│       ├── src/
│       ├── k8s/
│       └── deploy/
│           ├── values-dev.yaml
│           ├── values-green.yaml
│           └── values-yellow.yaml
├── environments-config/
│   ├── green/services/
│   └── yellow/services/
└── docs/
    ├── RACI.md
    └── DEPLOYMENT_STRATEGY.md
```

## Ownership Boundaries
- Infra: cluster lifecycle, networking, ingress, storage, DNS, observability (`platform-infra/`)
- Security: Keycloak/OIDC standards, policy bundles, RBAC baseline and exceptions (`platform-security/`)
- DevOps/Platform: Argo CD, appsets, templates, reusable pipelines (`platform-delivery/`)
- Dev teams: service code/config, app-native OIDC, runtime SLOs (`apps/`)

## Deployment Strategy
- Bootstrap order: `infra -> security -> delivery -> apps`
- CI gates: tests, lint, image scan, policy check, manifest validation
- CD gates: admission policy, drift detection, signed image/provenance, rollout health
- Prefer app-native OIDC; ingress auth is temporary or defense-in-depth
- Promotion model: one-way `green -> yellow`, immutable pins, PR approvals for yellow
