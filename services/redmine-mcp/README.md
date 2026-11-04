# Redmine MCP

`taskin-redmine-mcp` provides the agents with tools for accessing Redmine through Taskin's own account.

This document defines the agreed service boundary. The service implementation is not present yet.

## Responsibilities

- Translate MCP tool calls into Redmine provider requests and return usable results.
- Provide Redmine issue, project and user operations required by the agents' workflows.
- Handle provider connection failures and translate technical errors at the integration boundary.
- Authenticate to Redmine inside this service's container.

The concrete tool set follows workflow needs. This adapter does not decide how an issue should be executed or when work
is complete.

## Runtime and interfaces

Use the official Python MCP SDK. Run as a shared, long-running service in its own container, with Streamable HTTP
access. Both [chat-agent](../chat-agent/README.md) and [coding-agent](../coding-agent/README.md) connect to it. Their
LLMs decide when to invoke tools. The orchestrator does not call this MCP in the initial scope.

Redmine credentials remain inside the MCP container and are not made available to either agent. Human Redmine ID
associations do not mean that requests use each person's credentials.

## State and boundaries

The [orchestrator](../orchestrator/README.md) owns incoming Redmine webhooks, Taskin identity associations and work
queues. This service does not enqueue Taskin assignments, manage Codex sessions or own the Taskin database schema.

The initial scope uses the provider permissions of Taskin's account. Additional per-call Taskin authorization
callbacks and a possible future `PreToolUse` hook are deferred.

Preserve run and service context for execution logs stored in the database. Shared filesystem log storage is not
required.
