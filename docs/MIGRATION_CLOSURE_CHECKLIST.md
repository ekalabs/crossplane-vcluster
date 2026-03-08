# Migration Closure Checklist (Reference Architecture Alignment)

## P0 - Must Fix Now

- [x] Fix broken delivery kustomization references.
- [x] Remove remaining legacy `green/yellow` runtime assumptions from app manifests.
- [x] Wire ApplicationSets to promotion/environment config inputs (not raw `apps/*/*` scan).

## P1 - Security and Governance Hardening

- [ ] Replace Keycloak static admin credentials with OpenBao-backed secret flow.
- [ ] Move OpenBao from `dev` mode to HA + auto-unseal (KMS/HSM backed).
- [ ] Add declarative Keycloak realm/client/group/role manifests.
- [ ] Create real Argo `AppProject` resources with repo/destination/namespace/RBAC constraints.
- [ ] Enforce signed image/provenance and policy gates in admission.

## P2 - Platform Completeness

- [ ] Add deployable manifests for shared developer tooling:
  - Gitea
  - SonarQube
  - JFrog
  - CI runners
- [ ] Add deployable observability stack manifests:
  - Prometheus
  - Grafana
  - Alerting baselines
- [ ] Implement preview TTL lifecycle automation for ephemeral environments.

## Legacy Cleanup

- [x] Remove deprecated `environments-config/` tree (old green/yellow promotion model).
- [x] Move root bootstrap artifacts into platform-owned paths:
  - `platform-infra/clusters/management/local-kind/cluster.yaml`
  - `platform-infra/ingress/traefik/values-local-kind.yaml`
  - `platform-delivery/argocd/bootstrap/values-local-kind.yaml`
  - `platform-delivery/argocd/bootstrap/repository-secret.yaml`
- [x] Remove obsolete root artifacts (`SOR.md`, `.DS_Store`, legacy green/yellow app values files).
