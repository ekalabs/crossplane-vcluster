# Operations Documentation

## Scope

This folder contains the operational docs for the current platform architecture:
- management + runtime environments (`preview`, `qa`, `stage`, `prodlike`, `prod`)
- Argo CD GitOps
- Crossplane-based platform resources
- security and observability platform components

## Documents

- `SOP.md`: standard operating procedures (deploy, promote, recover, validate).
- `RUNBOOK.md`: incident playbooks and troubleshooting flows.
- `QUICK_COMMANDS.md`: high-frequency operational commands.

## Ownership

- Primary owner: Platform/DevOps
- Co-owners: Infra and Security

## Documentation Management Rules

1. Update docs in the same PR as operational or platform behavior changes.
2. Keep commands copy-paste ready and environment-specific.
3. Add exact file paths for manifests changed by a procedure.
4. For any incident handled manually, add or improve a runbook step after resolution.
5. Review this folder at least once per sprint.
