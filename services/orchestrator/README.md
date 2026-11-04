# Orchestrator

Taskin's control plane. This service uses FastAPI and SQLAlchemy to own durable work, expose the management API and connect
Telegram transport to the agent runtimes.

This document describes the agreed service boundary. Runtime implementation is not present yet.

## Responsibilities

- Receive Telegram webhooks and persist incoming messages for the chatter. Admit users with a registered Taskin Telegram
  identity; send a standard access-denied response to unknown users.
- Process relevant Redmine assignment webhooks and automatically enqueue issues assigned to Taskin according to project
  settings. Enqueueing an eligible assignment does not require an LLM decision.
- Select work for agents according to queue state and priorities. Persist assignments, incoming messages, steering and
  management commands, questions, answers and reported execution state.
- Receive agent heartbeats, detect expired assignments and timeouts, and make lost work available for reassignment.
  Reject operations on obsolete assignments so the affected agent can stop processing them.
- Own Taskin users, their Telegram/GitLab/Redmine identity associations, project and repository configuration, and settings.
  Support multiple configured projects and repositories.
- Persist Redmine-issue-to-Codex-session and Telegram-user-to-conversation associations. The agents own the actual Codex
  session runtimes and their persistent files.
- Store execution logs in PostgreSQL, grouped by a common run context and separated by the service producing each entry.
- Perform the final authorization checks for its HTTP API, including operations exposed through the control MCP.

## Interfaces

The [coding](../coding-agent/README.md) and [chat](../chat-agent/README.md) runtimes obtain assignments, messages and commands
through this API and report heartbeats, session associations, logs, results and execution state. They use the asynchronous
orchestrator client from [core](../../packages/core/README.md), rather than accessing Taskin queues directly in PostgreSQL.

[Control MCP](../control-mcp/README.md) translates the chatter's management tool calls into API requests with trusted caller
context. [The administration service](../admin/README.md) directly edits configuration records and reads execution state and
logs from the database; any operational actions it provides use the API.

The chatter submits generated responses through the API. The request handler sends each response directly through the
Telegram Bot API and handles the transport outcome. There is no separate outgoing delivery queue or outbox.

The orchestrator receives programmer clarification questions and makes their answers available through the API. A question
alone does not release the coding slot: the coding agent determines whether the missing answer fully blocks its task.

## Runtime and persistence

The intended deployment has separate API, APScheduler scheduler and Dramatiq worker processes owned by this project. They
can run in separate containers from one service image. APScheduler schedules heartbeat and timeout checks; Dramatiq
executes those checks only. It does not run Codex, process general webhook jobs or deliver Telegram replies.

PostgreSQL stores the durable agent work and steering queues, domain records and execution logs. SQLAlchemy models and
domain-schema migrations belong here. Django's administration login users and framework tables belong to the admin service.

## Boundaries

Agent services own Codex session execution, steering delivery, cancellation and local concurrency limits. The initial
installation has one coding-agent instance processing one Redmine issue at a time and one chat-agent instance managing
concurrent dialogs within its configured limit.

The orchestrator does not call MCP servers or initiate Redmine, GitLab or email requests. LLM agents choose integration
tools. Task workflows and completion criteria belong to the coding agent's workflow skills, rather than to orchestration
rules or a fixed MR/CI checklist.
