# ADR

## Registry

| ID | Title | Status | Date |
|---|---|---|---|
| `20261103-0001-0001` | Shared run label definition | `Accepted` | 2026-11-03 |

## `20261103-0001-0001`

- **Title:** Shared run label definition
- **Date:** 2026-11-03
- **Status:** `Accepted`
- **Replaces:** None
- **Replaced by:** None

### Context

Core and Coding-Agent need consistent handling of optional run names. Independent implementations could disagree about empty names or change explicit names. The formatting caller also needs a fallback that identifies its particular run.

### Solution

Define `CodingRunLabel` in Core and keep its field and method requirements authoritative in [the Core plan](packages/core.md#codingrunlabel). The model returns an explicit non-empty name unchanged and accepts a caller-provided fallback for missing or empty names. Coding-Agent supplies a fallback containing the run UUID, as described in [the Coding-Agent plan](services/coding-agent.md#format_run_label). This decision defines a Pydantic data contract and does not introduce a database table.
