# observability

Deploys shared infrastructure monitoring for management:
- Prometheus
- Grafana

Ingress endpoints:
- `grafana.env-management.appfactory.local`
- `prometheus.env-management.appfactory.local`

Collection model:
- Scrapes `ServiceMonitor`/`PodMonitor` resources cluster-wide.
- Auto-scrapes endpoints/pods with port names matching `*metrics*`.
- Auto-scrapes services/pods annotated with `prometheus.io/scrape: "true"`.

Per-environment agents:
- `platform-delivery/argocd/appsets/env-observability-agents.yaml` deploys a Prometheus agent into preview/qa/stage/prodlike/prod.
- Agents remote-write to `http://prometheus.env-management.appfactory.local/api/v1/write`.
