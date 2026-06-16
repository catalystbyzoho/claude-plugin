## File Upload via Advanced I/O Function (busboy)

Parse `multipart/form-data` file uploads using `busboy`. Install: `npm install busboy`.

```javascript
const Busboy = require('busboy');
const catalyst = require('zcatalyst-sdk-node');

module.exports = async (catalystApp, context, req, res) => {
  const busboy = Busboy({ headers: req.headers });
  const chunks = [];
  let fileName = '';
  let mimeType = '';

  await new Promise((resolve, reject) => {
    busboy.on('file', (fieldname, file, info) => {
      fileName = info.filename;
      mimeType = info.mimeType;
      file.on('data', chunk => chunks.push(chunk));
      file.on('end', resolve);
      file.on('error', reject);
    });
    busboy.on('error', reject);
    req.pipe(busboy);
  });

  const fileBuffer = Buffer.concat(chunks);

  // Upload to Stratus
  const { Readable } = require('stream');
  const stream = Readable.from(fileBuffer);
  await catalystApp.stratus().bucket('myapp-files-70699')
    .putObject(`uploads/${fileName}`, stream);

  res.status(200).json({ status: 'uploaded', fileName });
};
```

### Catalyst-Config for File Upload Function

```json
{
  "functions": [{
    "name": "file_upload",
    "type": "Advanced IO",
    "function_handler": "index.js",
    "authentication": "required",
    "memory": 512,
    "max_time": 45
  }]
}
```

---

## Stream a File from Stratus to Response

```javascript
module.exports = async (catalystApp, context, req, res) => {
  const key = req.query.file;
  if (!key) return res.status(400).json({ error: 'file param required' });

  const bucket = catalystApp.stratus().bucket('myapp-files-70699');

  // HEAD check first
  const head = await bucket.headObject(key, { throwErr: false });
  if (!head) return res.status(404).json({ error: 'File not found' });

  res.setHeader('Content-Type', head.content_type || 'application/octet-stream');
  res.setHeader('Content-Disposition', `attachment; filename="${key.split('/').pop()}"`);

  const stream = await bucket.getObject(key);
  stream.pipe(res);
};
```

---

## Error Handling Pattern

Standard error response pattern for Advanced I/O functions:

```javascript
module.exports = async (catalystApp, context, req, res) => {
  try {
    // ... your logic
    const result = await someOperation();
    res.status(200).json({ data: result });
  } catch (err) {
    console.error(JSON.stringify({
      action: 'myFunction',
      error: err.message,
      stack: err.stack
    }));

    // Classify error type
    if (err.name === 'ValidationError') {
      return res.status(400).json({ error: err.message });
    }
    if (err.status === 404 || err.message?.includes('not found')) {
      return res.status(404).json({ error: 'Resource not found' });
    }

    res.status(500).json({ error: 'Internal server error' });
  }
};
```

---

## CORS for Local Dev (Express)

**Do NOT add CORS headers in function code for production origins** — the Catalyst gateway handles this.
Only set CORS for `localhost` (local dev where no gateway exists):

```javascript
app.use((req, res, next) => {
  const origin = req.headers.origin || '';
  if (/^http:\/\/localhost(:\d+)?$/.test(origin)) {
    res.setHeader('Access-Control-Allow-Origin', origin);
    res.setHeader('Access-Control-Allow-Credentials', 'true');
    res.setHeader('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE, OPTIONS');
    res.setHeader('Access-Control-Allow-Headers', 'Content-Type, Authorization');
    if (req.method === 'OPTIONS') return res.status(204).end();
  }
  next();
});
```

---

## Testing Functions Locally (Mock Pattern)

Mock `http.ServerResponse` for unit testing Basic I/O functions:

```javascript
// test/myFunction.test.js
const handler = require('../functions/my_function/index.js');

async function runTest() {
  // Mock context
  const context = {
    closeWithSuccess: () => console.log('SUCCESS'),
    closeWithFailure: (msg) => console.error('FAILURE:', msg)
  };

  // Mock basicIO
  const basicIO = {
    getArguments: () => ({ userId: '12345', action: 'test' }),
    setOutput: (result) => console.log('OUTPUT:', JSON.stringify(result, null, 2))
  };

  await handler(context, basicIO);
}

runTest().catch(console.error);
```

For Advanced I/O (Express), test against the actual local dev server:
```bash
catalyst serve
curl -X POST http://localhost:3000/server/my_function/execute \
  -H "Content-Type: application/json" \
  -d '{"key": "value"}'
```

---

## Chaining Functions (Call One Function from Another)

**Anti-pattern:** Never call Advanced I/O functions via HTTP from other functions in production.

**Preferred patterns:**
1. **Shared module**: Extract common logic into `functions/utils/` and import it
2. **Circuits**: For multi-step orchestration
3. **Job Scheduling**: For async fan-out

```javascript
// functions/utils/dataHelper.js
async function getUserById(catalystApp, userId) {
  const rows = await catalystApp.zcql().executeZCQLQuery(
    `SELECT * FROM Users WHERE ROWID = '${userId}'`
  );
  return rows[0]?.Users || null;
}

module.exports = { getUserById };
```

```javascript
// functions/my_function/index.js
const { getUserById } = require('../utils/dataHelper');

module.exports = async (catalystApp, context, req, res) => {
  const user = await getUserById(catalystApp, req.params.id);
  if (!user) return res.status(404).json({ error: 'User not found' });
  res.json({ user });
};
```

---

## Result Unwrapping (ZCQL)

ZCQL result rows are wrapped — always unwrap before accessing column values:

```javascript
const rows = await catalystApp.zcql().executeZCQLQuery('SELECT * FROM Tasks');

// Each row is: { Tasks: { ROWID: '...', Title: '...', ... } }
const tasks = rows.map(row => row.Tasks);  // ← Unwrap the table name wrapper

// For JOINs
const joinRows = await catalystApp.zcql().executeZCQLQuery(
  'SELECT * FROM Tasks JOIN Users ON Tasks.UserId = Users.ROWID'
);
const items = joinRows.map(row => ({ task: row.Tasks, user: row.Users }));
```

---

## HTTP Payload Limits

| Function Type | Max Request Body | Max Response Body |
|--------------|-----------------|------------------|
| Advanced I/O | 50 MB | 50 MB |
| Basic I/O | 50 MB | 50 MB |
| AppSail | No explicit limit (configurable) | No explicit limit |

For uploads > 50 MB, use pre-signed Stratus URLs for direct browser → Stratus upload (bypasses the function entirely).
