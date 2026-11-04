---
name: manage-adr
description: Record agreed Taskin architecture decisions in project-wide .adr or a stage's .plans directory, including decisions established in conversation outside plans. Create, amend and supersede records; stage planning and implementation remain separate workflows.
---

# Manage ADR

Communicate in Russian and write documents in English. Read [the ADR format](references/adr-format.md) when creating, amending or checking a record. This skill owns ADR format and maintenance; stage planning and execution remain separate workflows.

## Select the scope

- A decision belonging to a development stage goes in that plan's ADR directory. Use the plan ID or path supplied by the user or established in the conversation; do not select the newest plan implicitly.
- An architectural decision established with the user outside a plan goes in `.adr/`, including during ordinary conversation or work on an unrelated stage. Record it as part of the ongoing task without requesting a plan ID, creating a stage or asking again whether to save the agreed decision. Proposed alternatives and assumptions do not become accepted ADRs.
- Route by the decision's actual scope, not merely by whether a plan is active. Reuse an existing applicable decision instead of copying it between project and plan directories. If ownership is genuinely ambiguous, clarify only that ambiguity.
- Read applicable `AGENTS.md` instructions, relevant selected-plan requirements and only the ADRs needed for this work. Preserve explicit reading restrictions. Relevant project ADRs are available during plan work; previous or sibling plans are not authorized by that exception.
- Use `$search-adr` when an existing decision must be located, passing the permitted project and plan scopes. Do not follow a link into another plan merely to allocate an ID, inspect history or maintain a backlink.
- Record only established decisions and their actual rationale. Do not invent architecture, implementation work or replacement relationships, or backfill unrelated earlier conversation decisions automatically.

## Create or amend

1. Allocate the next unused ID from directory names in the chosen scope using the ADR format. Plan records retain `<plan-id>-NNNN`; project records use `YYYYMMDD-NNNN` without an owning plan. Keep assigned IDs stable and never overwrite an existing directory.
2. Creating a project ADR for an agreed out-of-plan decision, or ADRs as part of a newly authorized plan, needs no extra confirmation. Before amending existing records or plan documents, show concrete proposed changes and obtain confirmation unless those specific changes are already established by the user's instruction. A confirmed replacement authorizes its in-scope status and relationship updates without another approval round.
3. Write the record at its canonical location. Include `replaces` or `replaced_by` only when the corresponding relationship exists, as a nonempty list of IDs; otherwise omit that field. For project records, create `.adr/` if needed and ensure `.gitignore` contains `/.adr/`. Preserve unrelated content. Do not force-add local records to Git.
4. For supersession, create the replacement and amend the old record only within the confirmed scope, preserving its original rationale. Links may cross scopes, but a project record does not authorize reading or editing an older plan. Leave out-of-scope links as metadata and report any reciprocal update that still needs authorization.
5. A new project ADR does not amend a stage's assignment or completion criteria. If it conflicts with the selected plan, obtain confirmation of the concrete plan amendment before changing that plan or implementing the conflicting behavior. Do not silently reopen a completed stage.
6. Check the ID, directory, metadata and heading agree; check required sections, relation encoding and status consistency. Check relevant links available within the authorized scope. Historical references outside that scope remain metadata.

## Refresh and report

After saving ADR changes, run from the repository root inside the devcontainer:

```bash
export XDG_CACHE_HOME="$PWD/.qmd/cache"
npm exec -- qmd update
npm exec -- qmd embed
```

This refreshes the search index; it does not authorize further document amendments. Check the update output for source-read errors and verify that the saved record is indexed; QMD can exit successfully after reporting file-read errors. If QMD is unavailable, report that the ADR was saved but the index is stale. Do not claim semantic indexing or search succeeded without a successful result.

Report the created or changed IDs, record paths and index-refresh outcome in Russian. This workflow does not create a stage `.done` marker or implement the decisions.
