# Email MCP

`taskin-email-mcp` provides tools to read and send email messages through Taskin's own account.

This document defines the agreed service boundary. The service implementation is not present yet.

## Responsibilities

- Read email messages on request.
- Send email messages on request.
- Translate these MCP requests and their results at the mail provider boundary.
- Handle provider connection failures and technical errors.
- Authenticate to the mail provider inside this service's container.

Reading and sending messages are the complete functional scope of this adapter.

## Runtime and interfaces

Use the official Python MCP SDK. Run as a shared, long-running service in its own container, with Streamable HTTP
access. Both [chat-agent](../chat-agent/README.md) and [coding-agent](../coding-agent/README.md) connect to it. The
programmer may need email operations when testing message sending. Each agent's LLM chooses when its workflow needs an
email tool.

The orchestrator does not call this MCP in the initial scope. Provider credentials remain inside the MCP container and
are not made available to the agents.

## State and boundaries

The service does not independently monitor mailboxes, create Taskin tasks or choose an email-processing workflow. It
does not own Taskin queues, identity records or Codex sessions.

The initial scope uses the provider permissions of Taskin's account. Additional per-call Taskin authorization callbacks
and a possible future `PreToolUse` hook are deferred.

Preserve run and service context for execution logs stored in the database.
