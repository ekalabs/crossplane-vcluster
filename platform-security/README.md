# platform-security

Owns OIDC standards, policy bundles, RBAC baseline, and approvals for policy exceptions.

## Structure
- `identity/keycloak/`: realms, clients, groups, roles.
- `secrets/openbao/`: shared secrets manager for management/green/yellow.
- `policies/kyverno/`: pod/image/namespace/network policy baselines.
- `rbac/baseline/`: cluster/namespace RBAC standards.
- `rbac/exceptions/`: approved, time-bounded exceptions.
