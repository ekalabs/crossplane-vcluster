# OpenBao (Env Management)

This component deploys OpenBao into the `env-management` cluster via Crossplane Helm provider.

Primary endpoint:
- `http://openbao.env-management.appfactory.local:32080`

Notes:
- ProviderConfig `provider-helm` must be available in `env-management` control plane.
- This is the shared secrets service for preview, qa, stage, prodlike, and prod policy domains.
- `server.dev.enabled: true` is currently set for lab bring-up only.
- `devRootToken` is `root`.
