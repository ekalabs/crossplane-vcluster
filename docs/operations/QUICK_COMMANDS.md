# Quick Commands

## Argo CD

```bash
kubectl -n argo-cd get applications
kubectl -n argo-cd get applicationsets
kubectl -n argo-cd describe application <app-name>
```

## Platform Render Validation

```bash
kubectl kustomize platform-infra >/dev/null
kubectl kustomize platform-security >/dev/null
kubectl kustomize platform-delivery >/dev/null
```

## Runtime Environment Checks

```bash
kubectl -n argo-cd get applications | grep "^preview-"
kubectl -n argo-cd get applications | grep "^qa-"
kubectl -n argo-cd get applications | grep "^stage-"
kubectl -n argo-cd get applications | grep "^prodlike-"
kubectl -n argo-cd get applications | grep "^prod-"
```

## Observability Checks

```bash
kubectl -n argo-cd get applications | grep observability
kubectl -n monitoring get pods,svc
kubectl -n monitoring logs deploy/prometheus-agent-server
```

## DNS/Connectivity Check (from cluster)

```bash
kubectl -n monitoring run -it --rm dns-test --image=busybox:1.36 --restart=Never -- nslookup prometheus.env-management.appfactory.local
kubectl -n monitoring run -it --rm curl-test --image=curlimages/curl --restart=Never -- \
  curl -sv http://prometheus.env-management.appfactory.local/api/v1/write
```
