# DataStore Operations via MCP

Invoke the `CatalystbyZoho_*` DataStore operations via `ZohoMCP_executeTool` (passing the operation name as `tool_name`; fetch its schema first with `ZohoMCP_getSchema`) to create and manage DataStore tables and columns without writing SDK code. The `CatalystbyZoho_*` names are `tool_name` values, not directly-callable tools.

## Pre-flight

Once per session, before your first DataStore MCP operation (not before every call — trust it once it passes):

1. **Establish + verify org/project** — follow the canonical pre-flight in `../../catalyst-basics/references/preflight.md` (read org/project from `.catalystrc` and confirm parity via `CatalystbyZoho_Get_Project_By_Id`, or resolve via `List_All_*` when absent).
2. `CatalystbyZoho_List_All_Tables` → verify access and get table IDs

## CatalystbyZoho_Create_Column

Creates one or more columns in an existing DataStore table. Supports batch creation (array of column objects).

**Required path variables:**
- `projectId`: Catalyst project ID
- `id`: Table ID — note this is `id`, NOT `tableId`

**Valid `data_type` values** (from the live tool schema): `text`, `varchar`, `date`, `datetime`, `int`, `double`, `boolean`, `bigint`, `encrypted text`, `foreign key`. There is **no `email` type** — model an email as `varchar` with `is_unique`.

Every column requires `column_name`, `data_type`, `is_mandatory`, and `audit_consent`. Type-specific fields:

| Type | Extra fields it accepts |
|------|-------------------------|
| `text` | *(none — no `max_length`, no `is_unique`, no `search_index_enabled`)* |
| `varchar` | `max_length`, `is_unique`, `search_index_enabled` |
| `int`, `bigint` | `is_unique`, `search_index_enabled` |
| `double` | `decimal_digits`, `search_index_enabled` |
| `date`, `datetime`, `boolean` | `search_index_enabled` |
| `encrypted text` | *(none beyond the common fields)* |
| `foreign key` | `parent_table`, `parent_column`, `constraint_type` (`ON-DELETE-SET-NULL` / `ON-DELETE-CASCADE`), `search_index_enabled` |

For types that accept `is_unique` / `search_index_enabled`, those fields are **required** in the payload (send `"false"` if not needed).

```json
// Text column — no max_length, no search index
{ "column_name": "description", "data_type": "text", "is_mandatory": "false", "audit_consent": "false" }

// Varchar column with a length cap and uniqueness (use this for emails)
{ "column_name": "email", "data_type": "varchar", "max_length": 255, "is_mandatory": "true", "is_unique": "true", "search_index_enabled": "false", "audit_consent": "false" }

// Bigint column — requires is_unique and search_index_enabled
{ "column_name": "count", "data_type": "bigint", "default_value": "0", "is_mandatory": "false", "is_unique": "false", "search_index_enabled": "false", "audit_consent": "false" }
```

**All boolean-like fields take string values:** `"true"` / `"false"` — not JSON booleans.

**Batch creation payload shape:**

```json
{
  "body": [
    { "column_name": "name", "data_type": "varchar", "is_mandatory": "true", "is_unique": "false", "search_index_enabled": "false", "max_length": 255, "audit_consent": "false" },
    { "column_name": "email", "data_type": "varchar", "is_mandatory": "true", "is_unique": "true", "search_index_enabled": "false", "max_length": 255, "audit_consent": "false" }
  ],
  "headers": { "Catalyst-org": 928993403, "Environment": "Development" },
  "path_variables": { "projectId": "85698000000014039", "id": "85698000000018006" }
}
```

## Common Errors

| Error | Cause | Fix |
|-------|-------|-----|
| `Text column cannot be search indexed` | `search_index_enabled` passed for a `text` column | Omit `search_index_enabled` for `text`; use `varchar` if you need indexing/uniqueness |
| `Missing required field is_unique` | `varchar`/`int`/`bigint` require `is_unique` | Add `"is_unique": "false"` (or `"true"`) to the column object |
| `Invalid max_length` | `max_length` only applies to `varchar` | Remove `max_length` from non-`varchar` column objects (use `varchar`, not `text`, for length-capped strings) |
| `Invalid data_type "email"` | No `email` type exists | Use `varchar` with `is_unique` for email columns |
| Column path variable wrong | Passing `tableId` instead of `id` in path_variables | Use `"id"` as the key name, not `"tableId"` |
