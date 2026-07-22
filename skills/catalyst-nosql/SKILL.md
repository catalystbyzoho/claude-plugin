---
name: catalyst-nosql
description: "Catalyst NoSQL — key-value table store with typed items built via NoSQLItem builder. Partition key required. Trigger on 'NoSQL', 'nosql.table', 'insertItems', 'fetchItem', 'updateItems', 'deleteItems', 'queryTable', 'NoSQLItem', 'document storage', 'flexible schema', or 'Catalyst document database'. Do NOT use when you need SQL joins, ZCQL queries, or a fixed relational schema — use catalyst-datastore instead."
metadata:
  version: "2.1.0"
---

## How It Works

1. **NoSQL vs Data Store** — Choose NoSQL for flexible/evolving per-item attributes, nested maps/arrays, and high-write-throughput key-value access; choose Data Store for a fixed relational schema, joins, or ACID/ZCQL access. Full comparison table in `references/nosql-basics.md`. **Rule:** if the user says "ZCQL", "join", "relational", or "fixed schema" — stop and load `catalyst-datastore` instead.
2. **Load `references/nosql-basics.md`** — for SDK operations (`insertItems`, `fetchItem`, `updateItems`, `deleteItems`, `queryTable`) and the `NoSQLItem` builder.
3. **Answer** — Provide the SDK method call with the correct table name, partition-key attribute, and `NoSQLItem` construction.

## Triggers

Use this skill for: "NoSQL", "document storage", `nosql.table`, `insertItems`, `fetchItem`, `updateItems`, `deleteItems`, `queryTable`, `NoSQLItem`, "NoSQL vs Data Store", "flexible schema", "Catalyst document database", "schemaless data on Catalyst", or "nested objects in Catalyst".

## References

| Reference | Load when the query is about… |
|-----------|-------------------------------|
| `references/nosql-basics.md` | SDK operations (`insertItems`, `fetchItem`, `updateItems`, `deleteItems`, `queryTable`), `NoSQLItem` builder, partition-key model, NoSQL vs Data Store decision table |
