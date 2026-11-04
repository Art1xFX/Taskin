# Chat Agent Boundaries

- Keep the runtime conversational: user-task development, repository commands and source changes belong to `coding-agent`.
  Do not bypass that boundary through GitLab API tools.
- Use the `core` client for orchestrator communication; keep PostgreSQL access and Telegram transport out of this service.
- Keep Python message routing and Codex session lifecycle here, including the per-user session association and steering.
- Preserve the model's choice between answering and using MCP tools; connect all four agreed MCP services.
- Keep explanation requests to the programmer distinct from steering that changes its execution.
- Enforce the regular conversation limit locally. Steering an active conversation must not require another turn slot.
- Generate busy replies with a separately limited, tool-free, very lightweight model. Preserve the queued input and primary
  conversation context.
- Process stop commands and terminate obsolete execution when the orchestrator rejects its assignment.
- Keep provider authentication inside the MCP services; do not copy provider credentials into the chat runtime.
- Associate execution logs with their run and service for database storage; do not introduce a shared filesystem log backend.
