## Overview

QuickML is Catalyst's no-code AutoML platform. Upload a dataset, configure the problem type, train a model, and call it via SDK/API. No ML expertise required.

---

## Workflow

1. **Import Dataset** — CSV/Excel from Stratus, Data Store export, or direct upload
2. **Configure Training** — Select target column, problem type, algorithm
3. **Train** — QuickML runs feature engineering, model selection, cross-validation
4. **Deploy** — Deploy the best model as an endpoint
5. **Predict** — Call predictions from functions or external services

---

## Problem Types

| Type | Use Case | Example |
|------|----------|---------|
| `classification` | Classify into categories | Spam/Not Spam, Churn/No Churn |
| `regression` | Predict numerical values | Price prediction, Sales forecasting |
| `multi_label` | Multiple simultaneous labels | Tag assignment, Multi-category |

---

## SDK — Prediction

```javascript
const quickML = catalystApp.quickML();

// Get a model
const model = quickML.model(MODEL_ID);  // Model ID from console

// Single prediction
const result = await model.predict({
  feature1: 'value1',
  feature2: 42,
  feature3: 'category_a'
});
// { prediction: 'positive', confidence: 0.87 }

// Batch prediction
const batchResult = await model.batchPredict([
  { feature1: 'val1', feature2: 10 },
  { feature1: 'val2', feature2: 20 }
]);
```

---

## REST API

```
# Single prediction
POST /api/v1/ml/models/{model_id}/predict
Authorization: Zoho-oauthtoken {token}
{
  "feature1": "value",
  "feature2": 42
}
```

---

## Pricing

> Canonical pricing lives in `catalyst-pricing/references/pricing-basics.md`. QuickML is billed on tiered inference (per API call) plus LLM token usage — there are no separate training-compute or model-storage line items.

| Resource | Free Tier | Cost |
|----------|-----------|------|
| Model Inference (0–25K calls) | 500 API calls/month | $0.0025 per API call |
| Model Inference (25K–100K calls) | — | $0.002 per API call |
| Model Inference (>100K calls) | — | $0.001 per API call |
| LLM Input Tokens | — | $0.2 per million |
| LLM Output Tokens | — | $0.4 per million |

## Common Errors

| Error | Cause | Fix |
|-------|-------|-----|
| Model training stuck in `PROCESSING` | Dataset too small (< 50 rows) or all rows have the same target value | Add more varied data; QuickML requires at least 50 rows with distribution across classes |
| Prediction returns `null` | Feature columns in prediction request don't match training column names exactly | Match feature names case-sensitively to training dataset headers |
| `Model not deployed` error on predict | Model trained but deployment step skipped | Explicitly deploy model from Console → QuickML → Deploy before calling prediction API |
| Free tier prediction limit hit | 500 predictions/month free tier exhausted | Upgrade plan or wait for next calendar month reset |
