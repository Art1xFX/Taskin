---
name: create-plan
description: Create or propose confirmed amendments to a Taskin development-stage plan in .plans, with file-by-file service and package specifications. Use for planning, not for implementing a stage.
---

# Create Plan

Communicate in Russian and write plan documents in English. Read [the canonical format](references/plan-format.md) when creating a plan or when a format rule needs clarification. Read individual examples only as needed: [README](references/example-plan/README.md), [service](references/example-plan/services/coding-agent.md), [package](references/example-plan/packages/core.md). Examples illustrate a fictional feature; do not copy their architecture or scope into a real plan.

## Read only the relevant context

- Read the repository README, relevant code and configuration, and the READMEs of affected projects and direct dependencies whose contracts matter.
- Find and obey every applicable `AGENTS.md` along ancestor paths to files being planned or edited, including intermediate and nested directories. Do not scan unrelated projects merely to collect instructions.
- When amending a selected existing plan, read its README, affected project documents and relevant definitions inside that same plan. Use `$search-adr` only when architectural rationale or a delegated decision is needed, restricting lookup to the selected plan. Delegate decision creation or amendment to `$manage-adr` within the same authorized scope and confirmation rules.
- Do not open previous or sibling plans, even through ADR or contract links. Listing `.plans/` directory names to allocate or select an ID is allowed; reading other plans' contents is not. Historical links are metadata only. Use current repository documents, current code and the user's context for established behavior.
- If the current plan depends on missing information, ask for it; do not recover it by reading another plan or silently inventing requirements.

## Create a new stage

1. Establish the requested outcome and affected projects. Resolve file locations, dependencies, field types, defaults, constraints, callable parameters and results, errors, ordering, concurrency and side effects where applicable. Check uncertain third-party capabilities against the version used by the project.
2. Use the creation date supplied by the client in `Europe/Chisinau`. Follow an existing numbering convention if specified. Otherwise, list valid `YYYYMMDD-NNNN` directory names and use the largest observed numeric suffix plus one, starting at `0001`. Never read those directories for numbering. Do not wrap after `9999`; request a numbering decision if necessary.
3. Create a fresh `.plans/<plan-id>/` directory without overwriting an existing plan. If the chosen name already exists, select the next unused number from directory names only. Preserve the chosen ID thereafter.
4. Ensure the root `.gitignore` excludes `/.plans/`, preserving existing rules. Do not force-add local plans to Git.
5. Write the root README, plus `services/<directory-name>.md` and `packages/<directory-name>.md` for affected projects. Delegate required architecture decision records to `$manage-adr` for this plan. Define shared interfaces in their owning project's document and link consumers to that definition. Put required checks in the root README. Do not create separate contract or verification documents, or a `projects/` directory.
6. Follow the canonical file and entity format. Derive projects and source roots from the actual uv workspace. Use `pyproject.toml`; do not presume SQLAlchemy models must live in Core or add Dockerfiles without a stage requirement.

## Amend an existing plan only after confirmation

Before changing any existing plan document, show the user the concrete proposed changes, affected files and their effect on scope or completion criteria, then wait for confirmation. This includes wording corrections, file renames or deletions, ADR changes and adding check results. A broad request to review, refine or execute a plan does not approve an unseen amendment. A concrete change already explicitly confirmed in this conversation does not need another confirmation.

Apply only the confirmed changes, keeping the plan ID and unrelated content. If the confirmed amendment reopens a completed stage, include removal of its `.done` marker in the proposal and remove it only after that reopening is confirmed. A wording-only correction does not reopen the stage. Keep delegated ADR work within the selected plan and the confirmed changes.

## Check and finish

- Ensure every planned file resolves unambiguously and every entity has a purpose description and precise requirements. Model and parameter tables use `Name`, `Type`, `Description`; callable results use a separate `**Returns:**` paragraph. Do not add standalone signatures or generic process wrappers to project documents.
- Check same-plan links, shared-definition ownership, implementation order, expected check outcomes and completion criteria. Delegate ADR validation to `$manage-adr`. Do not follow cross-plan links while checking references.
- Put unresolved decisions in the current README's `Open questions` section and explain the affected scope. A plan with unresolved required behavior is not ready for that part of implementation.
- Never create `.done` during planning. Do not implement a stage merely because its plan was requested. If implementation was also authorized, continue with the execution workflow after planning; an existing-plan amendment still needs its concrete confirmation.
- Report the plan ID, affected projects, readiness and remaining decisions in Russian. Do not create or update the repository's architecture documentation as an incidental part of this workflow.
