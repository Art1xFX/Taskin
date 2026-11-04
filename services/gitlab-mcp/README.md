# GitLab MCP

`taskin-gitlab-mcp` provides the agents with tools for GitLab API operations through Taskin's own account.

This document defines the agreed service boundary. The service implementation is not present yet.

## Responsibilities

- Translate MCP tool calls into GitLab provider requests and return usable results.
- Expose GitLab entities needed by workflows, such as repository metadata, merge requests, discussions and pipelines.
- Handle provider connection failures and translate technical errors at the integration boundary.
- Authenticate to GitLab inside this service's container.

The concrete tool set follows workflow needs. Completion criteria and the order of task actions belong to the agent's
workflow skills.

## Runtime and interfaces

Use the official Python MCP SDK. Run as a shared, long-running service in its own container, with Streamable HTTP
access. Both [chat-agent](../chat-agent/README.md) and [coding-agent](../coding-agent/README.md) connect to it. Their
LLMs choose tool calls. The orchestrator does not call this MCP in the initial scope.

GitLab credentials remain inside the MCP container and are not made available to either agent. Requests use Taskin's
account rather than each human user's credentials.

## State and boundaries

The coding service owns working copies, branches, file changes, command and test execution, commits and native Git push.
This MCP handles GitLab API entities and does not become a repository execution worker. The chatter's access supports
conversation and coordination; it does not give the chatter responsibility for changing task source code.

The initial scope uses the provider permissions of Taskin's account. Additional per-call Taskin authorization callbacks
and a possible future `PreToolUse` hook are deferred. Taskin scheduling, database ownership and Codex session management
remain outside this service.

Preserve run and service context for execution logs stored in the database.
