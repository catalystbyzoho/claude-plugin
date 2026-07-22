---
name: catalyst-datastore
description: "Catalyst Data Store — relational cloud database with ZCQL, CRUD operations, table permissions, and result pagination. Requires MCP connection — check for the ZohoMCP_* meta-tools before any operation. Trigger on 'Data Store', 'ZCQL', 'create table', 'executeZCQLQuery', 'table permissions', 'ROWID', 'boolean column', 'boolean always true', 'truthy string', 'boolean stored as string', or 'DataStore data types'. You MUST load this skill whenever writing code that reads or writes Data Store data — ZCQL result wrapping, boolean-as-string behavior, and App User permissions are non-obvious and cause silent bugs if skipped."
metadata:
  version: "2.1.0"
---

## ⚠️ PREREQUISITES — READ THIS FIRST

**MCP gate (before ANY Data Store operation):** Confirm the `ZohoMCP_*` meta-tools (`ZohoMCP_getSchema`, `ZohoMCP_executeTool`, `ZohoMCP_listTools`, `ZohoMCP_getFeatures`) are present in your tool list — that is the "MCP connected" signal. (The `CatalystbyZoho_*` names never appear as tools; they are `tool_name` values passed to `ZohoMCP_executeTool`.) **If the meta-tools are NOT present → STOP:** do not write code, scaffold files, or instruct Console steps — load the `catalyst-zoho-mcp` skill, guide the user through MCP setup, and resume only once they appear.

Once the tools are visible, complete the canonical pre-flight once per session (establish + verify org/project) → `../catalyst-basics/references/preflight.md`, then continue to "How It Works". Verify table access with `CatalystbyZoho_List_All_Tables` before create/update operations.

---

## How It Works

1. **Create or locate the table via MCP** — Creating a table? Invoke `CatalystbyZoho_Create_Table` via `ZohoMCP_executeTool` (schema first with `ZohoMCP_getSchema`). Do NOT instruct the user to open the Catalyst Console or create the table manually. Reading/writing data? Invoke `CatalystbyZoho_List_All_Tables` (same way) to get table IDs. Never ask the user to copy table IDs.
2. **Load `references/datastore-basics.md`** — for CRUD operations, ZCQL syntax, result unwrapping, and permissions setup.
3. **Unwrap ZCQL results** — Always remind: `rows.map(r => r.TableName)`. Raw ZCQL results are wrapped; accessing without unwrapping is the #1 Data Store bug.
4. **Permissions** — App User permissions are OFF by default. If the query involves a logged-in user reading data, check Console → Table → Scopes and Permissions.
5. **Pagination** — ZCQL max 300 rows per query. Use `LIMIT offset, count` for larger datasets.

## Security Checklist

- **App User write permissions are off by default.** The App User role has SELECT enabled by default, but INSERT, UPDATE, and DELETE must be manually enabled per table. Go to Console → Table → Scopes and Permissions → App User role → check Insert/Update/Delete for any table your authenticated users need to write.
- **App Administrator has all permissions by default.** Never assign the App Administrator role to regular end-users — it grants full read/write/delete access to all tables.

## Triggers

Use this skill for: "Data Store", "ZCQL", "catalyst table", "create table", "query data", `executeZCQLQuery`, "table permissions", "App User permissions", "ROWID", "CREATEDTIME", "Data Store CRUD", "JOIN in ZCQL", "pagination ZCQL", "data store column types", "insert row", "update row", "delete row", or "relational data on Catalyst".

## References

| Reference | Load when the query is about… |
|-----------|-------------------------------|
| `references/datastore-basics.md` | CRUD, ZCQL queries, result unwrapping, pagination, column types, App User permissions setup, CREATEDTIME timezone gotcha, emoji limitation |
