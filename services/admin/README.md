# Administration

The Django administration service provides configuration editing and inspection of Taskin work.

This document defines the agreed service boundary. The service implementation is pending.

## Responsibilities

- Read and edit Taskin users and their Telegram, GitLab and Redmine ID associations directly in the database.
- Maintain projects, repositories and settings through the same direct database access.
- Display executions, agent assignments and queue state for inspection.
- Display database-backed execution logs grouped by run and originating service.
- Authenticate administration users through Django's own user system.
- Route any operational actions added to the interface through the orchestrator API.

## Data ownership

The [orchestrator](../orchestrator/README.md) owns the Taskin domain schema, SQLAlchemy models and domain migrations.
Generate the corresponding Django models from the existing database with `inspectdb`, keep them unmanaged with
`managed = False`, and commit their generated source. Regenerate them during development when the domain schema changes.
Keep administration customization separate from the generated models.

Django owns its administration login users and framework-managed tables and migrations. Administration login users and
Taskin users are separate identities: a Django login does not replace the cross-system Taskin identity record.

## Interfaces

Configuration editing uses the database directly. Executions, assignments, queue state and execution logs are read-only
administration views. Actions that pause, resume, cancel or otherwise change execution use the orchestrator API, where
assignment transitions and access checks are owned. The shared API contracts and client are provided by
[core](../../packages/core/README.md).

Execution logs are stored in the database with a common run context and entries separated by service. The interface reads
those records; it does not depend on a shared filesystem log volume or access to Codex session files.

## Boundaries

- Configuration CRUD does not grant this service ownership of scheduling, assignment or queue-transition rules.
- Django migrations must not create or alter the orchestrator-owned Taskin schema.
- Codex execution, agent concurrency and provider integration calls belong to their respective services.
- Operational actions are an extension point, not an already implemented feature.
