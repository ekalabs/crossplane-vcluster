# Standard Operating Procedure (SOP)

## 1. Platform Bootstrap (Local Kind)

1. Create cluster:
```bash
kind create cluster --config platform-infra/clusters/management/local-kind/cluster.yaml
```
2. Install Traefik:
```bash
helm repo add traefik https://traefik.github.io/charts
helm repo update
kubectl create namespace traefik --dry-run=client -o yaml | kubectl apply -f -
helm upgrade --install traefik traefik/traefik \
  -n traefik \
  -f platform-infra/ingress/traefik/values-local-kind.yaml
```
3. Install Argo CD:
```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
kubectl create namespace argo-cd --dry-run=client -o yaml | kubectl apply -f -
helm upgrade --install argo-cd argo/argo-cd \
  -n argo-cd \
  -f platform-delivery/argocd/bootstrap/values-local-kind.yaml
```
4. Register Git repo secret:
```bash
kubectl apply -f platform-delivery/argocd/bootstrap/repository-secret.yaml
```
5. Bootstrap root app:
```bash
kubectl apply -f environments.yaml
```

## 2. Standard Deployment Flow

1. Verify root/platform apps:
```bash
kubectl -n argo-cd get applications
```
2. Verify ApplicationSets:
```bash
kubectl -n argo-cd get applicationsets
```
3. Confirm sync/health:
```bash
kubectl -n argo-cd get applications -o wide
```

## 3. Promotion SOP (qa -> stage -> prodlike -> prod)

1. Update promotion record in `promotion-repo/<env>/`.
2. Open PR and collect required approvals.
3. Merge PR.
4. Confirm target Argo apps for that environment are synced and healthy.

## 4. Observability SOP

1. Management monitoring stack is defined in:
- `developer-platform/observability/release.yaml`
2. Per-environment Prometheus agents are defined in:
- `platform-delivery/argocd/appsets/env-observability-agents.yaml`
3. Runtime metrics egress ACL is defined in:
- `platform-delivery/argocd/appsets/env-observability-acl.yaml`
- `platform-security/policies/network/observability-egress/networkpolicy.yaml`

## 5. Change Management SOP

1. Validate manifests before merge:
```bash
kubectl kustomize platform-infra >/dev/null
kubectl kustomize platform-security >/dev/null
kubectl kustomize platform-delivery >/dev/null
```
2. Use focused commits by layer:
- infra/core
- security/policy
- delivery/config
- app/runtime
3. Add or update docs in `docs/operations/` for behavior changes.

## 6. Recovery SOP

1. If Argo app is OutOfSync/Degraded, check:
```bash
kubectl -n argo-cd describe application <app-name>
kubectl -n argo-cd get application <app-name> -o yaml
```
2. Fix manifest or target cluster issue.
3. Re-sync from Argo UI/CLI.

## 7. Redeploy After Architecture Change

When testing from a feature branch before merge:

1. Pin Argo Git refs (`targetRevision`/`revision`) to the feature branch in:
- `environments.yaml`
- `environments/envs/*.yaml`
- `platform-delivery/argocd/appsets/*.yaml`
- `platform-infra/clusters/**/*.yaml` (Argo Application sources)
2. Push branch.
3. Apply root app:
```bash
kubectl apply -f environments.yaml
```
4. Force refresh:
```bash
kubectl -n argo-cd annotate application environments argocd.argoproj.io/refresh=hard --overwrite
```
5. Remove obsolete appsets/apps if migrating models:
```bash
kubectl -n argo-cd delete applicationset env-green-apps env-yellow-apps --ignore-not-found
kubectl -n argo-cd delete application green-nginx green-echo-server yellow-nginx yellow-echo-server --ignore-not-found
```
6. Validate new appsets are present and healthy.

After branch is merged to default branch:
1. Set pinned branch revisions back to `HEAD`.
2. Commit and sync again.

Note:
- `platform-infra/kustomization.yaml` should only include resources valid on the destination cluster.
- Do not include Crossplane XRD/Composition CRs unless Crossplane CRDs are installed on that cluster.
