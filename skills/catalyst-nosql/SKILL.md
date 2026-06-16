---
name: catalyst-nosql
description: "Catalyst NoSQL — non-relational document database for unstructured, semi-structured, or JSON-heavy data with flexible per-item schema. Trigger on 'NoSQL', 'document storage', 'NoSQL collection', 'flexible schema', or 'Catalyst document database'. Do NOT use when you need SQL joins, ZCQL queries, or a fixed relational schema — use catalyst-datastore instead."
metadata:
  version: "2.0.0"
---

## How It Works

1. **NoSQL vs Data Store** — If the user is undecided, apply this matrix:

   | | Catalyst Data Store | Catalyst NoSQL |
   |---|---|---|
   | Schema | Fixed, defined upfront | Flexible, per-item (no uniform structure required) |
   | Query | ZCQL (SQL-like joins and filters) | Partition key + optional sort key |
   | Data type | Structured, relational rows | Unstructured/semi-structured, JSON-format |
   | Priority | ACID compliance, complex queries | High scalability, high write throughput |

   **Rule:** If the user says "ZCQL", "join", "relational", or "fixed schema" — stop and load `catalyst-datastore` instead.
2. **Load `references/nosql-basics.md`** — for SDK operations (insert, get, delete, query) and collection management.
3. **Answer** — Provide the SDK method call with the correct collection name and item structure.

## Triggers

Use this skill for: "NoSQL", "document storage", "NoSQL collection", `insertItem`, `getItem`, `deleteItem`, "NoSQL vs Data Store", "flexible schema", "Catalyst document database", "schemaless data on Catalyst", or "nested objects in Catalyst".

## References

| Reference | Load when the query is about… |
|-----------|-------------------------------|
| `references/nosql-basics.md` | SDK operations (insert, get, delete, query), NoSQL vs Data Store decision table, collection management |
