---
name: execute-plan
description: Read and execute a selected Taskin stage plan from .plans, verify its implementation and create the empty .done marker after full completion. Existing-plan amendments require user confirmation before writing.
---

# Execute Plan

Communicate in Russian. Implement only the user's authorized scope of the selected plan. Keep plan documents unchanged unless the user has confirmed concrete amendments.

## Select and read

1. Use the plan ID or path specified by the user or clearly established in the conversation. If the selection is ambiguous, ask for the ID; do not choose the newest stage. The selected directory belongs under this repository's `.plans/`.
2. Inspect `.plans/` directly because it is ignored by Git. Directory names may be listed, but do not read previous or sibling plans, including their READMEs, ADRs or `.done` markers. Do not follow cross-plan links; they are historical metadata, not sources for this execution.
3. Read the selected plan's complete README and all service/package documents in the authorized scope. Read shared definitions in their owning project's document. For full-stage execution read every project document listed in the README. For an explicitly partial execution, also read referenced definitions within the same plan when needed, but do not expand implementation to their unrelated work.
4. Read ADR only when a required rule is delegated to a decision entry, rationale is needed to resolve a requirement conflict, or an ADR amendment must be proposed. Read only the relevant entries and same-plan replacement chain. A link alone, including an entry link, does not require reading ADR when the needed behavior is already specified elsewhere. Do not perform a blanket ADR audit as part of ordinary execution.
5. Read the relevant current repository/project READMEs, actual code and configuration. Find all applicable `AGENTS.md` along ancestor paths to files being read or changed, including intermediate and nested directories. Read direct dependency documentation only when it governs the affected interface.
6. Use the README's source-root mapping, implementation order, checks and completion criteria. Project documents group entities by actual files; methods belong to their enclosing classes. When a decision is relevant, honor its current `Accepted` entry rather than a superseded choice; follow replacement links only inside this plan. If the README's project list disagrees with the plan documents, propose a confirmed correction instead of silently skipping or expanding work. Do not load format instructions or examples routinely.

The writer owns [the document format](../create-plan/references/plan-format.md). Consult it only when interpretation is genuinely unclear or when preparing an amendment. Optional examples are bundled here: [README](references/example-plan/README.md), [ADR](references/example-plan/ADR.md), [service](references/example-plan/services/coding-agent.md), [package](references/example-plan/packages/core.md). They clarify document structure and do not authorize adding their fictional feature.

## Handle incomplete or completed stages

- If the selected stage has unresolved required questions, missing required documents, broken same-plan definitions or conflicting requirements, explain the precise blocker and propose an amendment. Do not infer a replacement contract from an older plan. Continue only independent, unambiguous work; do not run checks whose required implementation is known to be blocked or absent, and report those checks as not run.
- Treat `.done` as completion metadata, not proof that implementation exists. If it is non-empty or not a regular file, report the inconsistency and request a confirmed correction rather than treating the stage as completed. If a valid marked stage satisfies the requested scope, report that instead of repeating it. If implementation is missing or renewed work would reopen the stage, request confirmation of the reopening and marker removal before making those changes.
- A partial-execution request does not authorize the rest of the stage and does not authorize creating `.done` for the entire stage. A stage left incomplete or blocked receives no new marker.

## Implement without rewriting the assignment

- Inspect existing code before each change to support resumption. Follow the specified file locations, models, parameters, defaults, return values, error behavior, async semantics and side effects. Preserve unrelated user changes.
- Resolve ordinary private implementation choices within the stated behavior. If implementation would change a documented requirement, field, interface, architectural decision, planned file location, check command or expected outcome, first show the concrete amendment and obtain user confirmation. Do not write the change into the plan or implement the conflicting behavior while approval is pending.
- The same gate applies to wording fixes, structural migrations of older document formats, and adding actual check results to a plan. A broad execution or refinement request is not confirmation of an unseen amendment. Previously confirmed concrete changes do not require another confirmation.
- When an amendment is confirmed, update only the approved parts of this plan before implementing them. Maintain replacement links only inside the selected plan; do not read or edit earlier plans.
- Do not weaken checks, omit requirements or change completion criteria to match an easier implementation. A failed check is a result to resolve or report, not permission to rewrite the expected result.
- Delegate independent implementation work when useful, passing the selected scope and same reading and amendment restrictions. Inspect and integrate the result before declaring completion.

## Check and finish

1. Run the required commands or manual checks from the current README that apply to the authorized scope, using the current workspace and normal execution permissions. For full-stage execution run every required check. For a partial request run the selected scope's checks and necessary dependency checks; report unrelated checks as outside this request rather than executing unfinished projects. If a command cannot run, report the reason. Correcting a documented command or its expected result is a plan amendment and needs confirmation first.
2. Review the result against every entity and completion criterion in the authorized scope, including any applicable decisions that were necessary to consult. This is not an instruction to load unrelated ADR entries. Missing implementations, failed required checks and required checks not run leave the stage incomplete.
3. Report actual commands, results and remaining work in the conversation. Do not automatically record results in README, ADR or a new log document inside the plan. Offer a concrete document amendment only when such a write is requested or needed, and wait for its confirmation.
4. For an authorized full-stage execution, create a zero-byte regular file `.done` only after the entire stage is implemented and every required check has succeeded. This completion-marker creation is the already-authorized stage lifecycle, not permission to edit plan contents. If `.done` already exists, do not overwrite a non-empty or non-regular marker; report the inconsistency and request a confirmed correction.
5. Report completion or partial progress in Russian. Do not claim completion solely from a marker, an unexecuted check or a subagent's assertion.

Executing a plan does not by itself authorize commits, publication or external changes. Use the user's existing authorization and execution permissions. Do not create or update repository architecture documents outside the selected scope.
