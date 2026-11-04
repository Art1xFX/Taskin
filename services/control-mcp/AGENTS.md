# Control MCP boundaries

- Implement management tools through the orchestrator API client and contracts from `core`.
- Keep authorization and assignment decisions in the orchestrator; do not bypass its API with database access.
- Forward caller context from the trusted runtime rather than accepting a model-selected user identity.
- Keep the initial MCP connection limited to `chat-agent`; coding runtime API polling is a separate interface.
- Forward requests to pause, resume, cancel, switch or steer work; do not manipulate Codex sessions here.
- Route explanations about current coding work through the orchestrator. The coding service owns the temporary fork.
- Keep user and project configuration editing in `admin`.
- Preserve run and service correlation for database-backed execution logs.
