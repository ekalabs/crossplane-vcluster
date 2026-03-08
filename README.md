# Enterprise Platform Architecture (Argo + Crossplane)

This workspace implements a GitOps architecture with environment promotion:
- Preview (ephemeral)
- QA
- Stage
- ProdLike
- Prod

Core control planes:
- Argo CD: deployment orchestration and app-of-apps
- Crossplane: environment API/composition and provider-driven provisioning

## Top-Level Layout
- `platform-infra/`: clusters, networking, ingress, storage, observability, Crossplane environment APIs
- `platform-security/`: Keycloak, OpenBao, Kyverno/OPA-style policy and RBAC baseline
- `platform-delivery/`: Argo appsets/projects/bootstrap, deployment templates, reusable pipeline docs
- `developer-platform/`: Gitea, SonarQube, CI runner scaffolding
- `apps/`: service workloads (code/manifests/values)
- `environment-config/`: overlays for preview/qa/stage/prodlike/prod
- `promotion-repo/`: immutable promotion records qa->stage->prodlike->prod

## Argo Control Flow
1. Root app: `environments.yaml`
2. Platform apps from `environments/envs/`:
   - `platform-infra`
   - `platform-security`
   - `platform-delivery`
3. ApplicationSets from `platform-delivery/argocd/appsets/` deploy workloads to:
   - `env-preview`
   - `env-qa`
   - `env-stage`
   - `env-prodlike`
   - `env-prod`

## Crossplane Environment API
`platform-infra/crossplane/xenvironments/` includes:
- `XEnvironment` XRD
- `Composition` for baseline namespace controls (namespace, default-deny network policy, resource quota)

Claims examples are in:
- `platform-infra/crossplane/claims/`

## Promotion Model
- Build once, publish immutable artifact digest.
- Promote by PR approvals in `promotion-repo/`:
  - `qa` -> `stage` -> `prodlike` -> `prod`
- No rebuild between environments.

## Notes
- Preview environments include TTL metadata via environment claims template.
- Security/identity applies across all environments through Keycloak/OpenBao/policy bundles.
