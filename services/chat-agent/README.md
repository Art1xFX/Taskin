# Chat Agent

Taskin's conversational service coordinates work through private Telegram dialogs and MCP tools. It can converse before a
Redmine issue exists. This document defines the agreed service boundary; runtime implementation is pending.

## Responsibilities

- Run Codex through the Python SDK and manage conversation session creation, continuation, steering and stopping.
- Retrieve incoming messages through the [orchestrator API](../orchestrator/README.md), choose the user's Codex session and
  steer an active turn or start the next turn when capacity is available.
- Preserve a conversation session and the runtime context needed to continue it for each Telegram user. Report its
  session ID to the orchestrator.
- Let the model choose whether to answer directly or use the connected MCP tools.
- Submit generated responses to the orchestrator API. The orchestrator sends them directly to Telegram while handling that
  request; this service does not own Telegram transport or an outgoing delivery queue.
- Send execution heartbeats, process control commands and stop obsolete execution when the API rejects its assignment.

## Tools and coordination

Connect to the shared `control-mcp`, `redmine-mcp`, `gitlab-mcp` and `email-mcp` network services over Streamable HTTP.
Provider credentials stay inside their MCP containers and are not available to this runtime.

Use [control-mcp](../control-mcp/README.md) for work status, queue management and steering. Requests for an explanation of the
programmer's current work use its separate question operation; instructions that change execution use steering.

The runtime uses the HTTP client and contracts from [core](../../packages/core/README.md) for message retrieval, session
associations, heartbeats, responses and execution reporting. It does not access PostgreSQL directly. The orchestrator admits
registered Telegram users and handles the standard denial for an unknown user.

## Capacity and state

The initial installation runs one chat-agent instance. It owns a configurable local limit on concurrently executing regular
conversations. Messages for an already executing conversation can be delivered through steering without another regular
turn slot.

When a new turn has no slot, retain its input in the queue and generate a short busy acknowledgement using a very lightweight,
Luna-class model. This generation has no tools and a separate small concurrency limit. It does not replace the user's primary
session; normal processing follows when capacity becomes available. Submit the acknowledgement through the same response API.

The orchestrator owns durable message and session associations. This service owns Codex runtime context. Execution logs are
stored in the database with a common run context and a separate service identity; they are not a shared filesystem log feed.

## Boundaries

The chatter discusses requirements and coordinates work. Development, task working-copy changes and commands in task
repositories belong to [coding-agent](../coding-agent/README.md). GitLab MCP access does not permit the chatter to edit task
source code through the API instead. Coding workflow and completion decisions also belong to the programmer's workflow.
