# environment-config

Environment overlays and runtime defaults for:
- preview
- qa
- stage
- prodlike
- prod

Each environment defines service enrollment files under `services/*.yaml`.
Required keys:
- `appName`: Argo-safe app id (for Application name generation).
- `service`: canonical service id (used to merge with promotion records).
- `servicePath`: repo path to service deployment manifests.
- `namespace`: destination namespace in the target environment cluster.

These files hold environment-specific deployment intent, not application source code.
