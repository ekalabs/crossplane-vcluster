# RACI (Simplified)

## Cluster lifecycle, networking, ingress, storage
- Responsible: Infra
- Accountable: Infra Lead
- Consulted: Security, DevOps
- Informed: Dev teams

## Identity (Keycloak), OIDC standards, RBAC baseline, policy-as-code
- Responsible: Security
- Accountable: Security Lead
- Consulted: Infra, DevOps
- Informed: Dev teams

## Argo CD, CI/CD templates, app onboarding, release automation
- Responsible: DevOps/Platform
- Accountable: Platform Lead
- Consulted: Infra, Security
- Informed: Dev teams

## App code, app-native OIDC integration, service config, SLOs
- Responsible: Dev team
- Accountable: Service owner
- Consulted: DevOps, Security
- Informed: Infra

## Environment promotion (preview -> qa -> stage -> prodlike -> prod)
- Responsible: DevOps automation
- Accountable: Product/Service owner
- Consulted: Security (high risk), Infra (platform-impacting changes)
- Informed: Stakeholders
