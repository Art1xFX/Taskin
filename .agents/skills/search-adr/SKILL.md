---
name: search-adr
description: Find and read Taskin architecture decisions by ID or meaning through the project's QMD MCP server. Use for architectural rationale and decision history; ADR edits belong to manage-adr.
---

# Search ADR

Communicate in Russian. Use the project MCP server `qmd` to locate decisions, then read the relevant source Markdown before relying on it. ADR files are authoritative; the QMD index is a rebuildable search aid.

## Scope and discovery

- Preserve the scope passed by the user or calling workflow. When called by `create-plan` or `execute-plan`, restrict all searches and reads to the selected plan. Those workflows do not authorize reading older or sibling plans through an index.
- If a full ADR ID is given, derive its plan ID and exact source path using the format owned by [manage-adr](../manage-adr/SKILL.md). Read that source directly if authorized. A known ID does not need semantic search; a missing file is not permission to accept a fuzzy match from another plan.
- For a topic search without a narrower scope, search the repository's ADR collection. Do not load unrelated implementation-plan documents.
- Discover the available tools of MCP server `qmd` and inspect their schema. Read [QMD usage](references/qmd.md) for the request shape, setup, refresh commands or troubleshooting. Tool prefixes depend on the client.

## Search and read

1. Query collection `taskin-adrs`. Filter `type = adr` and normally `status = Accepted`; add an exact `plan_id` filter when a plan is selected. Apply scope filters in the query, before receiving results. If that is unavailable, use a scoped filesystem search rather than a broader query followed by filtering.
2. Combine a lexical query for identifiers or technical names with a semantic query describing the question. Prefer English search text for English ADRs; check the original meaning of Russian questions. For history or an explicit superseded ID, include the relevant statuses intentionally.
3. Read a small set of relevant results with `get`, or read their exact source files. Collection-relative paths map under `.plans/`. Check that every path remains inside the authorized plan or repository scope, including after symlink resolution.
4. Verify the current source's ID, status and replacement links. Follow replacements only within authorized scope; report an out-of-scope reference without opening it. Do not treat a stale indexed status as current. Accepted design does not itself prove its stage has been implemented.
5. Answer with the decision IDs, conclusion and links to the source files. Report missing evidence or conflicting metadata. If a change is requested, hand off to `manage-adr`; search alone does not authorize editing decisions.

## Index availability

Use `status` to check the index when it is missing, stale or recently changed. Refresh with the native QMD commands in the usage reference when needed. After a failed embedding refresh, report that semantic search is incomplete and use scoped lexical/source lookup if it can answer the question. Distinguish no match from an unavailable tool, missing model or stale index. Do not silently migrate old ADR formats or rewrite source documents to repair search.
