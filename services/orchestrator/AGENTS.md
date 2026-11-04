# Orchestrator boundaries

- Keep Taskin domain persistence, SQLAlchemy models, schema migrations and durable assignment rules in this service.
  Publish API contracts through `core`; do not require consumers to import the ORM.
- Select assignments on the server, while leaving execution capacity and Codex lifecycle management to the agent runtimes.
  Agents communicate through the API rather than manipulating PostgreSQL queues.
- Reject stale assignments and preserve priority-based recovery after heartbeat loss. An obsolete execution must not be
  accepted as the current assignment.
- Keep final API authorization here. Use trusted caller context for control-MCP requests instead of an identity chosen by
  the model.
- Automatically enqueue eligible Redmine assignment events using project settings. Do not introduce orchestrator-side MCP
  calls or provider workflows to implement intake.
- Send Telegram responses directly while handling the submission API request. Do not add an outgoing outbox, delivery
  queue or Dramatiq delivery actor.
- Limit APScheduler/Dramatiq work to heartbeat and timeout checks, with scheduler and worker entry points in this project.
- Persist service execution logs with run and service context in the database, separately from agents' Codex session files.
- Keep Taskin identity records separate from Django's administration authentication models and migrations.
- Treat workflow completion and whether a human answer fully blocks work as agent-reported decisions. Do not hard-code
  development workflow or mandatory MR/CI completion rules in the orchestrator.
