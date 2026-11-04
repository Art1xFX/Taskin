# Administration boundaries

- Keep generated Taskin Django models unmanaged. Domain-schema changes and their migrations belong to `orchestrator`.
- Regenerate domain models from the existing database during development and retain their generated source in the
  repository. Keep custom administration behavior outside the generated model code.
- Allow direct database editing of users, external identity associations, projects, repositories and settings.
- Keep executions, assignments, queue state and execution logs read-only in the administration interface. Route operational
  actions through the orchestrator API and reuse `core` contracts and its client where appropriate.
- Keep Django login users and framework-managed migrations separate from Taskin's identity registry and domain migrations.
- Build log views around database records correlated by run and service; do not introduce filesystem log browsing or mounts
  of agent session storage as the log interface.
- Keep scheduling rules, Codex execution and external-provider adapters out of this service.
