# Run Display Labels

- **Plan:** `20261103-0001`
- **Date:** 2026-11-03

## Goal

Provide one shared definition of a coding-run display label and use it when the coding agent formats run names. Explicit names must be preserved; unnamed runs must have an identifiable fallback.

This is a fictional format example, not a stage selected for implementation in Taskin. It assumes the uv workspace, source packages, Pydantic and pytest already exist.

## Scope

- Core defines the shared label model and missing-name behavior.
- Coding-Agent formats labels through that shared contract.
- Other projects are unaffected by this example.

## Projects

| Project | Source root | Plan |
|---|---|---|
| `packages/core` | `packages/core/src/taskin_core` | [Core](packages/core.md) |
| `services/coding-agent` | `services/coding-agent/src/taskin_coding_agent` | [Coding-Agent](services/coding-agent.md) |

The [Core plan](packages/core.md#codingrunlabel) owns the shared definition, and the [Coding-Agent plan](services/coding-agent.md#format_run_label) describes its consumer.

## Implementation order

1. Define `CodingRunLabel` and its `get_name` method in Core.
2. Add the Core checks described in its project plan.
3. Implement `format_run_label` in Coding-Agent using the shared model.
4. Add the Coding-Agent consumer checks.
5. Run the checks below and report their actual results in the conversation.

## Checks

Run commands from the repository root. The example assumes the workspace and its Pydantic and pytest dependencies are already configured; adapt commands to the actual workspace when preparing a real plan.

| Check | Command or action | Expected result |
|---|---|---|
| Core contract | `uv run --package taskin-core pytest packages/core/tests/test_models.py` | Missing-name fallback and explicit-name preservation checks pass. |
| Coding-Agent consumer | `uv run --package taskin-coding-agent pytest services/coding-agent/tests/test_services.py` | Explicit names are preserved and unnamed runs receive distinct UUID-based labels. |
| Change review | Review changed files against both project specifications. | The model, method, formatter and checks implement the specified shared behavior. |

## Completion criteria

- Both projects use the same shared label definition.
- Explicit names are returned unchanged, including surrounding whitespace.
- `None` and an empty name use the caller-provided fallback.
- The coding-agent fallback includes the run's UUID.
- All required checks have passed and their actual results have been reported.
- Only then is an empty `.done` file created in this plan directory.
