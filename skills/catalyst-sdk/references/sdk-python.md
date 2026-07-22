Install: `pip install zcatalyst-sdk`

Requires **Python 3.10+** (Catalyst function runtimes: 3.10–3.13).

---

## Initialization

```python
import zcatalyst_sdk

# Advanced I/O (Flask)
catalyst_app = zcatalyst_sdk.initialize(req=request)

# Basic I/O
catalyst_app = zcatalyst_sdk.initialize(req=context)

# Event / Cron functions
catalyst_app = zcatalyst_sdk.initialize(req=context)

# Job functions — MUST use admin scope; USER token is absent in the Job runtime
catalyst_app = zcatalyst_sdk.initialize(req=context, scope='admin')

# Admin scope (any function type)
admin_app = zcatalyst_sdk.initialize(req=request, scope='admin')
```

---

## Data Store

```python
table = catalyst_app.datastore().table("TableName")

# Insert single row
row = table.insert_row({"Name": "Alice", "Email": "alice@example.com"})

# Insert multiple rows
rows = table.insert_rows([
    {"Name": "Bob", "Email": "bob@example.com"},
    {"Name": "Carol", "Email": "carol@example.com"}
])

# Get single row by ROWID
row = table.get_row(row_id)

# Get paged rows
result = table.get_paged_rows(next_token="token", max_rows=200)
rows = result["data"]
has_more = result["more_records"]
next_token = result["next_token"]

# Update row (ROWID required)
updated_row = table.update_row({"ROWID": "123456000000012345", "Name": "Alice Updated"})

# Delete row
table.delete_row(row_id)
```

---

## ZCQL

```python
# NOTE: the ZCQL method name differs per SDK — Python: execute_query() / execute_olap_query(); Node.js: executeZCQLQuery()
zcql_service = catalyst_app.zcql()

rows = zcql_service.execute_query("SELECT * FROM TableName WHERE Name = 'Alice'")
result = zcql_service.execute_olap_query("SELECT COUNT(ROWID) FROM TableName GROUP BY Status")
```

---

## Cache

```python
segment = catalyst_app.cache().segment(segment_id)

segment.put("my_key", "my_value", expiry=3600000)  # expiry in ms
value = segment.get("my_key")
segment.update("my_key", "new_value", expiry=7200000)
segment.delete("my_key")  # sets to null, doesn't truly delete
```

---

## Stratus

```python
stratus_service = catalyst_app.stratus()
bucket = stratus_service.bucket(bucket_name)

buckets = stratus_service.list_buckets()
details = bucket.get_details()
objects = bucket.list_objects(prefix="folder/", max_keys=100)

with open("/path/to/file.txt", "rb") as f:
    bucket.upload_object("folder/file.txt", f, content_type="text/plain")

content = bucket.download_object("folder/file.txt")
bucket.delete_object("folder/file.txt")
bucket.rename_object("folder/old_name.txt", "folder/new_name.txt")
```

---

## Auth

```python
auth_service = catalyst_app.authentication()

# Register user
result = auth_service.register_user(
    {"platform_type": "web", "zaid": "your_zaid"},
    {"first_name": "Alice", "last_name": "Smith", "email_id": "alice@example.com"}
)

# Get current user details
user = auth_service.get_user_details()

# Delete user
auth_service.delete_user(user_id)
```

---

## Email

```python
catalyst_app.email().send_mail({
    "from_email": "noreply@yourdomain.com",
    "to_email": ["recipient@example.com"],
    "cc": ["cc@example.com"],
    "subject": "Hello from Catalyst",
    "content": "<h1>Welcome!</h1>",
    "html_mode": True
})
```

---

## Search

```python
result = catalyst_app.search().execute_search_query(
    "search term",
    search_config={"search_table_columns": {"TableName": ["Col1", "Col2"]}}
)
```

---

## Connections

```python
credentials = catalyst_app.connections().get_connection_credentials({
    "connection_name": "my_connection"
})
# credentials["access_token"] = OAuth token
```

---

## Circuits

```python
result = catalyst_app.circuit().execute(circuit_id, {"key1": "value1"})
```

---

## NoSQL

```python
nosql_service = catalyst_app.nosql()
table = nosql_service.table("NoSQLTableName")

# Items use Catalyst typed-JSON format: {"S": "..."} string, {"N": "123"} number
table.insert_items({"item": {"pk": {"S": "partition1"}, "data": {"S": "value1"}}})

# Updates use update_attributes operations — there is NO update_expression parameter
table.update_items({
    "keys": {"pk": {"S": "partition1"}},
    "update_attributes": [
        {"operation_type": "PUT", "attribute_path": ["data"], "update_value": {"S": "updated"}}
    ]
})
```

> For `fetch_items`, `query_table`, and `delete_items` signatures, see the Python NoSQL reference at docs.catalyst.zoho.com; the item model mirrors the `catalyst-nosql` skill (`../../catalyst-nosql/references/nosql-basics.md`).

---

## Job Scheduling

```python
cron = catalyst_app.job_scheduling().cron()

# job_meta defines WHAT to execute — jobpool_name (or jobpool_id) lives here, not at cron level
job_meta = {
    "job_name": "generate_report",       # alphanumeric + underscores only
    "target_type": "Function",
    "target_name": "generate_report",    # or use target_id
    "jobpool_name": "ReportPool",         # or jobpool_id
    "params": {"report_type": "daily_summary"}  # optional
}

# Calendar daily: fixed time each day — cron_detail carries the schedule, repetition_type: "daily"
cron.create_cron({
    "cron_name": "daily_report",
    "cron_status": True,
    "cron_type": "Calendar",
    "cron_detail": {"hour": 9, "minute": 0, "second": 0, "repetition_type": "daily"},
    "job_meta": job_meta
})
```

> Cron shape mirrors the Node.js SDK's `job_meta` / `cron_detail` model — see `sdk-nodejs.md`. `cron_type` is one of `OneTime`, `Periodic`, `Calendar`, `CronExpression` (capitalized). The flat `target_function` / `cron_expression` shape is **not** valid.

---

## Push Notifications

```python
push_service = catalyst_app.pushnotification()

push_service.send_notification({
    "subject": "New Update",
    "message": "A new feature has been released.",
    "recipients": ["user_id_1", "user_id_2"]
})
```

---

## Zia Services

```python
zia_service = catalyst_app.zia()

with open("document.png", "rb") as f:
    ocr_result = zia_service.extract_optical_characters(f, {"language": "eng", "model_type": "OCR"})

sentiment = zia_service.get_sentiment_analysis(["I love this!", "Terrible experience."])
entities = zia_service.get_named_entity_recognition(["Zoho Corporation is in Chennai, India."])
keywords = zia_service.get_keyword_extraction(["Catalyst is a serverless platform."])
analytics = zia_service.get_all_text_analytics(["Zoho Catalyst makes development easy."])

with open("image.jpg", "rb") as f:
    moderation = zia_service.moderate_image(f)
    faces = zia_service.detect_faces(f)
    objects = zia_service.recognize_objects(f)

with open("barcode.png", "rb") as f:
    barcode = zia_service.scan_barcode(f)
```

---

## SmartBrowz

```python
smart_browz = catalyst_app.smart_browz()

pdf = smart_browz.convert_to_pdf({
    "url": "https://example.com",
    "pdf_options": {"format": "A4", "print_background": True},
    "navigation_options": {"wait_until": "networkidle0", "timeout": 30000}
})

screenshot = smart_browz.take_screenshot({
    "url": "https://example.com",
    "screenshot_options": {"full_page": True, "type": "png"},
    "navigation_options": {"wait_until": "networkidle2", "timeout": 60000}
})

output = smart_browz.generate_from_template(
    "153000000009001",  # template_id
    template_data={"name": "Alice", "amount": "$100"},
    output_options={"output_type": "pdf"}
)
```

> ⚠️ APM (Application Performance Monitoring) is NOT available for Python functions. Use logs only for Python performance monitoring.

---

## Common Errors

| Error | Cause | Fix |
|-------|-------|-----|
| DataStore methods hang silently in Job functions | `zcatalyst_sdk` Table methods (`get_paged_rows`, `delete_rows`, `insert_rows`, etc.) use `CredentialUser.USER` internally. Job functions have no USER token — every call makes an unauthenticated request, waits 60 s per attempt, raises no exception, and silently burns toward the 15-minute timeout | Initialize with `scope='admin'`: `zcatalyst_sdk.initialize(req=context, scope='admin')` |
