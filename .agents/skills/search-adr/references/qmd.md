# Project QMD Integration

The project MCP server is named `qmd`. It runs QMD's installed executable directly from the repository root using `./node_modules/.bin/qmd`. Node.js 22 or later, npm, Git, Python and native build tools are supplied by the devcontainer configuration. No custom launcher or indexing script is used.

The root `package.json` and `package-lock.json` pin QMD directly to upstream Git revision `04e4dbd8245c527a88f1a8f0bda547aef9ca81fb`. The published npm 2.8.3 build predates metadata filtering despite newer README examples; do not replace the Git pin with that release without checking its MCP schema.

The tracked `.qmd/index.yml` defines two collections with paths relative to the repository root:

| Collection            | Root     | Pattern             | Scope                                      |
| --------------------- | -------- | ------------------- | ------------------------------------------ |
| `taskin-project-adrs` | `.adr`   | `*/README.md`       | Decisions established outside plans.       |
| `taskin-plan-adrs`    | `.plans` | `*/adr/*/README.md` | Decisions belonging to development stages. |

The explicit roots include both Git-ignored directories without indexing skill examples, plan READMEs or service/package specifications. Each match is one decision document. The source format belongs to [manage-adr](../../manage-adr/references/adr-format.md).

## Setup and refresh

QMD is a development dependency in the root `package.json`. The devcontainer's `npm ci` command in `postCreateCommand` installs it together with the other JavaScript development tools. To install dependencies manually in an already running container, run from the repository root as `vscode`:

```sh
npm ci
export XDG_CACHE_HOME="$PWD/.qmd/cache"
npm exec -- qmd update
npm exec -- qmd embed
```

npm installs the pinned dependency using the root `package-lock.json`. Run `update`, then `embed`, to refresh the index. First semantic use can download QMD's local models. Runtime, database and cache files are ignored. These commands do not modify source ADRs.

Other native QMD commands:

```sh
npm exec -- qmd status
```

There is no background watcher or MCP refresh tool configured. Refresh after ADR creation, amendment or deletion, and before relying on search results if source files changed. A failed `embed` can leave a current lexical index with incomplete vectors; report this distinction.

The project `.codex/config.toml` uses the relative executable `./node_modules/.bin/qmd` and cache location `.qmd/cache`. It omits `cwd` so Codex uses the calling runtime's working directory. Open or start Codex at the repository root and run the QMD commands there, with `PWD` matching the working directory. The collection paths `.adr` and `.plans` in `.qmd/index.yml` are relative to that working directory. The workspace mount path can change without editing these configuration files. Start or reload Codex in this trusted checkout after changing MCP configuration; an already running chat may not discover the new server immediately.

QMD's executable is `./node_modules/.bin/qmd`, also available in the devcontainer's root development-tools PATH. The `.qmd` directory holds only search configuration, the local index and model cache; dependencies use the root `node_modules`. Runtime and cache files stay in the bind-mounted checkout and are ignored by Git. Recreating the devcontainer reinstalls native dependencies; its cache remains available through the workspace mount. Existing containers can use the relative executable from the repository root immediately without a rebuild.

## MCP requests

The pinned QMD tools are `query`, `get`, `multi_get` and `status`. Inspect the installed server's tool schemas rather than assuming another release has the same interface.

An example search scoped to a selected plan is:

```json
{
  "searches": [
    { "type": "lex", "query": "core contracts" },
    { "type": "vec", "query": "where shared service contracts are defined" }
  ],
  "collections": ["taskin-plan-adrs"],
  "filter": {
    "operator": "and",
    "operands": [
      { "key": "type", "operator": "eq", "value": "adr" },
      { "key": "status", "operator": "eq", "value": "Accepted" },
      { "key": "plan_id", "operator": "eq", "value": "20261103-0001" }
    ]
  },
  "limit": 5,
  "rerank": false
}
```

For the project part of the same lookup, send a separate query with `collections: ["taskin-project-adrs"]` and the `type` and `status` filters only. Omit `plan_id`: project records do not have it. Keep the selected `plan_id` filter on every query to `taskin-plan-adrs`; remove it only when repository-wide plan ADR search is authorized. An explicitly plan-only request does not search the project collection.

This pinned revision uses `key` in filter conditions; newer main-branch examples may use `field`. The explicit typed queries avoid automatic query expansion, and `rerank: false` avoids requiring a reranking model for initial retrieval. Enable reranking when its extra model and latency are useful. A lexical-only query with `rerank: false` can work without embedding/reranking models; label this fallback accurately.

Use `get` with an exact returned document path and its installed parameter schema. Verify the source before answering, because search snippets and cached metadata may be stale. `replaces` and `replaced_by` are application metadata; QMD does not automatically resolve the decision lifecycle.

Official sources: [pinned QMD revision](https://github.com/tobi/qmd/tree/04e4dbd8245c527a88f1a8f0bda547aef9ca81fb), [pinned filter schema](https://github.com/tobi/qmd/blob/04e4dbd8245c527a88f1a8f0bda547aef9ca81fb/src/metadata-filter.ts), [Codex MCP configuration](https://learn.chatgpt.com/docs/extend/mcp?surface=cli).
