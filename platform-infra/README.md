# platform-infra

Owns vcluster creation, Traefik, base networking, DNS, certificate plumbing, storage, and observability foundations.

## Structure
- `clusters/management`, `clusters/preview`, `clusters/qa`, `clusters/stage`, `clusters/prodlike`, `clusters/prod`
- `clusters/management/local-kind/`: local kind cluster bootstrap artifacts
- `network/`, `dns/`, `ingress/`, `storage/`, `observability/`
- `crossplane/`: environment API (`XEnvironment`) and compositions
