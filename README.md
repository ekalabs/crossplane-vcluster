# Crossplane vCluster Platform Workflow

## Implemented Domain Layout
The proposed structure is now scaffolded in this repository:
- `platform-infra/`
- `platform-security/`
- `platform-delivery/`
- `apps/`
- `environments-config/`
- `docs/`

These folders provide concrete ownership boundaries while current active manifests remain compatible with the existing Argo CD paths under `environments/` and `crossplane-manifests/`.

## Purpose
This repository defines a multi-environment platform with:
- `env-management` for shared control services (Argo CD, Keycloak, security tooling)
- `env-green` and `env-yellow` for application workloads

This document defines:
- deployment workflow
- segregation of responsibilities
- target folder structure
- starter ApplicationSet layout

## Environment Model
- `env-management`: platform control plane and shared identity (`keycloak.env-management.appfactory.local`)
- `env-green`: first application environment (fast validation)
- `env-yellow`: second application environment (pre-prod style gate)

## Responsibility Segregation (RACI)
### Infra Team
- Owns cluster/vcluster lifecycle, networking, ingress controllers, storage classes, DNS, base observability.
- Owns bootstrap manifests under `environments/*/vcluster.yaml` and infra-level ingress plumbing.

### Security Team
- Owns Keycloak realm/client standards, RBAC baseline, Kyverno/OPA policies, secret handling standards.
- Owns policy definitions and exception process.
- Approves security-sensitive promotions (OIDC changes, policy relaxations, privileged workload changes).

### DevOps / Platform Team
- Owns Argo CD topology, ApplicationSet generation, CI/CD templates, promotion automation.
- Provides paved-road templates for app onboarding, OIDC integration, ingress/auth middleware.

### Application Development Teams
- Own app code, app manifests/Helm values, app-level OIDC integration, app authorization logic.
- Own service SLOs, dashboards, and rollback procedures for their workloads.

## Concrete Repository Structure
Current concrete layout in this repo:

```text
.
├── argocd_values.yaml
├── traefik_values.yaml
├── environments/
│   ├── env-management/
│   │   ├── vcluster.yaml
│   │   ├── bootstrap.yaml
│   │   ├── crossplane-providers.yaml
│   │   ├── crossplane-manifests.yaml
│   │   └── ingress/
│   ├── env-green/
│   │   ├── vcluster.yaml
│   │   ├── bootstrap.yaml
│   │   ├── crossplane-providers.yaml
│   │   ├── crossplane-manifests.yaml
│   │   └── ingress/
│   └── env-yellow/
│       ├── vcluster.yaml
│       ├── bootstrap.yaml
│       ├── crossplane-providers.yaml
│       ├── crossplane-manifests.yaml
│       └── ingress/
├── crossplane-manifests/
│   ├── env-management/
│   │   ├── kustomization.yaml
│   │   ├── platform-security/
│   │   │   └── identity/
│   │   │       └── keycloak/
│   │   │           └── release.yaml
│   │   └── platform-observability/
│   ├── env-green/
│   │   ├── kustomization.yaml
│   │   └── apps/
│   │       └── platform/nginx/
│   │           └── release.yaml
│   └── env-yellow/
│       ├── kustomization.yaml
│       └── apps/
│           └── platform/echo-server/
│               └── release.yaml
├── environments/applicationsets/
│   ├── env-management-components.yaml
│   ├── env-green-apps.yaml
│   └── env-yellow-apps.yaml
├── common/
├── kyverno/
└── examples/applicationsets/
```

## GitOps Deployment Workflow
1. Infra bootstrap
- Infra team updates `platform-infra/` (clusters, ingress, networking, bootstrap wiring).
- Argo CD sync applies vcluster and host routing.

2. Platform bootstrap
- DevOps team updates bootstrap/provisioning apps under `platform-infra/clusters/*`.
- Crossplane, providers, and shared controllers become ready.

3. Security baseline
- Security team merges policy/RBAC/identity changes in `platform-security/`.
- Argo CD sync enforces baseline before app rollouts.

4. App onboarding
- Dev team adds app manifests under `crossplane-manifests/env-green/apps/<team>/<service>/`.
- App is deployed to green through ApplicationSet automation.

5. Promotion to yellow
- Promotion is PR-based (Git change only), no manual `kubectl`.
- Same app version + env-specific overrides promoted from green to yellow after checks.

6. Production-like controls in yellow
- Yellow requires stronger checks: security scan pass, policy pass, change approval where required.

## Argo CD App-of-Apps Chain
Current control-plane chain:

1. Root app: `environments.yaml`
- Syncs `environments/envs` into `argo-cd`.

2. Platform apps from `environments/envs/`
- `platform-infra.yaml` (sync-wave `1`)
- `platform-security.yaml` (sync-wave `2`)
- `platform-delivery.yaml` (sync-wave `3`)

3. Delivery app
- `platform-delivery` syncs `platform-delivery/`, including ApplicationSets.
- This creates:
  - `env-management-components`
  - `env-green-apps`
  - `env-yellow-apps`

4. ApplicationSets generate per-component/per-app Applications
- Management components from `crossplane-manifests/env-management/platform-*`
- Green apps from `crossplane-manifests/env-green/apps/*/*`
- Yellow apps from `crossplane-manifests/env-yellow/apps/*/*`

## Promotion and Quality Gates
Required checks before promotion:
- unit/integration tests pass
- image vulnerability threshold pass
- policy admission pass (Kyverno/OPA)
- rollout health check pass
- SLO/error budget not violated

Optional high-risk approvals:
- Security approval for policy exceptions
- Infra approval for network/ingress/storage-impacting changes

## OIDC Strategy
- Preferred: app-native OIDC for each service in green/yellow.
- Optional defense-in-depth: ingress auth in front of app.
- Keycloak in management is the central IdP; each app has separate client and least-privilege scopes.

## Starter ApplicationSet Layout
Starter manifests matching current paths are provided at:
- `environments/applicationsets/env-management-components.yaml`
- `environments/applicationsets/env-green-apps.yaml`
- `environments/applicationsets/env-yellow-apps.yaml`

Reference examples are also kept at:
- `examples/applicationsets/env-green-apps.yaml`
- `examples/applicationsets/env-yellow-apps.yaml`

## Branching and Change Policy
- `main`: source of truth for Argo CD sync.
- Short-lived feature branches.
- PR review requirements:
  - infra changes: Infra + DevOps
  - security changes: Security + DevOps
  - app changes: Dev team + DevOps

## Operating Rules
- No manual drift in clusters (`kubectl apply` only for emergency break-glass, followed by PR reconciliation).
- All environment changes are traceable to Git commits.
- Keep environment differences as values/overlays, not copied manifests.
