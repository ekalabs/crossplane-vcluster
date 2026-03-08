# Observability Egress ACL

Allows pods in `monitoring` namespace to:
- resolve DNS via `kube-system` on TCP/UDP 53
- egress on TCP 80/443 for Prometheus remote-write and endpoint scraping

Deployed to runtime environments by:
- `platform-delivery/argocd/appsets/env-observability-acl.yaml`
