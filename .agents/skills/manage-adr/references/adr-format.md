# Taskin ADR Format

Each decision is an English Markdown document in its own directory. Decisions belonging to a development stage remain at:

```text
.plans/<plan-id>/adr/<adr-id>/README.md
```

`plan-id` is `YYYYMMDD-NNNN`. A plan ADR's full numeric ID is `<plan-id>-<four-digit-decision-number>`, for example `20261103-0001-0001`. Choose the next unused number within that plan, starting at `0001`.

Decisions established outside a plan use the project-wide directory:

```text
.adr/<adr-id>/README.md
```

A project ADR's numeric ID is `YYYYMMDD-NNNN`, for example `20261104-0001`. Use the creation date supplied by the client in `Europe/Chisinau` and the largest existing project ADR numeric suffix plus one, starting at `0001`. This sequence is independent of plan numbering and does not reset with the date. Allocate IDs by listing directory names only, without reading unrelated records. Do not wrap after `9999`; request a numbering decision if necessary. Keep each chosen ID stable and never overwrite an existing record.

Directory names and metadata use the numeric ID; the visible heading adds `ADR-`. Project and plan ADRs share the body and relationship format. A project ADR has no `plan_id`; do not create a fictional plan to store it.

There is no root `ADR.md`, Registry or manually maintained duplicate index. Related files may share the decision's directory, but the record remains `README.md`.

## Frontmatter

Start the file with YAML frontmatter under `qmd.metadata`. The fields `type`, `adr_id`, `title`, `date` and `status` are required. `plan_id` is required only for plan ADRs and omitted for project ADRs. The relationship fields `replaces` and `replaced_by` are optional and must be omitted when no corresponding relationship exists. These are Taskin's metadata names, not reserved QMD field names.

| Field         | Value                                                                                                  |
| ------------- | ------------------------------------------------------------------------------------------------------ |
| `type`        | The string `adr`.                                                                                      |
| `adr_id`      | The full numeric decision ID, quoted as a string.                                                      |
| `plan_id`     | The owning plan ID, quoted as a string, for plan ADRs only; omit this key from project ADRs.           |
| `title`       | A concise English title describing the decision.                                                       |
| `date`        | The decision date as a quoted `YYYY-MM-DD` string; preserve it when superseding an existing decision.  |
| `status`      | Exactly `Accepted` or `Superseded`.                                                                    |
| `replaces`    | Optional nonempty list of full numeric IDs replaced by this decision; omit when it replaces no record. |
| `replaced_by` | Optional nonempty list of full numeric replacement IDs; omit when this decision has no replacement.    |

An absent relationship key means no relationship. When present, each field must be a nonempty list of IDs. Do not use placeholder strings such as `"None"`, YAML null, empty lists or fictitious IDs. QMD supports omitted metadata keys.

An `Accepted` record omits `replaced_by`. A `Superseded` record has at least one `replaced_by` ID. A replacement can itself later be superseded, so both lists can be populated. References use numeric IDs without the heading's `ADR-` prefix. A two-part ID resolves under `.adr/`; a three-part ID resolves under its owning `.plans/<plan-id>/adr/`. Do not include the record's own ID in either list. A cross-scope reference does not authorize reading or changing its target.

## Markdown body

Use exactly one H1, `ADR-<adr-id>`, without backticks. The only H2 sections are `Context` and `Solution`.

`Context` explains the problem and relevant constraints. `Solution` explains the chosen behavior and its rationale. Include necessary tradeoffs or consequences in these two sections. Do not repeat the frontmatter as a metadata list or introduce extra headings.

## Project example

This fictional project record lives at `.adr/20261104-0001/README.md` and has no owning plan.

```markdown
---
qmd:
  metadata:
    type: adr
    adr_id: "20261104-0001"
    title: "Capture project decisions outside development stages"
    date: "2026-11-04"
    status: Accepted
---

# ADR-20261104-0001

## Context

Architecture discussions can establish decisions before a development stage exists.
Those decisions need a stable source for later planning.

## Solution

Record agreed decisions outside plans in `.adr/`. Keep stage decisions with their
plans and index both sources with QMD.
```

## Plan example

The following fictional record replaced an earlier decision and was subsequently superseded, so both relationship lists are populated.

```markdown
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
```
