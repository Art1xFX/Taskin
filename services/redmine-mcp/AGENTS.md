# Redmine MCP boundaries

- Keep this service an adapter between MCP tools and Redmine provider operations.
- Leave Taskin assignment intake and webhook handling in `orchestrator`.
- Keep task workflow, completion criteria and Codex execution outside tool handlers.
- Resolve provider authentication inside this container; do not export Redmine credentials to agent runtimes.
- Do not add per-call orchestrator authorization callbacks or a `PreToolUse` hook in the initial scope.
- Keep Taskin identity mappings, queues and domain migrations outside this service.
- Preserve run and service correlation when reporting provider results, failures and execution logs.
