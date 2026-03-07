# Migration Closure Checklist (Reference Architecture Alignment)

## P0 - Must Fix Now

- [x] Fix broken delivery kustomization references.
  - Updated `platform-delivery/kustomization.yaml` to use preview/qa/stage/prodlike/prod appsets.
- [ ] Remove remaining legacy `green/yellow` runtime assumptions from app manifests.
  - `apps/platform/nginx/k8s/release.yaml` still points to `nginx.env-green.appfactory.local`.
  - `apps/platform/echo-server/k8s/release.yaml` still points to `echo-server.env-yellow.appfactory.local`.
- [x] Wire ApplicationSets to promotion/environment config inputs (not raw `apps/*/*` scan).
  - Preview now reads `environment-config/preview/services/*.yaml`.
  - QA/Stage/ProdLike/Prod now merge `environment-config/<env>/services/*.yaml` with `promotion-repo/<env>/*.yaml` on `service`.

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
