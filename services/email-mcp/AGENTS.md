# Email MCP boundaries

- Keep the functional scope limited to reading and sending email messages.
- Execute requested operations; do not introduce autonomous mailbox monitoring, Taskin intake or workflow selection.
- Keep mail provider requests, result translation and technical error handling inside this adapter.
- Authenticate inside the MCP container using Taskin's account; do not export provider credentials to agents.
- Support calls from both agents, including email operations needed by the programmer's tests.
- Do not add per-call orchestrator authorization callbacks or a `PreToolUse` hook in the initial scope.
- Keep Taskin queues, identity mappings, domain migrations and Codex sessions outside this service.
- Preserve run and service correlation when reporting results, failures and execution logs.
