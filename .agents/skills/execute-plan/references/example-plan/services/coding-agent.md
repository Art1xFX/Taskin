# Coding-Agent

## Services

### format_run_label

Produces an identifiable run name for status messages and logs. It delegates missing-name handling to the shared Core model so consumers agree on how explicit names are preserved.

- Create a synchronous module-level function.
- Import `CodingRunLabel` from `taskin_core.models`; its definition is in [the Core plan](../packages/core.md#codingrunlabel).
- Call `label.get_name` with `Run ` followed by `label.id` in its standard hyphenated UUID form as the fallback.
- Do not mutate the label or perform I/O.

| Name | Type | Description |
|---|---|---|
| label | `CodingRunLabel` | Required run identity and optional name to format. |

**Returns:** `str` — the unchanged explicit name, or a fallback such as `Run 00000000-0000-0000-0000-000000000001` when the name is missing or empty.

## tests/test_services.py

### test_explicit_names_survive_consumer_formatting

Checks that the coding agent preserves the shared explicit-name contract when preparing text for its users.

- Create a label with an explicit name containing surrounding whitespace.
- Format it and verify that the supplied name is returned unchanged.

**Returns:** `None`.

### test_unnamed_runs_have_identifiable_labels

Checks that two unnamed runs remain distinguishable in status messages and logs.

- Create two labels with distinct UUIDs, one with a missing name and one with an empty name.
- Verify that each formatted result contains its corresponding UUID and that the results differ.

**Returns:** `None`.
