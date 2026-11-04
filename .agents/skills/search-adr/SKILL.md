---
name: search-adr
description: Find and read project-wide and stage-specific Taskin architecture decisions by ID or meaning through the project's QMD MCP server. Use for architectural rationale and decision history; ADR edits belong to manage-adr.
---

# Search ADR

Communicate in Russian. Use the project MCP server `qmd` to locate decisions, then read the relevant source Markdown before relying on it. ADR files are authoritative; the QMD index is a rebuildable search aid.

## Scope and discovery

- Preserve the scope passed by the user or calling workflow. `create-plan` and `execute-plan` normally permit relevant project ADRs in `.adr/` and ADRs of the selected plan, but not previous or sibling plans. An explicit narrower instruction, such as searching only this plan, remains narrower.
- Resolve an exact numeric ID using the format owned by [manage-adr](../manage-adr/references/adr-format.md): project ID `YYYYMMDD-NNNN` maps to `.adr/<id>/README.md`; plan ID `YYYYMMDD-NNNN-NNNN` maps to `.plans/<plan-id>/adr/<id>/README.md`. Read the source directly only within the authorized scope. A missing record does not authorize a fuzzy match from another scope.
- For a topic search without a narrower scope or an active plan workflow, search both ADR collections. This does not authorize loading unrelated implementation-plan documents.
- Discover the available tools of MCP server `qmd` and inspect their schema. Read [QMD usage](references/qmd.md) for the request shape, setup, refresh commands or troubleshooting. Tool prefixes depend on the client.

## Search and read

1. Query `taskin-project-adrs` for project decisions and `taskin-plan-adrs` for plan decisions, selecting only authorized collections. Filter `type = adr` and normally `status = Accepted`. During selected-plan work, query these sources separately: project records have no `plan_id`, while the plan query must include the exact selected `plan_id`. Never remove that filter to recover a project match from the plan collection. Apply scope filters before receiving results; if unavailable, use a scoped filesystem search.
2. Combine a lexical query for identifiers or technical names with a semantic query describing the question. Prefer English search text for English ADRs; check the original meaning of Russian questions. For history or an explicit superseded ID, include the relevant statuses intentionally.
3. Read a small set of relevant results with `get`, or read their exact source files. Paths from `taskin-project-adrs` map under `.adr/`; paths from `taskin-plan-adrs` map under `.plans/`. Check that every path remains inside the authorized project or plan scope, including after symlink resolution.
4. Verify the current source's ID, status and replacement links. Follow replacements only within authorized scope; a project ADR linking to an older plan does not authorize opening that plan. Report out-of-scope references without reading them. Do not treat cached status as current or an accepted design as proof of implementation. If a relevant project decision conflicts with the selected plan, report the conflict and route a concrete plan amendment through its confirmation workflow.
5. Answer with the decision IDs, conclusion and links to the source files. Report missing evidence or conflicting metadata. If a change is requested, hand off to `manage-adr`; search alone does not authorize editing decisions.

## Index availability

Use `status` to check the index when it is missing, stale or recently changed. Refresh with the native QMD commands in the usage reference when needed. After a failed embedding refresh, report that semantic search is incomplete and use scoped lexical/source lookup if it can answer the question. Distinguish no match from an unavailable tool, missing model or stale index. Do not silently migrate old ADR formats or rewrite source documents to repair search.
