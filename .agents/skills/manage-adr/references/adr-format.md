# Taskin ADR Format

Each decision is an English Markdown document at:

```text
.plans/<plan-id>/adr/<adr-id>/README.md
```

`plan-id` is `YYYYMMDD-NNNN`. `adr-id` is the full numeric ID `<plan-id>-<four-digit-decision-number>`, for example `20261103-0001-0001`. The final number is unique within its plan. Directory names and metadata use the numeric ID; the visible heading adds `ADR-`.

There is no root `ADR.md`, Registry or manually maintained duplicate index. Related files may share the decision's directory, but the record remains `README.md`.

## Frontmatter

Start the file with YAML frontmatter under `qmd.metadata`. All fields below are required. These are Taskin's metadata names, not reserved QMD field names.

| Field | Value |
|---|---|
| `type` | The string `adr`. |
| `adr_id` | The full numeric decision ID, quoted as a string. |
| `plan_id` | The owning plan ID, quoted as a string. |
| `title` | A concise English title describing the decision. |
| `date` | The decision date as a quoted `YYYY-MM-DD` string; preserve it when superseding an existing decision. |
| `status` | Exactly `Accepted` or `Superseded`. |
| `replaces` | A nonempty list of full numeric IDs, or the quoted string `"None"` when this decision replaces no record. |
| `replaced_by` | A nonempty list of full numeric IDs, or the quoted string `"None"` when this decision has no replacement. |

Both relationship keys must be present. The quoted `"None"` sentinel deliberately represents no relation: QMD metadata does not accept null or empty arrays. Do not write YAML null, an empty list or a fictitious ID. Consumers must treat this sentinel as no relationship rather than a decision ID.

Use `Accepted` with `replaced_by: "None"`. A `Superseded` record has at least one `replaced_by` ID. A replacement can itself later be superseded, so both lists can be populated. References use numeric IDs without the heading's `ADR-` prefix. Do not include the record's own ID in either list.

## Markdown body

Use exactly one H1, `ADR-<adr-id>`, without backticks. The only H2 sections are `Context` and `Solution`.

`Context` explains the problem and relevant constraints. `Solution` explains the chosen behavior and its rationale. Include necessary tradeoffs or consequences in these two sections. Do not repeat the frontmatter as a metadata list or introduce extra headings.

## Example

The following fictional record replaced an earlier decision and was subsequently superseded, so both relationship lists are populated.

````markdown
---
qmd:
  metadata:
    type: adr
    adr_id: "20261103-0001-0001"
    plan_id: "20261103-0001"
    title: "Shared core package"
    date: "2026-11-03"
    status: Superseded
    replaces:
      - "20261102-0001-0004"
    replaced_by:
      - "20261103-0002-0001"
---

# ADR-20261103-0001-0001

## Context

Several services need the same data contracts. Independent definitions could
diverge between services.

## Solution

Define shared data contracts in the taskin-core package. Service packages import
these contracts from taskin-core.
````
