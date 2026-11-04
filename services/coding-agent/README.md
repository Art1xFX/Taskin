# Coding Agent

Taskin's development service executes work associated with Redmine issues through the Codex Python SDK. This document
defines the agreed service boundary; runtime implementation is pending.

## Responsibilities

- Obtain the next assignment from the [orchestrator API](../orchestrator/README.md), retrieve steering and control commands,
  send heartbeats and report questions, execution state and results.
- Own Codex session creation, continuation, steering and stopping. Stop obsolete execution when the API rejects its
  assignment.
- Own working-copy preparation, branches, file changes, command and test execution, commits and native Git push operations.
  Preserve the working state needed to return to a paused issue.
- Execute the configured task workflow skill. That skill defines task steps and completion criteria; the agent decides when
  the work is complete and reports the outcome. This service boundary does not prescribe an MR or CI completion checklist.

## Work and session ownership

The initial installation runs one coding-agent instance, executing one Redmine issue at a time. A Redmine issue is required
for development work and is the unit of active execution. Subagents may work within that issue.

Associate one primary Codex session with each issue and resume it when returning after a task switch or process restart.
The orchestrator stores the issue-to-session-ID association. This service manages the session and retains its Codex session
files on its persistent volume.

Use the HTTP client and contracts from [core](../../packages/core/README.md) for orchestrator interactions. This service does
not read or update PostgreSQL work queues directly. Local execution capacity belongs here; assignment selection and durable
queue priorities belong to the orchestrator.

## Questions and steering

Submit clarification questions through the orchestrator API, keeping each question and answer associated with its issue.
Pause the issue and release its coding slot only when the missing answer completely blocks further progress. Otherwise,
continue useful work on the same issue while awaiting clarification. A blocked issue returns through the normal assignment
mechanism with its execution context preserved.

An explanatory question from the chatter is a separate interaction. Prepare its answer in an ephemeral fork of the current
Codex session. The fork may inspect existing context but must not code or modify files. It belongs to the current issue and
does not replace the primary session. Changes to the ongoing execution use steering of the main session. This service owns
the fork's lifecycle; the concrete SDK mechanism is an implementation choice.

## External tools and state

Connect to `redmine-mcp`, `gitlab-mcp` and `email-mcp` over Streamable HTTP. Email access supports development work such as
testing message sending. `gitlab-mcp` provides GitLab API entities, including MR, discussions and pipeline information;
local repository operations remain here. `control-mcp` is connected only to the chatter.

Providers are authenticated with Taskin's accounts inside their MCP containers. The coding runtime must not receive those
provider secrets. Native Git authentication must satisfy the same isolation requirement; its mechanism has not been chosen.

Execution logs are stored in the database, correlated by run and separated by service. Persistent Codex session files serve
execution continuity and are not a shared filesystem log backend.
