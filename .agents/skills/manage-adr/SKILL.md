---
name: manage-adr
description: Create, amend and supersede Taskin architecture decision records in .plans. Use for maintaining decision records, not for planning or implementing a development stage.
---

# Manage ADR

Communicate in Russian and write documents in English. Read [the ADR format](references/adr-format.md) when creating, amending or checking a record. This skill owns ADR format and maintenance; stage planning and execution remain separate workflows.

## Select the scope

- Use the plan ID or path supplied by the user or clearly established in the conversation. Ask when the target is ambiguous; do not select the newest plan or create a stage implicitly.
- Read applicable `AGENTS.md` instructions, the relevant current-plan requirements and only the ADRs needed for the requested work. Preserve scope passed by another skill. Do not read other plans or follow historical links merely to allocate an ID or maintain a backlink.
- Use `$search-adr` when an existing decision must be located, passing the authorized plan scope. Cross-plan lookup requires that scope to be explicitly authorized; a reference to an older decision is not permission to open or edit its plan.
- Record decisions established by the user or the authorized task. Do not invent architecture, implementation work or replacement relationships to fill a template.

## Create or amend

1. For a new record, list ADR directory names inside the selected plan and choose the next unused four-digit decision number, starting at `0001`. Combine it with the plan ID and keep the resulting ID stable. Listing names does not require reading unrelated records.
2. Creating ADRs as part of a newly authorized plan needs no extra confirmation. Before modifying existing ADR or plan documents, show the concrete proposed edits and their effects, then wait for confirmation. A specific change already confirmed in this conversation needs no second confirmation. If a new ADR would change an existing stage's assignment or completion criteria, obtain confirmation of that concrete plan amendment first.
3. Write the record at the canonical location and keep both replacement fields. Preserve unrelated content. For supersession, create the replacement and amend the old record only within the confirmed scope, preserving its original rationale. Do not silently edit another plan or reopen a completed stage.
4. Check the ID, directory, metadata and heading agree; check required sections, relation encoding and status consistency. Check relevant links available within the authorized scope. Historical references outside that scope remain metadata.

## Refresh and report

After saving ADR changes, run from the repository root inside the devcontainer:

```bash
export XDG_CACHE_HOME="$PWD/.qmd/cache"
npm exec -- qmd update
npm exec -- qmd embed
```

This refreshes the search index; it does not authorize further document amendments. Check the update output for source-read errors and verify that the saved record is indexed; QMD can exit successfully after reporting file-read errors. If QMD is unavailable, report that the ADR was saved but the index is stale. Do not claim semantic indexing or search succeeded without a successful result.

Report the created or changed IDs, record paths and index-refresh outcome in Russian. This workflow does not create a stage `.done` marker or implement the decisions.
