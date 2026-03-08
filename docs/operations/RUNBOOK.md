# Runbook

## 1. Argo Application SyncError

Symptoms:
- `SyncError`
- `failed to generate manifest`
- `path does not exist`

Actions:
1. Inspect app:
```bash
kubectl -n argo-cd describe application <app-name>
```
2. Verify source path exists in repo and branch.
3. Validate local render:
```bash
kubectl kustomize <path>
```
4. Commit fix and resync app.

## 2. Crossplane Object CRD Missing

Symptoms:
- `could not find kubernetes.crossplane.io/Object`

Actions:
1. Check provider install in target cluster:
```bash
kubectl get providers.pkg.crossplane.io
kubectl get crd | grep kubernetes.crossplane.io
```
2. Ensure cluster provider app is synced:
- `platform-infra/clusters/<env>/crossplane-providers.yaml`
3. Retry app sync after provider healthy.

## 3. Prometheus Agent Remote Write Failing

Symptoms:
- agent logs show remote write errors/timeouts

Actions:
1. Check agent pod logs in runtime env:
```bash
kubectl -n monitoring logs deploy/prometheus-agent-server
```
2. Check ACL app status:
```bash
kubectl -n argo-cd get applications | grep observability-acl
```
3. Validate DNS from runtime cluster:
```bash
kubectl -n monitoring run -it --rm dns-test --image=busybox:1.36 --restart=Never -- nslookup prometheus.env-management.appfactory.local
```
4. Validate HTTP reachability:
```bash
kubectl -n monitoring run -it --rm curl-test --image=curlimages/curl --restart=Never -- \
  curl -sv http://prometheus.env-management.appfactory.local/api/v1/write
```

## 4. Keycloak Not Reachable

Actions:
1. Check release app and pod:
```bash
kubectl -n argo-cd get applications | grep keycloak
kubectl -n keycloak get pods
```
2. Check ingress:
```bash
kubectl -n keycloak get ingress
```
3. Verify host routing via Traefik and DNS.

## 5. OpenBao Not Healthy

Actions:
1. Check release and pods:
```bash
kubectl -n argo-cd get applications | grep openbao
kubectl -n openbao get pods
```
2. Confirm expected mode (currently dev mode in this repo).
3. Verify ingress host:
`openbao.env-management.appfactory.local`

## 6. Runtime App Not Deployed in Environment

Actions:
1. Confirm app is enrolled:
- `environment-config/<env>/services/*.yaml`
2. For gated envs, confirm promotion record exists:
- `promotion-repo/<env>/<service>.yaml`
3. Check env appset output:
```bash
kubectl -n argo-cd get applications | grep "^<env>-"
```
