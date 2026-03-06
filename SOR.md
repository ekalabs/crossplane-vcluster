# Segregation Of Responsibilities

**Proposed Structure**

1. `platform-infra`  
    - `clusters/management`, `clusters/green`, `clusters/yellow`  
    - `network/`, `dns/`, `ingress/`, `storage/`, `observability/`  
    - Owns vcluster creation, Traefik, base networking, external-dns, certs plumbing.

2. `platform-security`  
    - `identity/keycloak/` (realms, clients, groups, roles)  
    - `policies/kyverno/` (pod security, image policy, namespace policy, network defaults)  
    - `rbac/baseline/`, `exceptions/`  
    - Owns OIDC standards, policy bundles, and approvals for policy exceptions.

3. `platform-delivery`  
    - `argocd/projects/`, `argocd/appsets/`, `argocd/bootstrap/`  
    - `templates/service-helm/`, `templates/oidc/`, `templates/ingress-auth/`  
    - `pipelines/reusable/`  
    - Owns GitOps framework, CI/CD templates, promotion rules.

4. `apps/<team>/<service>`  
    - `src/`, `charts/` or `k8s/`  
    - `deploy/values-dev.yaml`, `deploy/values-green.yaml`, `deploy/values-yellow.yaml`  
    - Owns app code, app config, native OIDC config, runtime SLOs.

5. `environments-config` (optional central repo if you prefer separated promotion)  
    - `green/services/`, `yellow/services/` with pinned app versions and overrides  
    - PR-based promotion only (`green` auto, `yellow` approved).

**RACI (Simplified)**

1. Cluster lifecycle, networking, ingress, storage  
    - Responsible: Infra  
    - Accountable: Infra Lead  
    - Consulted: Security, DevOps  
    - Informed: Dev teams

2. Identity (Keycloak), OIDC standards, RBAC baseline, policy-as-code  
    - Responsible: Security  
    - Accountable: Security Lead  
    - Consulted: Infra, DevOps  
    - Informed: Dev teams

3. Argo CD, CI/CD templates, app onboarding, release automation  
    - Responsible: DevOps/Platform  
    - Accountable: Platform Lead  
    - Consulted: Infra, Security  
    - Informed: Dev teams

4. App code, app-native OIDC integration, service config, SLOs  
    - Responsible: Dev team  
    - Accountable: Service owner  
    - Consulted: DevOps, Security  
    - Informed: Infra

5. Environment promotion (`green -> yellow`)  
    - Responsible: DevOps automation  
    - Accountable: Product/Service owner  
    - Consulted: Security (high risk), Infra (platform-impacting changes)  
    - Informed: Stakeholders

**Deployment Strategy**

1. Bootstrap order per environment: infra base -> security baseline -> delivery stack -> apps.  
2. Enforce checks in CI: tests, lint, image scan, policy check, manifest validation.  
3. Enforce checks in CD: admission policy, drift detection, signed image/provenance, rollout health gates.  
4. Prefer app-native OIDC; ingress auth only as temporary or defense-in-depth.  
5. Use one-way promotion with immutable version pinning and PR approvals.
