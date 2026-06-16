## Overview

NoSQL is Catalyst's document database for semi-structured/flexible schema data. Organized in collections of documents.

## SDK Operations (Node.js)

```javascript
const nosql = catalystApp.nosql();
const collection = nosql.collection('UserProfiles');
// Collection name from Console → Cloud Scale → NoSQL

// Insert a document
const doc = await collection.insertDocument({
  userId: 'u123',
  preferences: { theme: 'dark', language: 'en' },
  tags: ['premium', 'beta']
});

// Get a document
const fetched = await collection.getDocument(DOCUMENT_ID);

// Update a document
await collection.updateDocument(DOCUMENT_ID, {
  preferences: { theme: 'light' }
});

// Delete a document
await collection.deleteDocument(DOCUMENT_ID);

// Query documents
const results = await collection.queryDocuments({
  filter: { 'preferences.theme': 'dark' }
});
```

## When to Use NoSQL vs Data Store

| Use NoSQL | Use Data Store |
|-----------|---------------|
| Flexible/evolving schema | Fixed schema |
| Nested/nested arrays | Flat tabular data |
| No complex JOINs needed | JOINs required |
| Document-oriented access | Row-oriented access with ZCQL |
