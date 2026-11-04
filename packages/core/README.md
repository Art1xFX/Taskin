# Core

Core is the shared Python package for Taskin API contracts and the asynchronous orchestrator HTTP client.

This document defines the agreed package boundary. Package implementation is pending.

## Responsibilities

- Define shared Pydantic request and response models for the orchestrator API.
- Define statuses and API errors used by those contracts.
- Provide an asynchronous HTTP client for calling the orchestrator API.
- Give API consumers a consistent interface without exposing the database representation.

## Consumers

The [orchestrator](../../services/orchestrator/README.md) implements the API and owns its persistent state. It uses the
shared contracts while retaining its service-specific business rules and database models.

The [chat agent](../../services/chat-agent/README.md) and [coding agent](../../services/coding-agent/README.md) use the client
from their Python runtimes to obtain work, exchange messages and commands, submit heartbeats and report results.

[Control MCP](../../services/control-mcp/README.md) translates tool calls into orchestrator API requests using this client.
The client is for the orchestrator HTTP API; it is not a library for connecting agents to the control MCP server.

Other API consumers, including any [administration](../../services/admin/README.md) operational actions, can reuse the same
contracts and client.

## Boundaries

- SQLAlchemy models, persistence, domain migrations and durable queue handling belong to the orchestrator.
- Assignment selection, authorization decisions and task workflows remain with their owning services and workflow skills.
- Codex session execution and agent concurrency belong to the agent services.
- MCP servers and external-provider adapters belong to their service projects.
- FastAPI routes and Django models or administration views are service implementations, not shared API contracts.

Core is a library consumed by other projects. It has no standalone service process or independently owned database.
