# Coding Agent Boundaries

- Keep Codex Python SDK execution, working-copy management and native Git operations in this service.
- Require a Redmine issue for runtime development work. Enforce one active issue locally; subagents stay within its scope.
- Preserve one primary session per issue across switches and restarts. Report its ID through the orchestrator API and keep
  Codex session files on the service's persistent volume.
- Use `core` contracts and the orchestrator client for assignments, steering, heartbeats, questions and results. Do not add
  direct access to PostgreSQL work queues.
- Process stop commands and stop obsolete execution when the API rejects its assignment.
- Keep task steps and completion criteria in the configured workflow skill; do not hard-code an MR or CI completion policy.
- Release the coding slot for a human question only when the answer fully blocks progress. Otherwise continue the issue.
- Keep explanatory forks ephemeral and read-only: no coding or file changes, no replacement of the primary session.
  Changes to the ongoing execution must use steering of the main session.
- Connect the runtime to Redmine, GitLab and email MCP services, including email for development testing, but not control MCP.
- Preserve MCP-side provider credential isolation for all repository access, including native Git authentication.
- Correlate execution logs by run and service for database storage; keep session persistence separate from log storage.
