# Core boundaries

- Keep shared Pydantic models, statuses and API errors aligned with the orchestrator's HTTP contracts and their consumers.
- Keep the orchestrator client asynchronous. Its job is API transport and error representation, not assignment selection,
  authorization policy or task execution.
- Keep the client independent of MCP transport; `control-mcp` consumes the HTTP client as a service-side dependency.
- Leave SQLAlchemy and Django models, persistence and migrations with their service owners.
- Keep FastAPI routes, administration views, Codex runtimes and external-provider adapters out of this package.
- Preserve a reusable dependency direction: services may import `core`; shared contracts and the client must not import
  service implementations or embed service-specific workflows.
