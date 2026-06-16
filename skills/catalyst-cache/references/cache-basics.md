## Overview

In-memory cache organized in segments. Ephemeral data only — not persisted to disk.

- **Max value size**: 5 MB
- **Max TTL**: 48 hours (172,800 seconds)
- Values are **strings only** — serialize/deserialize JSON yourself

## SDK Operations (Node.js)

```javascript
const cache = catalystApp.cache();
const segment = cache.segment(SEGMENT_ID);
// Segment ID from Console → Cloud Scale → Cache → segment list

// Put (TTL in seconds, max 172800 = 48 hours)
await segment.put('user:123', JSON.stringify({ name: 'Alice' }), 3600);

// Get
const value = await segment.getValue('user:123');
const parsed = value ? JSON.parse(value) : null;

// Update
await segment.update('user:123', JSON.stringify({ name: 'Alice Updated' }));

// Delete
await segment.delete('user:123');
```

## Critical Gotchas

### `segment.delete()` does NOT remove the key

`segment.delete(key)` sets `cache_value = null` but the key continues to exist. A subsequent `getValue()` returns HTTP 200 with `cache_value: null` — it does NOT raise an exception.

```javascript
// WRONG — checking for exception won't work:
try {
  const val = await segment.getValue(key);
} catch (e) { /* never throws for deleted keys */ }

// CORRECT — check the value itself:
const val = await segment.getValue(key);
if (val) {
  // key is live
}
```

### `segment.update()` without expiry resets TTL to 48 hours

`segment.update(key, value)` without an expiry argument **resets the TTL to 48 hours**, regardless of the TTL the key was originally created with. If you created a key with a 1-hour TTL and update it without passing expiry, the key now lives for 48 hours.

```javascript
// WRONG — silently resets TTL to 48 hours:
await segment.update(key, value);

// CORRECT — always pass the TTL explicitly to preserve intended expiry:
await segment.update(key, value, 1);  // 1-hour TTL, matching initial put(key, value, 1)
```

## Cache Pattern for Session/Temp Data

```javascript
// Store session
await segment.put(`session:${userId}`, JSON.stringify({ 
  userId, role: 'admin', loginTime: Date.now() 
}), 86400); // 24 hours

// Retrieve session
const sessionStr = await segment.getValue(`session:${userId}`);
const session = sessionStr ? JSON.parse(sessionStr) : null;

if (!session) {
  // Session expired or doesn't exist
}
```
