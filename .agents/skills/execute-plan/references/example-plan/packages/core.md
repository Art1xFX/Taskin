# Core

## Models

### CodingRunLabel

Represents the shared identity and optional display name of a coding run. Consumers use the same missing-name behavior while choosing a fallback appropriate to their interface.

- Create a Pydantic `BaseModel` named `CodingRunLabel`.
- This is a data-transfer model; do not create a database table.

| Name | Type | Description |
|---|---|---|
| id | `UUID` | Required run identifier. Use Pydantic's standard UUID validation. |
| name | `str \| None` | Optional display name. Defaults to `None`; preserve the supplied string without trimming. |

**Method `get_name`**

Provides a display name for a consumer without changing the model. Add a synchronous instance method so each consumer can supply its own fallback.

| Name | Type | Description |
|---|---|---|
| fallback | `str` | Name used when `name` is `None` or empty. Defaults to `"Unnamed run"`. |

**Returns:** `str` — the unchanged `name` when it is non-empty; otherwise `fallback`. A whitespace-only name is non-empty. Do not mutate any fields.

## tests/test_models.py

### test_missing_names_use_the_supplied_fallback

Checks that consumers can select their own fallback consistently for both absent and empty names.

- Create labels with `name=None` and `name=""`.
- Call `get_name` with a distinct explicit fallback for each label.
- Verify that each supplied fallback is returned and each model remains unchanged.

**Returns:** `None`.

### test_explicit_names_are_preserved

Protects user-supplied display names from normalization by shared infrastructure.

- Check a normal name and a whitespace-only name.
- Verify that `get_name` returns each original value unchanged.

**Returns:** `None`.
