# platform-infra

Owns vcluster creation, Traefik, base networking, external-dns, certificate plumbing.

## Structure
- `clusters/management`, `clusters/green`, `clusters/yellow`: cluster-level composition and lifecycle pointers.
- `network/`, `dns/`, `ingress/`, `storage/`, `observability/`: shared infra capability domains.

This folder is the target home for infra ownership. During migration, references point to current active manifests under `environments/`.
