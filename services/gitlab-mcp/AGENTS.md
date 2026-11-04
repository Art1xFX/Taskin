# GitLab MCP boundaries

- Keep this service focused on GitLab API requests, response translation and technical errors.
- Keep native Git execution and working-copy operations in `coding-agent`.
- Preserve the chatter's conversational scope; GitLab tool access must not turn it into a development executor.
- Keep task workflow and completion policy in the configured agent skills rather than GitLab tool handlers.
- Authenticate with Taskin's account inside the MCP container; do not export provider credentials to agents.
- Do not add per-call orchestrator authorization callbacks or a `PreToolUse` hook in the initial scope.
- Keep Taskin queues, identity mappings, domain migrations and Codex sessions outside this service.
- Preserve run and service correlation when reporting provider results, failures and execution logs.
