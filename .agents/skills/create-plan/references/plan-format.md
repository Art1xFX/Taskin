# Taskin Plan Format

Write documents in English and discuss them with the user in Russian. This format specifies plan documents, not the architecture of Taskin's services.

## Directory and lifecycle

```text
.plans/YYYYMMDD-NNNN/
├── README.md
├── services/
│   └── coding-agent.md
├── packages/
│   └── core.md
└── .done
```

README is required. Include service/package documents only for affected projects and name each file after its repository directory, without the distribution's `taskin-` prefix. There is no `projects/` directory or separate contract or verification file. Empty `services/` or `packages/` directories are unnecessary. Use `$manage-adr` for architecture decision records and `$search-adr` for relevant decision lookup within the authorized plan scope. Those skills own ADR format and lifecycle.

`.done` is absent during planning and partial implementation. Full successful execution creates it as an empty regular file. Changing existing plan content requires prior user confirmation of the concrete amendment. Removing a completed stage's marker requires a confirmed reopening or marker correction. Creating the completion marker after authorized successful full execution is already part of the agreed lifecycle.

Read only the selected current plan. Previous/sibling plans and historical ADR links must not be opened or modified. Listing plan directory names for ID allocation or selection is allowed. Keep required current-stage context self-contained; refer to current code or ask the user when a necessary fact is unavailable.

## README.md

Use an H1 stage title and these metadata items:

```markdown
- **Plan:** `20261103-0001`
- **Date:** 2026-11-03
```

Use these H2 sections:

- `Goal`: the overall observable result.
- `Scope`: the work included in this stage and relevant existing prerequisites.
- `Projects`: a table with `Project`, `Source root`, `Plan` columns. Project and source-root paths are repository-relative; Plan links are relative to the plan directory. Give the actual Python source root for each project with shorthand module headings. Use an explicit not-applicable value for projects without Python source changes.
- `Implementation order`: ordered dependencies between concrete entities/project changes in this plan.
- `Checks`: a table with `Check`, `Command or action`, `Expected result` columns. Commands run from the repository root unless another working directory is explicit. Identify the affected project or shared behavior in each check so partial execution can select the relevant checks. Include prerequisites and manual actions where needed. No Actual result column is required; execution reports outcomes in the conversation and may write them to a plan only after confirmation.
- `Completion criteria`: all conditions required before `.done` may be created.

If repository-wide files such as the root `pyproject.toml`, `uv.lock` or Compose configuration change, add `Repository files` to the README. Use H3 headings with their exact repository-relative paths, a purpose paragraph, and precise changes. This covers workspace files without pretending they belong to one service or adding another plan-file category.

Add `Open questions` only if requirements remain unresolved. Identify what those questions block. Do not present those affected parts as implementation-ready. Link to the current ADR where relevant and to shared definitions in their owning project's document.

## Service and package documents

Use the same structure in `services/<name>.md` and `packages/<name>.md`:

1. H1 is the project's display name, for example `Coding-Agent` or `Core`.
2. H2 identifies one actual file, not a category of entities.
3. H3 identifies an entity in that file. A method is nested under its class using a bold label such as **Method `get_name`**.

### Unambiguous file mapping

`Models` maps to `models.py` and `Services` to `services.py` directly under the Python source root declared in the README's Projects table. Other simple Python filename stems may use the same display convention when the mapping is unique: lower-case the words, join spaces with underscores and append `.py`.

For nested modules, tests, non-Python files, special names or any ambiguity, use the literal path relative to the project directory as H2. Examples are `tests/test_models.py`, `src/taskin_core/domain/models.py`, `pyproject.toml`, and `Dockerfile`. Do not infer a nested file from a vague heading. Use one section for each physical file; do not split the same file between multiple generic categories.

### Purpose and actions

Every entity and method begins with a short paragraph explaining why it is needed and what it will do in this stage. This includes tests, fixtures, configuration sections and Docker stages. A name or creation instruction alone is insufficient.

State whether to create, modify, remove or rename the entity. Put concrete behavior in prose or action bullets. For modifications identify what changes; for removals/renames include affected callers and required cleanup. Do not use unresolved placeholders in an implementation-ready plan.

Do not add generic Goal, Dependencies or Implementation Steps sections to a project document. H2 headings remain file locations; stage-level sequencing belongs in the README.

### Models and configuration

Model field tables use exactly `Name`, `Type`, `Description` columns. Put requiredness, nullability, defaults or default factories, validation, persistence details and constraints in descriptions. Specify inheritance and construction semantics in prose when relevant. For persistent models also specify table names, SQL types, relations, indexes and migration changes; do not presume every model is a database table.

Describe configuration/dependency changes under their actual file headings. Use an H3 for the concrete configuration object or section, with a purpose paragraph and exact keys/values or constraints. Do not add Python method tables to Dockerfile or configuration sections that have no callables.

### Functions, methods and tests

State sync/async and module/instance/class/static semantics in the purpose paragraph or action bullets. Parameter tables use exactly `Name`, `Type`, `Description`; put defaults, ordering and positional/keyword restrictions in descriptions when they matter. Types are code-formatted. Escape `|` in Markdown union types.

Omit a parameter table when no explicit arguments exist; implicit `self` and `cls` do not need rows. Parameters with no default are required. Distinguish `None` as a permitted value from an optional argument. Describe exceptions, side effects, transaction boundaries, ordering and retry behavior where applicable.

Give results a separate `**Returns:**` paragraph containing the return type and behavior; use `None` for no result. For an async callable this describes the value obtained after awaiting it. Do not add a standalone signature or `Signature:` line. Document properties and custom constructors as owned entities with their own purpose and relevant field/parameter details.

Tests have a purpose paragraph explaining the behavior protected, concrete setup/actions/assertions, and parameter details if fixtures are passed. Plan checks appropriate to the change rather than tests that merely repeat implementation syntax.

### Shared definitions

Define each shared model, API, protocol or queue message in the document of its owning service or package, under the actual file and entity that implement it. Describe its purpose, producers/consumers and precise behavior there. When fields are needed, use the same `Name`, `Type`, `Description` table.

Consumers link to that authoritative same-plan definition rather than copying a competing one. Describe cross-project errors and ordering where relevant. Refer to current code for unchanged interfaces; do not read prior-plan documents or create another root-level specification file.
