# Control MCP

`taskin-control-mcp` exposes tools for managing Taskin work through the
[orchestrator HTTP API](../orchestrator/README.md).

This document defines the agreed service boundary. The service implementation is not present yet.

## Responsibilities

- Read task, queue and execution status.
- Change priorities and enqueue work for an existing Redmine issue.
- Request task pause, resume, cancellation and switching.
- Submit steering messages for an agent's main session.
- Request an explanation about the programmer's current work and return the answer to the chatter.

These operations use the orchestrator client and shared contracts from [core](../../packages/core/README.md).
The orchestrator owns authorization, assignment rules and resulting state changes.

## Runtime and interfaces

Use the official Python MCP SDK. Run as a shared, long-running service in its own container, with Streamable HTTP
access.
Only [chat-agent](../chat-agent/README.md) connects to this MCP in the initial scope.

Forward trusted caller context supplied by the agent runtime. Caller identity is not an arbitrary argument chosen by the
model. The orchestrator API performs the final access checks.

An explanatory question is routed through the orchestrator to [coding-agent](../coding-agent/README.md). The coding
service owns the read-only, ephemeral fork of its current session and the answer it produces. Changes to execution use
steering.

## State and boundaries

The server does not access Taskin's database, run Codex, create session forks or execute development work. It translates
management tool calls into API requests; the coding service receives its assignments through its own Python API client.
User and project configuration belongs to the administration interface.

Keep run and service context on management operations so execution logs can be stored in the database and inspected
together with the participating services' logs.
