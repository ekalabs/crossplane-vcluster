# Deployment Strategy

1. Bootstrap order per environment:
- infra base -> security baseline -> delivery stack -> apps

2. CI checks:
- tests
- lint
- image scan
- policy check
- manifest validation

3. CD checks:
- admission policy
- drift detection
- signed image/provenance
- rollout health gates

4. Identity strategy:
- prefer app-native OIDC
- ingress auth only as temporary or defense-in-depth

5. Promotion model:
- one-way promotion green -> yellow
- immutable version pinning
- PR approvals required for yellow
