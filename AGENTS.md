# Agent instructions

## Language and scope

- Communicate with the user in Russian. Write repository documents, code comments and docstrings in English.
- Taskin is a Python monorepo. Services belong in `services/`; shared packages belong in `packages/`.
- Read the root README and the relevant code, configuration and project READMEs. Follow applicable nested `AGENTS.md` files
  along the paths involved in the task. Do not scan unrelated projects or load every document by default.
- Treat the current code and configuration as the implementation state. Do not assume that planned services, workspace
  members or test runners are already implemented.
- Preserve unrelated user changes and keep edits within the authorized scope.

## Plans and architecture decisions

- Use [create-plan](.agents/skills/create-plan/SKILL.md) when creating or amending a development-stage plan and
  [execute-plan](.agents/skills/execute-plan/SKILL.md) when implementing a selected plan.
- Use [manage-adr](.agents/skills/manage-adr/SKILL.md) for decision records and
  [search-adr](.agents/skills/search-adr/SKILL.md) when architectural rationale or decision history is needed.
- Read only the skill relevant to the task and any resources it requires. The skills own their document formats and
  workflows; do not introduce competing templates here or load ADRs routinely.
- Preserve the selected plan's reading and amendment restrictions. Planning and execution do not authorize reading older
  or sibling plans through links or search results.

## Environment files

- Never read, open, search, print, parse or otherwise inspect the contents of `.env` files. Exclude them from searches and
  bulk file reads, including when a task involves environment configuration.
- Use the environment already configured for the process or devcontainer. Do not source `.env` as an automatic retry for
  failed tests or commands.
- If configuration is missing, report the missing prerequisite. Ask the user only for the specific non-sensitive
  information needed; do not request or expose secret values.

## Development environment

- Use `uv` for Python dependency management and execution. Follow the Python version and dependencies declared in the
  relevant `pyproject.toml` files; the root currently requires Python 3.14.
- Keep dependencies in their owning project's `pyproject.toml` and use the root `uv.lock`. Use locked execution for checks
  so verification does not silently change dependency resolution.
- Use the committed Node tooling through `npm`; install its locked dependencies with `npm ci` when setup is needed. Keep
  `package-lock.json` synchronized when deliberately changing Node dependencies.
- The devcontainer defines the shared development environment. Prefer its configured tools and paths instead of adding
  parallel environments or hard-coding host-specific paths into the repository.

## Checks

- Run focused checks relevant to the change. Use the affected project's documented test runner or the selected plan's
  required commands; use only runners configured in the repository.
- For changed Python files, run `uv run --locked ruff check <paths>` and `uv run --locked ruff format --check <paths>`.
- For changed Markdown and supported configuration files, use `npm exec -- prettier --check <paths>`. Preserve existing
  formatting conventions and limit any formatting fixes to the affected files.
- For TOML, explicitly load the installed plugin: `npm exec -- prettier --plugin=prettier-plugin-toml --check <paths>`.
- Select the relevant test cases when the runner supports selection. Run broader suites only when required by the plan or
  justified by the affected behavior.
- If a check fails, diagnose the failure before retrying. Report failed or unavailable checks accurately; do not claim
  successful verification when prerequisites are missing.

## Tools

- Prefer available purpose-built tools for external services, including GitLab and Redmine. Use command-line fallbacks only
  when the corresponding operation is unavailable through those tools.
- A fallback command or retry does not grant permission for external changes. Use the user's existing authorization for
  issues, merge requests, comments, branches and other state changes; request confirmation only when the action exceeds it.
- Use `rg` and `rg --files` for local content and filename searches. Limit searches to relevant paths and exclude `.env`
  files. Use an available alternative if `rg` is unavailable.
- Use `jq` for JSON and `yq` for YAML and TOML when inspecting, querying, validating or modifying structured data. Do not use
  Python scripts or Python libraries for those operations. Use the correct input and output formats for `yq`.
- Use focused patches when structured-data tools would reformat unrelated content. Preserve indentation, quoting style,
  blank lines and comments.

## Formatting and delivery

- Follow `.editorconfig` and the relevant formatter configuration. Use four spaces for Python, two for Markdown and most
  configuration files, UTF-8, LF endings and a final newline. The configured line length is 120 characters.
- Avoid unrelated formatting, dependency upgrades and generated-file changes.
- Report what changed, which checks ran and any remaining blockers in Russian. Do not create commits, publish changes or
  modify external systems without authorization covering those actions.
